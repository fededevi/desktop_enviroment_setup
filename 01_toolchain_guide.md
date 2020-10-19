### The linux-x86_64 to linux-armv7at2hf toolchain
  
The 'arm' toolchain for the ctrl_com can be downloaded  [here](tool/fslc-framebuffer-glibc-x86_64-fsl-image-gui-armv7at2hf-neon-toolchain-2.4.1.sh).
  
The toolchain is a collection of tool and libraries  to cross-compile from an **host** to a **target**. The provided toolchain contains the compiler, linker and libraries to compile applications for the ctrl_com board from a linux host.  
  
To install the toolchain just run the self extracting shell script provided above. The toolchain should automatically install under:  
```/opt/fslc-framebuffer/2.4.1```  
  
The toolchain includes an enviroment script which contains variable definitions pointing to the correct compilers/flags inside the toolchain to compile stuff for the board. The enviroment can be loaded by calling:  
```source /opt/fslc-framebuffer/2.4.1/environment-setup-armv7at2hf-neon-fslc-linux-gnueabi```  
  
Assuming a makefile is configured to use the defined variables ( $CPP, $CXX, $CC & relative flags) then calling
```make <whatever>```  
will compile the project for the target platform. 
  
### Setting up an IDE to use the toolchain
Setting up IDEs should be relatively easy but depending on the project you may want to setup the enviroment in different ways:  
- IDEs can be configured to just call **make** on the project makefiles after loading the enviroment like above
- IDEs can be configured to setup the toolchain enviroment (like above) and then use the defined CXX, CPP, etc.. variables to call compilers and linker with the correct flags.
- IDEs can be configured to just ignore the enviroment script and call the compiler/linker directly but this usually means that additional flags for compilers/linker must be provided manually to the IDE.

If you are unsure about the executables to call and the flags to use to compile just look inside the enviroment file.
