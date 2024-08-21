# SETUP CAMERA

## upgrade system
sudo apt update
sudo apt -y upgrade

## setup python
sudo apt install -y python3-pip python3-venv python3-wheel python3-setuptools python3-build

## enable legacy camera
sudo apt install -y raspi-config
sudo raspi-config

## configure config.txt (add these lines)
start_x=1
gpu_mem=128
dtoverlay=imx219
dtoverlay=vc4-kms-v3d

## reboot system
reboot

## setup latest version of meson
sudo pip3 install meson

## ensure ffmpeg is installed (idk why, just do it)
sudo apt install -y ffmpeg*

## ensure video 4 linux (v4l2) is installed
sudo apt install -y v4l*
sudo modprobe v4l2loopback

## ensure user is part of video group
sudo adduser $USER video

## verify imx219 driver
sudo modinfo imx219

## install libcamera
### install dependencies (assume all packages are needed, more the merrier)
sudo apt install -y git g++ cmake ninja-build pkg-config
sudo apt install -y libboost-dev
sudo apt install -y libavcodec-dev libavdevice-dev libavformat-dev libswresample-dev
sudo apt install -y libyaml-dev libudev-dev libevent-dev libdrm-dev libjpeg-dev libsdl2-dev libtiff-dev libcamera-dev libepoxy-dev libpng-dev
sudo apt install -y libgnutls28-dev openssl libtiff5-dev libpython3-dev pybind11-dev
sudo apt install -y qtbase5-dev libqt5core5a libqt5gui5 libqt5widgets5
sudo apt install -y python3-yaml python3-ply python3-build python3-jinja2
sudo apt install -y libglib2.0-dev libgstreamer-plugins-base1.0-dev
### build from source
cd ~
git clone https://github.com/raspberrypi/libcamera.git
cd libcamera
meson setup build
ninja -C build
sudo ninja -C build install

## install rpicam-apps
### install dependencies
sudo apt install -y cmake libboost-program-options-dev libdrm-dev libexif-dev
sudo apt install -y ninja-build
### build from source
cd ~
git clone https://github.com/raspberrypi/rpicam-apps.git
cd rpicam-apps
meson setup build -Denable_opencv=enabled
meson compile -C build
sudo meson install -C build

## install libkms
### install dependencies
sudo apt install -y  libfmt-dev libdrm-dev libfmt-dev
### install from source
cd ~
git clone https://github.com/tomba/kmsxx.git
cd kmsxx/
git submodule update --init
meson build -Dpykms=enabled
sudo ninja -C build install

## install picamera2
sudo apt install libcap-dev
pip install rpi-libcamera rpi-kms picamera2  # do in your venv

## link libraries together (important)
sudo ldconfig

## start a new session and check everything worked
reboot
rpicam-hello  # should see a visual!
