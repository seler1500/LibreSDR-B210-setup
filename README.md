# LibreSDR-B210-setup
Setting up LibreSDR B210 setup guide with uhd-oc and SatDump. I'm attempting to make a single guide to simplify the setup, original sources of parts of this guide are listed below.
I'll be using SatDump v2.0.0 (verywip), as it was recently patched to improve USRP stability on most devices. 
###### This guide was tested on Ubuntu 24.04.5 LTS

# Prerequisites

- Ubuntu Linux or another Debian-based distribution
- A USRP B210 LibreSDR clone (XC7A100T+AD9361 ONLY)

# Credits and sources
Initial setup, FPGA image:  
https://github.com/lmesserStep/LibreSDRB210  
Overclocking the B210:  
https://github.com/MothMaux/uhd-oc  
*A generic satellite data processing software*, SatDump:  
https://github.com/SatDump/SatDump  

# Step 1 - Install UHD and SatDump dependencies
<details>

<summary>Unmodified USRP Hardware Driver</summary>

```
sudo add-apt-repository ppa:ettusresearch/uhd 
sudo apt update 
sudo apt install libuhd-dev uhd-host
```
</details>
<details>

<summary>SatDump v2.0.0 Dependencies - Debian, Ubuntu, and other Debian-based distros</summary>

```
sudo apt install git build-essential cmake g++ pkgconf libfftw3-dev libpng-dev \
                 libtiff-dev libjemalloc-dev libcurl4-openssl-dev libvolk-dev libnng-dev \
                 libglfw3-dev zenity portaudio19-dev libzstd-dev libhdf5-dev librtlsdr-dev \
                 libhackrf-dev libairspy-dev libairspyhf-dev libad9361-dev libiio-dev \
                 libbladerf-dev libomp-dev ocl-icd-opencl-dev intel-opencl-icd mesa-opencl-icd \
                 libdbus-1-dev libsqlite3-dev
```
</details>

# Step 2 - UHD Setup
First, you'll need to set up unomdified USRP Hardware Driver. 
After installing it in step 1, you'll need to download firmware and FPGA images.

### Install Python3 (if not already installed)
`sudo apt install python3`
### Download firmware and FPGA images
`sudo /usr/lib/uhd/utils/uhd_images_downloader.py`   
Once the images and firmware finish downloading, you can check if your SDR is properly detected by UHD.

## LibreSDR B210
```
$ uhd_usrp_probe
[INFO] [UHD] linux; GNU C++ version 13.3.0; Boost_108300; UHD_4.9.0.0-0ubuntu1~noble3
[INFO] [B200] Loading firmware image: /usr/share/uhd/4.9.0/images/usrp_b200_fw.hex...
[INFO] [B200] Detected Device: B210
[INFO] [B200] Loading FPGA image: /usr/share/uhd/4.9.0/images/usrp_b210_fpga.bin...
Error: RuntimeError: fx3 is in state 5
```
This is behaviour is expected, as Libre B210 requires a custom FPGA image to work properly. Replace it using:
```
wget https://github.com/lmesserStep/LibreSDRB210/raw/main/usrp_b210_fpga.bin
sudo cp usrp_b210_fpga.bin /usr/share/uhd/4.9.0/images/
```
<details>
<summary>Now verify again using</summary>
  
```
$ uhd_usrp_probe
[INFO] [UHD] linux; GNU C++ version 13.3.0; Boost_108300; UHD_4.9.0.0-0ubuntu1~noble3
[INFO] [B200] Detected Device: B210
[INFO] [B200] Loading FPGA image: /home/seler1500/usrp_b210_fpga.bin...
[INFO] [B200] Operating over USB 3.
[INFO] [B200] Detecting internal GPSDO.... 
[INFO] [GPS] No GPSDO found
[INFO] [B200] Initialize CODEC control...
[INFO] [B200] Initialize Radio control...
[INFO] [B200] Performing register loopback test... 
[INFO] [B200] Register loopback test passed
[INFO] [B200] Performing register loopback test... 
[INFO] [B200] Register loopback test passed
[INFO] [B200] Setting master clock rate selection to 'automatic'.
[INFO] [B200] Asking for clock rate 16.000000 MHz... 
[INFO] [B200] Actually got clock rate 16.000000 MHz.
  _____________________________________________________
 /
|       Device: B-Series Device
|     _____________________________________________________
|    /
|   |       Mboard: B210
|   |   serial: *******
|   |   name: LibreSDR_B210mini
|   |   product: 2
|   |   revision: 4
|   |   FW Version: 8.0
|   |   FPGA Version: 16.0
|   |   
|   |   Time sources:  none, internal, external, gpsdo
|   |   Clock sources: internal, external, gpsdo
|   |   Sensors: ref_locked
|   |     _____________________________________________________
|   |    /
|   |   |       RX DSP: 0
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       RX DSP: 1
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       RX Dboard: A
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       RX Frontend: A
|   |   |   |   Name: FE-RX2
|   |   |   |   Antennas: TX/RX, RX2
|   |   |   |   Sensors: temp, rssi, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 76.0 step 1.0 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       RX Frontend: B
|   |   |   |   Name: FE-RX1
|   |   |   |   Antennas: TX/RX, RX2
|   |   |   |   Sensors: temp, rssi, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 76.0 step 1.0 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       RX Codec: A
|   |   |   |   Name: B210 RX dual ADC
|   |   |   |   Gain Elements: None
|   |     _____________________________________________________
|   |    /
|   |   |       TX DSP: 0
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       TX DSP: 1
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       TX Dboard: A
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       TX Frontend: A
|   |   |   |   Name: FE-TX2
|   |   |   |   Antennas: TX/RX
|   |   |   |   Sensors: temp, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 89.8 step 0.2 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       TX Frontend: B
|   |   |   |   Name: FE-TX1
|   |   |   |   Antennas: TX/RX
|   |   |   |   Sensors: temp, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 89.8 step 0.2 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       TX Codec: A
|   |   |   |   Name: B210 TX dual DAC
|   |   |   |   Gain Elements: None
```
</details>






