# Multiarch images for python and node runtimes

---

## About
---

The Dockerfile provided creates a flexible development environment for the Cloudisense project, supporting multiple Python versions and leveraging Cython for performance optimization.


## Key Features:

* **Multi-Python Support:** The Dockerfile utilizes pyenv to manage and install various Python versions as specified in the `TARGET_VERSIONS` variable.

* **Cython Integration:** Cython is installed in each Python virtual environment to optimize Python code for performance gains.

* **Dedicated Builder Environment:** A separate virtual environment (BUILDER_VENV) is created, equipped with essential tools like boto3, Gitpython, and Cython for the build process.



## Environment variables
---

**build.env**

```sh
    TARGET_VERSIONS=3.7.17,3.8.20,3.9.20,3.10.15,3.11.10
    BUILD_FOLDER_NAME_PREFIX=buildsystem_
    BUILDER_VENV=builder
    VENV_PARENT_FOLDER=virtualenvs
    VENV_PATH=

```

**Variables**

* `TARGET_VERSIONS`: A comma-separated list of target Python versions.

* `BUILD_FOLDER_NAME_PREFIX`: Prefix used for virtual environment folders.

* `BUILDER_VENV`: Name of the builder virtual environment.

* `VENV_PARENT_FOLDER`: Parent directory for virtual environments.

* `VENV_PATH`: Absolute path to the virtual environment parent directory.


## Building builder image (x86 only)
---

The builder image is an image containing multiple Python versions and pre-created virtual environments. The build script will use these environments to generate builds that support all these Python versions.


1. **Define Target Python Versions:** Create a `build.env` file and specify the desired Python versions:

```sh
# Python versiosn to include and create virtual environments for   
TARGET_VERSIONS=3.7.17,3.8.20,3.9.20,3.10.15,3.11.10
```

> The current builder docker image supports targettig these versions for obfuscation:
`3.7.17, 3.8.20, 3.9.20, 3.10.15, 3.11.10`, as these versions are installed on the image.

2. **Build the Image:**


**x86 Architecture**

```sh

sudo docker buildx build --build-arg TARGET_VERSIONS=$(cat build.env | grep TARGET_VERSIONS | cut -d'=' -f2) --build-arg BUILD_FOLDER_NAME_PREFIX=$(cat build.env | grep BUILD_FOLDER_NAME_PREFIX | cut -d'=' -f2) --build-arg BUILDER_VENV=$(cat build.env | grep BUILDER_VENV | cut -d'=' -f2) -t rajdeeprath/cloudisense-builder-x86:0.0.2 .

```
> Get image from  https://hub.docker.com/repository/docker/rajdeeprath/cloudisense-builder-x86



## Running the container


1. **x86 Architecture**

```sh

sudo docker run -it --rm -v /home/rajdeeprath/Documents/GitHub/cloudisense-builder:/builder rajdeeprath/cloudisense-builder-x86:0.0.2

```

> Get image from  https://hub.docker.com/repository/docker/rajdeeprath/cloudisense-builder-x86

or


2. **aarch64 Architecture**

**Step 1**: Enable QEMU for ARM Emulation

```sh
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

**Step 2**: Pull the ARM64 Image with the Correct Platform

```sh
docker pull --platform linux/arm64 rajdeeprath/cloudisense-builder-arm:0.0.2
```

**Step 3**: Run container 

```sh

sudo docker run -it --rm --platform linux/arm64 rajdeeprath/cloudisense-builder-arm:0.0.2

```

> From : https://hub.docker.com/repository/docker/rajdeeprath/cloudisense-builder-arm

This command will start a docker container with the `rajdeeprath/cloudisense-builder` image in `linux/arm64` emulation mode. 

> if you already have a image built locally or in dockerhub, this step will be faster


## On Docker hub


* [x86 Build Repository](https://hub.docker.com/repository/docker/rajdeeprath/cloudisense-builder-arm/general)


* [Arm64 Build Repository](https://hub.docker.com/repository/docker/rajdeeprath/cloudisense-builder-x86/general)