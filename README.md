
# Singularity Quick-Start for New User

1. Login to the kepler GPU server with your username and password.(The same credentials as for aiken or tesla).\

    ```$ ssh eyyxxx@kepler.ce.pdn.ac.lk ```

2. A sample singularity container definition file(Already comes with python3, pip3 and pytorch) is available at /srv/singularity/base.def  

3. Copy it to your preferred (home) directory.\
    ```$ cp /srv/singularity/base.def path/to/your/dir/base.def```

4. Build a Sandbox version to test and install necessary packages/dependencies.\
    ```$ cd path/to/your/dir```\
    ```$ singularity build --sandbox --fakeroot base base.def ```

5. To enter the singularity container\
    ```$ singularity shell --writable --fakeroot base```

6. Check whether new packages can be installed with apt package manager to the new container.For example:\
    ```$ apt-get install neofetch```

7. Add the commands from step 6 to the def file under the %post blob so that the final build will have the packages you tested above.

8. Build the container\
    ```$ singularity build --fakeroot base.sif base.def```

9. Run commands using the container\
    ```$ singularity exec base.sif <your-command>```


