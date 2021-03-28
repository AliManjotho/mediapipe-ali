# mediapipe-ali

1) Prerequisites

   sudo apt install python3-pip
   pip install numpy
   sudo apt install git
   
   sudo apt-get install python-is-python3
   
2) Install Android Studio (Snapcraft Store)

   sudo snap install android-studio --classic
   
   sudo apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virt-manager
   
   Install Android Platform 28 & 29
   Install Android NDK r21e
   
   
3) Install Bazel

   sudo apt install apt-transport-https curl gnupg
   curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor > bazel.gpg
   sudo mv bazel.gpg /etc/apt/trusted.gpg.d/
   echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list

   sudo apt update && sudo apt install bazel

   sudo apt update && sudo apt full-upgrade

4) Run bazel

   bazel version
   
   
5) Install JDK

   sudo apt install openjdk-11-jdk



   
   
   
6) Checkout MediaPipe repository

   git clone https://github.com/google/mediapipe.git
   cd mediapipe
   
   
7) Install OpenCV

   sudo apt-get install libopencv-core-dev libopencv-highgui-dev libopencv-calib3d-dev libopencv-features2d-dev libopencv-imgproc-dev libopencv-video-dev
   
   

   
   
8) Setup environmental variables (add these lines to ~/.bashrc  file)

   export ANDROID_HOME=$HOME/Android/Sdk
   export ANDROID_NDK_HOME=$HOME/Android/Sdk/android-ndk
   export PATH=$PATH:$ANDROID_HOME/platform-tools
   
   
   
9) Change WORKSPACE file:

   android_ndk_repository(
       name = "androidndk",
       api_level = 21,
   )

   android_sdk_repository(
       name = "androidsdk",
       api_level = 29,
   )

   
bazel build -c opt --config=android_arm64 --action_env PYTHON_BIN_PATH=$(which python3) mediapipe/examples/android/src/java/com/google/mediapipe/apps/basic:helloworld

bazel build -c opt --config=android_arm64 --action_env PYTHON_BIN_PATH=$(which python3) mediapipe/examples/android/src/java/com/google/mediapipe/apps/posetrackinggpu:posetrackinggpu



adb install bazel-bin/mediapipe/examples/android/src/java/com/google/mediapipe/apps/basic/helloworld.apk

adb install bazel-bin/mediapipe/examples/android/src/java/com/google/mediapipe/apps/posetrackinggpu/posetrackinggpu.apk



AAR Android:




##########################################################################################

diff --git a/mediapipe/framework/BUILD b/mediapipe/framework/BUILD
index d2ed6cf..d68159f 100644
--- a/mediapipe/framework/BUILD
+++ b/mediapipe/framework/BUILD
@@ -78,13 +78,13 @@ mediapipe_proto_library(
 mediapipe_proto_library(
     name = "mediapipe_options_proto",
     srcs = ["mediapipe_options.proto"],
-    visibility = ["//mediapipe/framework:__subpackages__"],
+    visibility = [":mediapipe_internal"],
 )
 
 mediapipe_proto_library(
     name = "packet_factory_proto",
     srcs = ["packet_factory.proto"],
-    visibility = ["//mediapipe/framework:__subpackages__"],
+    visibility = [":mediapipe_internal"],
 )
 
 mediapipe_proto_library(
@@ -105,7 +105,7 @@ mediapipe_proto_library(
 mediapipe_proto_library(
     name = "status_handler_proto",
     srcs = ["status_handler.proto"],
-    visibility = ["//mediapipe/framework:__subpackages__"],
+    visibility = [":mediapipe_internal"],
     deps = ["//mediapipe/framework:mediapipe_options_proto"],
 )
 
diff --git a/mediapipe/java/com/google/mediapipe/mediapipe_aar.bzl b/mediapipe/java/com/google/mediapipe/mediapipe_aar.bzl
index 5aa4fa2..b477439 100644
--- a/mediapipe/java/com/google/mediapipe/mediapipe_aar.bzl
+++ b/mediapipe/java/com/google/mediapipe/mediapipe_aar.bzl
@@ -152,6 +152,13 @@ cat > $(OUTS) <<EOF
             "@maven//:com_google_flogger_flogger_system_backend",
             "@maven//:com_google_guava_guava",
             "@maven//:androidx_lifecycle_lifecycle_common",
+            "@com_google_protobuf//:protobuf_java",
+            "//mediapipe/framework:stream_handler_java_proto_lite",
+            "//mediapipe/framework:status_handler_java_proto_lite",
+            "//mediapipe/framework:packet_factory_java_proto_lite",
+            "//mediapipe/framework:mediapipe_options_java_proto_lite",
+            "//mediapipe/framework:packet_generator_java_proto_lite",
+            "//mediapipe/framework:calculator_options_java_proto_lite",
         ],
         assets = assets,
         assets_dir = assets_dir,
         
####################################################################################################


bazel build -c opt --host_crosstool_top=@bazel_tools//tools/cpp:toolchain --fat_apk_cpu=arm64-v8a,armeabi-v7a //mediapipe/examples/android/src/java/com/google/mediapipe/apps/aar_example:mp_face_detection_aar


bazel build -c opt mediapipe/graphs/face_detection:face_detection_mobile_gpu_binary_graph




Change in MainActivity.java

private static final String BINARY_GRAPH_NAME = "face_detection_mobile_gpu.binarypb";



