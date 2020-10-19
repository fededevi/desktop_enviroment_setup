### Enable GPU Acceleration on IMX6 target
**Tested on target with kernel 4.9.11 - imx6**  
  
In order to enable GPU acceleration on the CTRL_COM board for Qt projects (and other OpenGL apps) a couple of requirements need to be met:  
  
- The installed Qt libraries installed in the target have to include the EGLF plugin. The plugin file should be present here:    
```qt5/plugins/platforms/libqeglfs.so```  
- The installed Qt libraries should have the Vivante EGLFS device integration plugin, the file should be in:   ```qt5/plugins/egldeviceintegrations/libqeglfs-viv-integration.so```  
- The target should include the OpenGL ES libraries (```libGLESv2.so``` & co.), in the current rootFS those are already   present under  ```/usr/lib```  
- The target kernel should include the **galcore** module; this module is used by the OpenGL ES implementation to access the Vivante GPU included in the IMX6 Modules  
  
The current rootFS/kernel of the CTRL_COM have a Qt version which lacks the EGLFS/Vivante plugins and lacks the galcore module in the kernel. In order to use the integrated GPU a new version of Qt and the galcore kernel module must be compiled, see how to compile Qt with imx6 GPU support [**here**](ctrl_com_desktop_env_setup.md). 
Once built, The Qt libraries need to be copied on the target at ```/usr/local/qt```  
  
### Build and install galcore module  
  
The **galcore** kernel module can either be included in the kernel itself by enabling the configuration option:  
```Device Drivers > MXC support drivers > MXC Vivante GPU support > MXC Vivante GPU support``` **(not tested)**  
or by compiling the module externally.  
To build the module separately we still need the kernel and module sources.  
The kernel can be obtained from gitlab [**repository**](http://192.168.23.28/hyc_team/linux-imx).  
The module sources with additional fixes can be obtained from the repository at (to be moved to gitlab or included in kernel sources) [**here**](https://github.com/fededevi/kernel-module-imx-gpu-viv), more recent versions are not compatible with older Linux kernels. The module sources are also included in the imx kernel but are a older version.

Assuming the module is under ```/module``` and the kernel is under ```/kernel```  
From ```/kernel```  
```
make defconfig 
make prepare
make scripts
```
From ```/module```, assuming the toolchain is installed in that directory..  
```
export KERNEL_SRC=/kernel
source /opt/fslc-framebuffer/2.4.1/environment-setup-armv7at2hf-neon-fslc-linux-gnueabi 
make
```

If everything goes well the ```galcore.ko``` module should be found in ```/module/kernel-module-imx-gpu-viv-src``` and can be copied on target to  ```/lib/modules/4.9.11-mx6/kernel/galcore```  
To dinamically load the kernel on the target machine issue:    
```
depmod               //to reload themodules list  
modprobe galcore     //to load galcore  
```
To check that the module is working correctly:  
```
cat /sys/bus/platform/drivers/galcore/module/initstate  
```
if the driver is working correctly it should print ```live```

#### Workaround to framebuffer size limitations    

**Problem:**  
- Qt applications fail to create a OpenGL device with error 0x303  

**Causes:**  
- The GPU used in imx6 devices can only render to framebuffers which  size is a multiple of 16.
- The EGLFS QT forces the root window to be fullscreen
- The current screen and fb size on the hyc is 1366*768
- 1366 is not a multiple of 16

**Solution:**
- Set the virtual X framebuffer size to a multiple of 16, the nearest resolution which will cover the whole framebuffer is 1376x768, to do this:
```
fbset -fb /dev/fb0  -vxres 1376 
```



