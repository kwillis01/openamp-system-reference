# Platform Specifics for Baremetal R5 on SK-AM64

These files are needed to use OpenAMP with the baremetal R5 on the SK-AM64. With 
OpenAMP cloned, this folder should be put in the apps/machine/ folder in OpenAMP. 
The ti_k3_r5.cmake file should be placed in the cmake/platforms/ folder in OpenAMP. 
CMakeLists.txt in OpenAMP should have the line
```
project (open_amp C)
```
changed to 
```
project (open_amp C ASM)
```

## Build OpenAMP
Once OpenAMP is cloned and libmetal is built, OpenAMP can be build by following
the code snippet below. If libmetal and OpenAMP DONT have the same parent directory 
or the libmetal folder is named something other than libmetal, use the DLIBMETAL_PATH 
varbiable to define where the libmetal directory is.
```
$ mkdir -p build-openamp
$ cd build-openamp
$ cmake .. -DCMAKE_TOOLCHAIN_FILE=../cmake/platforms/ti_k3_r5.cmake -DWITH_APPS=ON \
[-DLIBMETAL_PATH=path/to/libmetal]
$ make VERBOSE=1 DESTDIR=$(pwd) install
```

## Run the Matrix Multiply example on the remote R5
The host core should be running a kernel that is 6.1 or higher. In 
build-openamp/apps/examples/matrix_multiply, copy matrix_multiplyd.out to the remote R5. 
In the openamp-system-reference repository, copy
the examples/linux/rpmsg-mat-mul and examples/linux/common folders to 
the host processor. 

Find the R5_1 core 0 by searching dmesg. Then run
```
$ sudo cp matrix_multiplyd.out /lib/firmware/
$ sudo echo matrix_multiplyd.out > /sys/class/remoteproc/remoteproc<rproc_number>/firmware
$ sudo echo start > /sys/class/remoteproc/remoteproc<rproc_number>/state
$ sudo cat /sys/kernel/debug/remoteproc/remoteproc<rproc_number>/trace0
```

From the host processor run
```
$ cd rpmsg-mat-mul
$ make
$ ./mat_mul_demo
```
