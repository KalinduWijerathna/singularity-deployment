# <h1>Singularity Deployment Instructions</h1>

# <h2>How Singularity Container works on Kepler/Turing/Aiken server?</h2>

For eg: Let's Consider Kepler\
Here's an illustration of Singularity containers for multiple students, each with their preferred OS, software, and packages, on Kepler:
```
    +----------------------------------+
    |               Kepler             |
    |    High Performance GPU Server   |
    |          OS: Ubuntu Server       |
    +----------------------------------+
               |                   |
               |                   |
+--------------|-------------------|--------------+
|              |                   |              |
|   Student_1  |     Student_2     |   Student_3  |
|    eyy001    |      eyy002       |    eyy003    |
|              |                   |              |
+--------------|-------------------|--------------+
               |                   |
               |                   |
               V                   V
        +----------------------------------+
        |       Singularity Runtime        |
        |                                  |
        | +--------------------------+     |
        | |                          |     |
        | | Singularity Container    |     |
        | | for eyy001               |     |
        | | - Preferred OS (Ubuntu)  |     |
        | | - Preferred languages    |     |
        | | - Preferred packages     |     |
        | | - Preferred software     |     |
        | +--------------------------+     |
        |                                  |
        | +--------------------------+     |
        | |                          |     |
        | | Singularity Container    |     |
        | | for eyy002               |     |
        | | eg: {                    |     |
        | | - OS: CentOS             |     |
        | | - Languages: R           |     |
        | | - GCC, Make              |     |
        | | - Data Analysis Tools }  |     |
        | +--------------------------+     |
        |                                  |
        | +--------------------------+     |
        | |                          |     |
        | | Singularity Container    |     |
        | | for eyy003               |     |
        | | eg: {                    |     |
        | | - OS: Fedora             |     |
        | | - Languages: Java        |     |
        | | - Apache Spark           |     |
        | | - Hadoop              }  |     |
        | +--------------------------+     |
        |                                  |
        +----------------------------------+
                                               

```
- Each Student can build <b>only 1</b> singularity container <b>for now</b> in their user account in Kepler.
- Each Student can install their <b>preferred OS(Ubuntu,CentOS...etc.), preferrd laguages(C++,Java,R,Python...etc.), prefered libraries, packages, and softwares</b> inside their container.


# <h2>Singularity Quick-Start for New User</h2>
The follwoing instructions are focused on installing in Kepler.The process is exactly the same for Aiken and Turing as well.

1. Login to the kepler GPU server with your username and password.(The same credentials as for aiken or tesla).

    ```$ ssh eyyxxx@kepler.ce.pdn.ac.lk ```\
   server public documentation: https://faq.ce.pdn.ac.lk/network-n-servers/kepler/

2. A sample singularity container definition file(Already comes with python3, pip3 and pytorch) is available in this repository as /base.def  

3. Download the sample def file.<br />
 ```eyyxxx@kepler:~/path/to/your/dir$ wget https://raw.githubusercontent.com/cepdnaclk/singularity-deployment/main/base.def```

4. Build a Sandbox version to test and install necessary packages/dependencies.\
    ```eyyxxx@kepler:~$ cd path/to/your/dir```\
    ```eyyxxx@kepler:~/path/to/your/dir$ singularity build --sandbox --fakeroot base base.def ```

5. To enter the singularity container\
    ```eyyxxx@kepler:~/path/to/your/dir$ singularity shell --writable --fakeroot base```

6. Check whether new packages can be installed with apt package manager to the new container.
   For example:\
    ```Singularity> apt-get install neofetch```\
    -<b>Always make sure to check the current version of CUDA when neccessary.(command: nvidia-smi)</b>

7. Add the commands from step 6 to the def file under the %post blob so that the final build will have the packages you tested above.

8. Assign the Server's GPUs to your Container\
    ```eyyxxx@kepler:~/path/to/your/dir$export NVIDIA_VISIBLE_DEVICES=1,2```

9. Build the container\
    ```eyyxxx@kepler:~/path/to/your/dir$ singularity build --fakeroot base.sif base.def```
   
10. Run commands using the container\
    i. If Running Without GPUs (General Command)\
    ```eyyxxx@kepler:~/path/to/your/dir$  singularity exec base.sif <your-command>```\
        eg: Running Python CLI\
            ```
                eyyxxx@kepler:~/path/to/your/dir$  singularity exec base.sif python3
                Python 3.10.12 (main, Month dd yyyy, hh:mm:ss) [GCC 11.4.0] on linux
                Type "help", "copyright", "credits" or "license" for more information.
                >>> print("Hello, World")
                Hello, World
            ```

    ii. If Running With GPUs\
    ```eyyxxx@kepler:~/path/to/your/dir$  singularity exec --contain --nv base.sif  <your-command>```\
         eg: Running Python CLI with pytorch\
            ```
                eyyxxx@kepler:~/path/to/your/dir$  singularity exec base.sif python3
                Python 3.10.12 (main, Month dd yyyy, hh:mm:ss) [GCC 11.4.0] on linux
                Type "help", "copyright", "credits" or "license" for more information.
                >>> import torch
                >>> torch.cuda.device_count()
                1
            ```    


<b>Singularity Official Documentation</b> https://apptainer.org/user-docs/master/ \
<b>More on creating custom def files</b> https://docs.sylabs.io/guides/latest/user-guide/definition_files.html \
<b>More on Bind-mount of singularity.conf(for server admin's reference)</b> https://apptainer.org/docs/admin/main/configfiles.html#bind-mount-management 

# <h2>Why Use Singularity Containers?</h2>

- High Performance: Provide Near Native Performance
- User Privilages: Allows non-root/sudo users to create, run, and manage software and package inside their container
- Enterprise and Research Use: Serves both enterprise and research needs, bridging the gap between development, testing, and production environments
- Easy Conversion: Enables easy conversion of popular Docker images
- Container Mobility: Can be moved across different systems, allowing researchers to work on their projects without being tied to a specific machine
- Support for MPI and GPUs: Supports Multi-Process Communication (MPI) and GPU acceleration, critical for parallel and GPU-intensive computing tasks
  - Machine Learning
  - AI
  - Deep Learning
- Security Isolation: Preventing applications from affecting the host system and other containers, enhancing security

