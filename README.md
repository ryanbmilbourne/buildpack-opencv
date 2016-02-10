# Heroku buildpack for OpenCV

This buildpack provides an OpenCV install that layers on top of the standard Node.js buildpack.

The plan for making this buildpack is to install OpenCV in a Heroku one-off shell, pack it into
an archive stored on S3, and then in the buildpack scripts, unpack it into /app/.heroku/opencv.

For now this is a bare-bones OpenCV 3.1.0 build that is just whatever it was able to compile without
downloading any dependencies.  
