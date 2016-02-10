# Heroku buildpack for OpenCV

Steve Marschner, February 2016

This buildpack provides a basic installation of OpenCV that can be used to compile C++ code 
in later buildpacks and run it in the application.  In particular, it is designed to support
C++ addons compiled by the standard Node.js buildpack.

The buildpack works by unpacking an image of OpenCV that is stored on S3, and setting up the 
required environment variables for it to be used by later buildpacks and at run time.

## Creating the OpenCV image

The image is created by building OpenCV from source in a Heroku one-off shell running in an 
app that has the Python buildpack installed (to support `awscli`).  Then it is tarred up and
uploaded to S3.  The following commands are what I used:

```
pip install awscli
aws configure
wget https://github.com/Itseez/opencv/archive/2.4.11.zip
wget https://cmake.org/files/v3.5/cmake-3.5.0-rc1-Linux-x86_64.tar.gz
unzip 2.4.11.zip && rm 2.4.11.zip
mkdir .heroku/cmake
tar zxf cmake-3.5.0-rc1-Linux-x86_64.tar.gz && rm cmake-3.5.0-rc1-Linux-x86_64.tar.gz
mv cmake-3.5.0-rc1-Linux-x86_64/bin .heroku/cmake
mv cmake-3.5.0-rc1-Linux-x86_64/share .heroku/cmake
cd opencv-2.4.11/
../.heroku/cmake/bin/cmake -DCMAKE_INSTALL_PREFIX=/app/.heroku/opencv .
make
make install
cd ..
tar zcf opencv.env.tgz .heroku/opencv .heroku/cmake
aws s3 cp opencv.env.tgz s3://test5a9c0284/opencv.env.tgz
```

Then I set that file to be public via the S3 console, since the buildpack just downloads it without
any credentials.

For now this is a bare-bones OpenCV 3.1.0 build that is just whatever it was able to compile without
downloading any dependencies.  

## TODO

I would also like to be able to install this buildpack after the standard Python buildpack, so that 
the OpenCV Python modules are installed for use in Python code.  I believe this will require working
out what OpenCV installs into the Python directories and including that in the package.

The image should be stored in a project-owned S3 bucket and downloaded using appropriate credentials
rather than an anonymous download.