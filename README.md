# Packaging Existing Artifacts
This repository is an example of integrating Conan into your existing build process where the artifacts already exist.

The structure of the example project is as follows -
  - code
    - inc -> contains public APIs (header files) of modules
    - ModuleA -> contains source files for ModuleA
    - ModuleB -> contains source files for ModuleB
  - obj
    - libs -> pre-built static libraries of ModuleA and ModuleB
    - ModuleA -> corresponding object files for source files of ModuleA
    - ModuleB -> corresponding object files for source files of ModuleB
    
The idea is to create a package that has the public header files and the pre-compiled static libraries from this project.

Conan in its current form is heavily geared towards building from source and publishing the artifacts as a result of that build. However, in our case we want Conan to simply slot in to an existing build process and only be responsible for creating a package from existing artifacts and publishing it to a remote for consumption by other projects.

The main scripts of interest are `build.py` and `conanfile.py`. Below are the list of steps they run -
 
  - Execute command `conan package in the same directory as conanfile.py is located
  - Change to the newly created package directory
  - Export and package the libraries and include files
  - Do a Conan seqarch to make sure the package exists
  - Execute command `conan upload Test/0.1@myuser/testing --all -r=artifactory` uploads the package recipe and its artifacts to the remote named `artifactory`

# Changing Configuration of Package
If you are packaging binaries outside a build process it is likely that the binaries will have been built using different settings than the default profile which is used when running `conan package_files Test/0.1@myuser/testing -f`. To change the settings you have two options:

  - Use the -s flag to override specific default settings: `conan package_files Test/0.1@myuser/testing -f -s arch=x86` (if the binaries were built for 32 bit architecture)
  - Use the -pr flag to specify a different conan profile created to match the settings that the binaries were build under `conan package_files Test/0.1@myuser/testing -f -pr profileName`

# Consumption of Uploaded Package
See [conan-package-binary-consume-example](https://github.com/shreyasbharath/conan-package-binary-consume-example) to understand how to extract the contents of the uploaded package to a client project for consumption by the client project's build process.

# Credits
A big thanks to @memsharded and @drodri for their help.
# Changes
The build.py file was modified to support Conan 1.51.3.  Removal of the mypkg directory and handling export and packaging all in one command. My modifications are Linux specific.
# Use
Run python3 build.py in the root directory of this project
