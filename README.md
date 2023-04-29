# aosp_memo


- [aosp\_memo](#aosp_memo)
- [Build previous AOSP (9.0 etc.) on newly OS(Centos or Rockylinux 8 etc.)](#build-previous-aosp-90-etc-on-newly-oscentos-or-rockylinux-8-etc)
  - [Solving problem like "Assertion \`cnt \< (sizeof (\_nl\_value\_type\_LC\_TIME) / sizeof (\_nl\_value\_type\_LC\_TIME\[0\]))' failed."](#solving-problem-like-assertion-cnt--sizeof-_nl_value_type_lc_time--sizeof-_nl_value_type_lc_time0-failed)
- [Debug Android Native Service](#debug-android-native-service)
  - [Caveats for AOSP 12 on Centos/Rockylinux](#caveats-for-aosp-12-on-centosrockylinux)
  - [Emulator](#emulator)
  - [VSCode](#vscode)
  - [Other Tips](#other-tips)


# Build previous AOSP (9.0 etc.) on newly OS(Centos or Rockylinux 8 etc.)

## Solving problem like "Assertion `cnt < (sizeof (_nl_value_type_LC_TIME) / sizeof (_nl_value_type_LC_TIME[0]))' failed."
- [Your flex is too old](https://stackoverflow.com/questions/49301627/android-7-1-2-armv7)
```bash
# host
cd prebuilts/misc/linux-x86/flex
rm flex-2.5.39
tar zxf flex-2.5.39.tar.gz
cd flex-2.5.39
./configure
make
mv flex ../
cd ../
rm -rf flex-2.5.39
mv flex flex-2.5.39
```

# Debug Android Native Service

## Caveats for AOSP 12 on Centos/Rockylinux
- Don't Use gdbserver , the one in AOSP is wrongly parse the argument
- Don't use gdbclient.py or lldclient.py, poor python coding will drive you crazy
- Import the whole AOSP into your VSCode workspace.
## Emulator

- Writable rootfs (>= Android 10)
```bash
# host
source build/envsetup.sh && lunch sdk_car_x86_64-userdebug
emulator -writable-system <--- '-writable-system' that's the key

adb root
adb shell avbctl disable-verification <--- or adb disable-verity
adb reboot

adb root
adb remount
```
- Prepare lldb-server

```bash
# host 
adb push lldb-server /data/local/tmp/
adb forward tcp:1234 tcp:1234

# target
/data/local/tmp/lldb-server platform --listen *:1234
```

## VSCode

- Import settings.json
- Import extensions.json
- Import launch.json
- Check the parameters for launch.json

```
1. adb devices <--- get the device like emulator-5554
2. get the native pid
3. change the source mapping
```

## Other Tips

- [lldb-vscode manual](https://github.com/vadimcn/vscode-lldb/blob/master/MANUAL.md)
- [GDB to LLDB command map](https://lldb.llvm.org/use/map.html)
