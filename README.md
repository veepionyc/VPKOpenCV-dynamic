# VPKOpenCV

Stripped-down dynamic framework build of OpenCV 3.4.13 whose only purpose is to run a fully-functioning `contrib/tracking` module. All extraneous dependencies have been stripped out.

**Architectures**  
arm64-iPhoneOS  
x86_64-iPhoneSimulator   

**Modules**   
core  
imgproc  
tracking  
plot
video  
world  


**Build**  
3.4.13 with `tracking` and `plot` modules added from `contrib`
 
`
python ./platforms/ios/build_framework.py --without calib3d --without cudaarithm --without cudabgsegm --without cudacodec --without cudafeatures2d --without cudafilters --without cudaimgproc --without cudalegacy --without cudaobjdetect --without cudaoptflow --without cudastereo --without cudawarping --without cudev --without dnn --without features2d --without flann --without highgui --without imgcodecs --without java --without js --without ml --without objdetect --without photo --without python --without shape --without stitching --without superres --without tracking --without ts --without videoio --without videostab --without viz --iphoneos_archs arm64 --iphonesimulator_archs x86_64  --contrib  ../opencv_contrib  build
`

The `contrib` repo has all modules _removed_ except `tracking` and `plot`
