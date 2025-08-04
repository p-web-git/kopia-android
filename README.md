# kopia-android

## Problem

When trying to execute kopia on termux running on android 14, I was failing to connect to the repositiory because kopia failed to you the DNS from android. So when doing

``` bash
$ kopia repositiory connect s3 \
      --bucket=test \
      --endpoint=minio.mydomain.org:9001 \
      ...
ERROR can't connect to storage: error retrieving storage config from bucket "test": Get "https://minio.mydomain.org:9001/test/.storageconfig": dial tcp: lookup minio.mydomain.org on [::1]:53: read udp [::1]:60141->[::1]:53: read: connection refused
```

## Solution 

Build kopia from source and ensures the cgo DNS resolver is used

```bash
$ wget https://dl.google.com/android/repository/android-ndk-r28c-linux.zip
$ unzip android-ndk-r28c-linux.zip
$ export ANDROID_NDK=~/src/android-ndk-r28c
$ export CC="$ANDROID_NDK/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android24-clang"
$ export CXX="$ANDROID_NDK/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android24-clang++"
$ export GOOS=android GOARCH=arm64 CGO_ENABLED=1
$ go install -tags netcgo -o kopia-android github.com/kopia/kopia@v0.21.1
```

