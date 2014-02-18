---
layout: default
title: Installation
tagline: Install Ilastik
category: "Installation"
---

# Install CPLEX

To use the *Automatic Tracking* Workflow, it is required to install the commercial solver IBM CPLEX. 

**Note that ilastik will run even without IBM CPLEX installed, it is only a requirement for the [Automatic Tracking]({{baseurl}}/documentation/tracking/tracking.html) workflow.
To enable additional functionality, it is also recommended for the [Density Counting]({{baseurl}}/documentation/counting/counting.html) workflow.**

## Application for Academic License at IBM

IBM CPLEX is a commercial solver which is free for academic use.
To apply for an academic license, the user first needs to apply for an 
academic membership at IBM. Details may be found on
[the IBM Academic Initiative website](http://www-03.ibm.com/ibm/university/academic/pub/page/membership).
Please note that it might take some days until the application gets approved by IBM.

## Download IBM CPLEX

After the academic membership has been approved, the user can download IBM CPLEX. To do so, 
the steps on [this IBM website](http://www-03.ibm.com/ibm/university/academic/pub/jsps/assetredirector.jsp?asset_id=1070)
may be followed. 
The search result in the IBM software catalogue should look similar to this:
<div align="center"><img src="./fig/ibm_search_result.jpg"></div>

We recommend to use *Http transfer*, then clicking on the CPLEX instance in *Industry Solutions* should open
a list of CPLEX versions available for different platforms:
<div align="center"><img src="./fig/cplex_result.jpg"></div>

The current version of ilastik works with 
**IBM ILOG CPLEX Optimization Studio V12.5.1**.
After choosing the appropriate platform, the user has to agree with the IBM license. 
Finally, CPLEX may be downloaded and is ready to install.

**Important note: It is not sufficient to download the Trial version of CPLEX since its solver can only handle
very small problem sizes. Please make sure, the correct version is downloaded as described here.**


##Windows

On Windows, there are typically no further modifications needed after installing CPLEX. 
After successful installation, the *Automatic Tracking Workflow* is displayed on the Start-Screen of ilastik.

If this workflow is not present, something went wrong with the CPLEX installation. As a workaround,
the user may copy the files `libcplex.dll`, `libilocplex.dll`, 
and `libconcert.dll` from the CPLEX installation directory into the library folder of ilastik, usually
located at `C:/Program Files/ilastik/lib`.



##Linux

Unfortunately, CPLEX packages do not provide shared versions of all required libraries, but only 
static variants. The user has to navigate to the installation folder of CPLEX and therein to the 
static libraries `libcplex.a`, `libilocplex.a`, and `libconcert.a` (usually located in 
`ILOG/CPLEX_Studio125/cplex/lib/x86-64_sles10_4.1/static_pic` and `ILOG/CPLEX_Studio125/concert/lib/x86-64_sles10_4.1/static_pic`)
to run the following commands:

    g++ -fpic -shared -Wl,-whole-archive libcplex.a -Wl,-no-whole-archive -o libcplex.so
    g++ -fpic -shared -Wl,-whole-archive libilocplex.a -Wl,-no-whole-archive -o libilocplex.so
    g++ -fpic -shared -Wl,-whole-archive libconcert.a -Wl,-no-whole-archive -o libconcert.so

These commands will link shared CPLEX libraries from the static versions.

Finally, these shared libraries need to be copied into the library folder of the ilastik installation,`ilastik/lib`. 

After successful installation, the *Automatic Tracking Workflow* is displayed on the Start-Screen of ilastik.


##Mac

Similar to Linux, CPLEX packages for Mac do not provide shared versions of all required libraries, but only static variants.

There are different ways to create shared libraries for CPLEX on Mac OSX depending on the operating system version and compiler type.
In all cases, you need a compiler installed. 

You can check whether you already have a compiler installed by running the following command in a terminal (open the Terminal app!).
    
    gcc --version

If no compiler is installed, choose what to do depending on your OSX version:
- For all OSX < 10.9, so up to Mountain Lion, you need to install XCode from the AppStore. Then you need to go to XCode's Preferences, to the Downloads tab, and install the command line tools.
- For OSX 10.9 Mavericks it suffices to install the command line tools using the following command without installing XCode.

      xcode-select --install

  Then you need to accept the XCode licence by running "sudo gcc" once.


Now we have the preliminaries to create the shared libraries, we can move on to the next step. This depends on the compiler type and operating system version. To find your compiler version, run

    gcc --version

and look at the output. This should be either `GCC 4.2.1`, or `Apple LLVM (clang)`. The folders used to specify the location of the libraries in cases involving `clang` depend on whether you installed from `cplex_studio1251.macos.bin` or `cplex_studio1251.osx.bin`. If you installed the first one, you need to replace `x86-64_osx` by `x86-64_darwin`.


- If the compiler is `GCC 4.2.1`, use the commands below (in the respective folders where the files are located)

      g++ -fpic -shared -Wl,-all_load libcplex.a -Wl,-noall_load -o libcplex.dylib
      g++ -fpic -shared -Wl,-all_load libilocplex.a -Wl,-noall_load -o libilocplex.dylib
      g++ -fpic -shared -Wl,-all_load libconcert.a -Wl,-noall_load -o libconcert.dylib

- If the compiler is `clang` and you have OSX version < 10.9, then you need to supply the symbols of `cplex` and `concert` when building `libilocplex.dylib`:

      g++ -fpic -shared -Wl,-all_load libcplex.a -Wl,-noall_load -o libcplex.dylib
      g++ -fpic -shared -Wl,-all_load libconcert.a -Wl,-noall_load -o libconcert.dylib
      g++ -fpic -shared -Wl,-all_load libilocplex.a -Wl,-noall_load -L. -L../../../../concert/lib/x86-64_osx/static_pic -lcplex -lconcert -o libilocplex.dylib


- If the compiler is `clang` and you have OSX 10.9 Mavericks, then you need to specify the correct C++ standard library to use in addition to the commands above:

      g++ -fpic -shared -Wl,-all_load libcplex.a -Wl,-noall_load -stdlib=libstdc++ -o libcplex.dylib
      g++ -fpic -shared -Wl,-all_load libconcert.a -Wl,-noall_load -stdlib=libstdc++ -o libconcert.dylib
      g++ -fpic -shared -Wl,-all_load libilocplex.a -Wl,-noall_load -L. -L../../../../concert/lib/x86-64_osx/static_pic -lcplex -lconcert -stdlib=libstdc++ -o libilocplex.dylib
      install_name_tool -id @executable_path/../Frameworks/libcplex.dylib libcplex.dylib
      install_name_tool -id @executable_path/../Frameworks/libconcert.dylib libconcert.dylib
      install_name_tool -id @executable_path/../Frameworks/libilocplex.dylib libilocplex.dylib
      install_name_tool -change libcplex.dylib @executable_path/../Frameworks/libcplex.dylib libilocplex.dylib
      install_name_tool -change libconcert.dylib @executable_path/../Frameworks/libconcert.dylib libilocplex.dylib

Now copy those files to your ilastik installation into the lib folder. On Mac this works by right-clicking on _ilastik.app_, choose _Show package contents_, and place the three libraries in _Contents/Frameworks_.

After a successful installation, the *Automatic Tracking Workflow* will appear on the Start-Screen of ilastik.

