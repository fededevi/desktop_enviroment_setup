### Compile and setup Qt and Qt Creator
  
Install the toolchain as described in the [toolchain_guide.md](toolchain_guide.md) file. 
  
The configuration/build process of Qt will need Perl and Python  
```sudo apt-get install perl python```  
  
Download [Qt 5.9.1 opensource](tools/qt-everywhere-opensource-src-5.9.1.tar.xz) sources from this repository.   
  
Extract the archive and launch configure inside Qt source root:  
```./configure -v -opensource -confirm-license -no-pch -no-xcb -no-opengl -opengl es2 -make libs -device imx6 -prefix /usr/local/qt5 -extprefix $HOME/iMX6B/qt5 -hostprefix /$HOME/qt5_iMX6_toolchain/ -device-option CROSS_COMPILE=/opt/fslc-framebuffer/2.4.1/sysroots/x86_64-fslcsdk-linux/usr/bin/arm-fslc-linux-gnueabi/arm-fslc-linux-gnueabi- -sysroot /opt/fslc-framebuffer/2.4.1/sysroots/armv7at2hf-neon-fslc-linux-gnueabi -nomake examples -no-icu -nomake tests -skip qtwebengine```  

**Note on directories specified in the configuration to the command:**  
**Prefix** : The directory where the qt libraries should be copied in the **target** machine after build  
**Extprefix** : Directory where qt libraries will be installed in the **host** machine (to be copied in the target)  
**Hostprefix** : Directory where the qt tools are placed (QMake and such), those are not required on the target machine  
  
**Note on hardware accelerated graphics:**  
The following options should enable the EGLFS plugin for the OpenGL ES graphical backend ( on top of other things )  
```-no-opengl -opengl es2 -device imx6```  
To make sure the configuration step went fine check if these option are enabled in the output:  
```
EGLFS .................................. yes
EGLFS details:
  EGLFS i.Mx6 .......................... yes
  EGLFS EGLDevice ...................... yes
LinuxFB ................................ yes
```

**Finally to compile  Qt:**  
```
source /opt/fslc-framebuffer/2.4.1/environment-setup-armv7at2hf-neon-fslc-linux-gnueabi
make
make install
```
This will compile and install Qt to the specified directories  


### Setup Qt Creator to use the toolchain
Install QtCreator:  
```sudo apt-get install qtcreator```  

Now Creator need to know where to find the cross compiler for C and C++ and the toolchain for the target we built by compiling Qt. Qt Creator handles this through Kits. Under options create a new kit and set this options:  
  
**Add C++ compiler:**   
```/opt/fslc-framebuffer/2.4.1/sysroots/x86_64-fslcsdk-linux/usr/bin/arm-fslc-linux-gnueabi/arm-fslc-linux-gnueabi-g++```  
**Add C compiler:**   
```/opt/fslc-framebuffer/2.4.1/sysroots/x86_64-fslcsdk-linux/usr/bin/arm-fslc-linux-gnueabi/arm-fslc-linux-gnueabi-gcc```  
**Add Qt version** ( hostprefix/extprefix directories used in qt configure )  
```/home/<user>/qt5_iMX6_toolchain/bin/qmake```  
  
Qt Creator should automatically use the correct compiler/linker flags for the target.  
  
Additionally it is possible to provide the kits with debuggers and **devices** to handle auto-installation with ssh/sftp of compiled binaries.  
Now Qt Creator should be able to compile qt projects for the target platform.  

### Install qt libraries on the platform
This step should not be necessary because the target should already have Qt libraries installed but if for any reason those needs to be updated just copy ```$HOME/iMX6B/qt5``` from the host to the target: ```/usr/local/``` (prefix to extprefix)  














