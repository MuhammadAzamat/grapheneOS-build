# Build GrapheneOS

This guide represents step by step building GrapneOS for Pixel 5a according to https://grapheneos.org/build

# Downloading source code
## Stable release

```
mkdir grapheneos-TAG_NAME
cd grapheneos-TAG_NAME
repo init -u https://github.com/GrapheneOS/platform_manifest.git -b refs/tags/TAG_NAME
```

for ``` TAG_NAME ``` picked from ``` Version: SP2A.220505.002.2022050301 ``` from https://grapheneos.org/releases#barbet-stable

Verify the manifest:

```
gpg --recv-keys 65EEFE022108E2B708CBFCF7F9E712E59AF5F22A
cd .repo/manifests
git verify-tag $(git describe)
cd ../..
```

Complete the source tree download:
```
repo sync -j16
```

## Updating and switching branches or tags

To update the source tree, run the repo init command again to select the branch or tag and then run repo sync -j16 again. You may need to add --force-sync if a repository switched from one source to another, such as when GrapheneOS forks an additional Android Open Source Project repository. You don't need to start over to switch between different branches or tags. You may need to run repo init again to continue down the same branch since GrapheneOS only provides a stable history via tags.

## Building Kernel
You musbuild kernel separately

List of kernels corresponding to officially supported devices:

    Pixel 3, Pixel 3 XL, Pixel 3a, Pixel 3a XL: crosshatch
        Pixel 3: blueline
        Pixel 3 XL: crosshatch
        Pixel 3a, Pixel 3a XL: bonito
    Pixel 4, Pixel 4 XL, Pixel 4a: coral
        Pixel 4, Pixel 4 XL: coral
        Pixel 4a: sunfish
    Pixel 4a (5G), Pixel 5: redbull
        Pixel 4a (5G): bramble
        Pixel 5: redfin
    Pixel 5a: barbet
    Pixel 6, Pixel 6 Pro: raviole
    
For example, to build the kernel for barbet:
```
cd kernel/google/barbet
git submodule sync
git submodule update --init --recursive
./build.sh barbet
```
## Extracting vendor files for Pixel devices

### Pixel 4 and later

The below commands need to only be run once to initially create a working environment.

``` cd vendor/adevtool/ && yarn install && cd ../..
source script/envsetup.sh
m aapt2 ```

Extract the vendor files corresponding to the matching release with ``` DEVICE ``` and ``` BUILD_ID ``` replaced with the appropriate values:

For ``` BUILD_ID ``` got ID from https://developers.google.com/android/images#barbet, for barbet ``` SP2A.220505.002 ```

``` vendor/adevtool/bin/run download vendor/adevtool/dl/ -d DEVICE -b BUILD_ID -t factory ota
sudo vendor/adevtool/bin/run generate-all vendor/adevtool/config/DEVICE.yml -c vendor/state/DEVICE.json -s vendor/adevtool/dl/DEVICE-BUILD_ID-*.zip
sudo chown -R $(logname):$(logname) vendor/google_devices
vendor/adevtool/bin/run ota-firmware vendor/adevtool/config/DEVICE.yml -f vendor/adevtool/dl/DEVICE-ota-BUILD_ID-*.zip ```

After first command of download images You need second - extract vendor files 
From ``` vendor/adevtool/dl ``` You can find downloaded file like this ``` vendor/adevtool/dl/barbet-sp2a.220505.002-factory-78d755fd.zip ```

In this step I got errors such as ``` REFERENCE ERROR ```, ``` undefined get ```, or ``` command not found: generate-all ```
for fix this I have installed adevtool ``` yarn add global adevtool ```, updated nodejs to v17.

## Setting up the OS build environment

Set up the build environment:

``` source script/envsetup.sh ```

Select the desired build target (barbet is the Pixel 5a):

``` choosecombo release barbet user ```

    
    
