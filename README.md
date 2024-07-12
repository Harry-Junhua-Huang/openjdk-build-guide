---
layout: home
title: Home
---



# Building OpenJDK 11u on Ubuntu 22.04 Using Visual Studio Code

Hi everyone! I'm a junior computer science student, and I recently set up my local environment for building OpenJDK 11u on Ubuntu 22.04 using Visual Studio Code. I faced several issues along the way, so I wanted to share my experience to help anyone who might encounter similar problems. This guide is based on the official [OpenJDK build documentation](https://github.com/openjdk/jdk11u/blob/master/doc/building.md), with some enhancements and formatting improvements courtesy of ChatGPT.

## Prerequisites

### 1. Install Git

First, ensure you have `git` installed:

```bash
sudo apt-get install git
```

This command installs `git`, which is required to clone the OpenJDK repository.

### 2. Clone the OpenJDK 11u Repository

Clone the OpenJDK 11u repository using the following commands:

```bash
git clone https://github.com/openjdk/jdk11u.git
cd jdk11u
```

This will download the source code for OpenJDK 11u into your local machine.

### 3. Install Required Packages

Install the necessary build tools:

```bash
sudo apt-get install build-essential autoconf
```

`build-essential` provides the necessary tools for building software, and `autoconf` is used for generating configuration scripts.

### 4. Boot JDK Requirements

Building the JDK requires a pre-existing JDK, known as the "boot JDK". The general rule is to use the previous major version of the JDK as the boot JDK (N-1 rule). However, an up-to-date build of the current JDK source is also acceptable.

### 5. Install JDK 11 as Boot JDK

You can install JDK 11 using `apt`:

```bash
sudo apt-get update
sudo apt-get install openjdk-11-jdk
```

Set the `JAVA_HOME` environment variable and update the `PATH`:

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

Verify the JDK installation:

```bash
java -version
javac -version
```

You should see output similar to this:

```
openjdk version "11.0.23" 2024-04-16
OpenJDK Runtime Environment (build 11.0.23+9-post-Ubuntu-1ubuntu122.04.1)
OpenJDK 64-Bit Server VM (build 11.0.23+9-post-Ubuntu-1ubuntu122.04.1, mixed mode, sharing)
```

### 6. Install External Library Requirements

Install the required libraries:

```bash
sudo apt-get install libfreetype6-dev libcups2-dev libx11-dev libxext-dev libxrender-dev libxtst-dev libxt-dev libasound2-dev libffi-dev
```

These libraries are necessary for building OpenJDK.

### 7. Run Configure

Run the configure script to set up the build environment:

```bash
bash configure --with-boot-jdk=$JAVA_HOME
```

You should see a message indicating a successful configuration:

```
A new configuration has been successfully created in /home/harry54480/projects/jdk11u/build/linux-x86_64-normal-server-release using default settings.
```

### 8. Build the JDK

Compile the source code and create the JDK images:

```bash
make images
```

Verify the newly built JDK:

```bash
./build/*/images/jdk/bin/java -version
```

You should see output similar to this:

```
openjdk version "11.0.24-internal" 2024-07-16
OpenJDK Runtime Environment (build 11.0.24-internal+0-adhoc.harry54480.jdk11u)
OpenJDK 64-Bit Server VM (build 11.0.24-internal+0-adhoc.harry54480.jdk11u, mixed mode)
```

### 9. Running Tests

To run the tests, you need `jtreg`. Here’s how to install and configure `jtreg` in an isolated directory:

#### Download jtreg

Download the latest `jtreg` version from [Adoptium](https://ci.adoptium.net/job/dependency_pipeline/lastSuccessfulBuild/artifact/jtreg/jtreg-7.4+1.tar.gz):

```bash
wget https://ci.adoptium.net/job/dependency_pipeline/lastSuccessfulBuild/artifact/jtreg/jtreg-7.4+1.tar.gz
```

#### Create an Isolated Directory for jtreg

Create a directory where you want to install `jtreg`:

```bash
mkdir -p ~/projects/jtreg
```

#### Extract jtreg to the Isolated Directory

Extract the downloaded `jtreg` tarball to the isolated directory:

```bash
tar -xvf jtreg-7.4+1.tar.gz -C ~/projects/jtreg --strip-components=1
```

#### Set the JT_HOME Environment Variable

Set the `JT_HOME` environment variable to point to the isolated `jtreg` directory:

```bash
export JT_HOME=~/projects/jtreg
```

#### Reconfigure the JDK Build with jtreg

Reconfigure the JDK build to use the isolated `jtreg` directory:

```bash
cd ~/projects/jdk11u
bash configure --with-boot-jdk=$JAVA_HOME --with-jtreg=$JT_HOME
```

### 10. Run the Tests

Run the tests using `jtreg`:

```bash
make test-tier1
```

You should see output similar to this:

```
==============================
Test summary
==============================
   TEST                                              TOTAL  PASS  FAIL ERROR   
   jtreg:test/hotspot/jtreg:tier1                     1495  1495     0     0   
   jtreg:test/jdk:tier1                               1896  1896     0     0   
   jtreg:test/langtools:tier1                         3938  3938     0     0   
   jtreg:test/nashorn:tier1                              0     0     0     0   
   jtreg:test/jaxp:tier1                                 0     0     0     0   
==============================
TEST SUCCESS
```

### Additional Notes

For some versions of the JDK, like JDK 17, you might also need to set up the Google Test framework. If you need to do this, follow these steps:

#### Step-by-Step Guide to Include Google Test in the Build

Download the Google Test framework version 1.8.1:

```bash
git clone -b release-1.8.1 https://github.com/google/googletest /home/harry54480/projects/googletest
```

Specify the path to the Google Test framework when running the configure script for your JDK build:

```bash
cd ~/projects/jdk17u
bash configure --with-boot-jdk=$JAVA_HOME --with-jtreg=$JT_HOME --with-gtest=/home/harry54480/projects/googletest
```

Re-run the build:

```bash
make images
```

Run the tests:

```bash
make test-tier1
```

### Summary

By following these steps, you can set up your environment, build OpenJDK 11u, and run tests using `jtreg` on Ubuntu 22.04. This guide covers installing necessary packages, setting up the boot JDK, running the configure script, building the JDK, and running tests. If you encounter any further issues, please let me know for more specific guidance. Happy coding!

**Citation**: This guide is based on the official [OpenJDK build documentation](https://github.com/openjdk/jdk11u/blob/master/doc/building.md). Special thanks to ChatGPT for helping to reformat and improve the writing of this guide.