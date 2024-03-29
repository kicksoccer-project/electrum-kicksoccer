# Kivy GUI

The Kivy GUI is used with Electrum-KickSoccer on Android devices.
To generate an APK file, follow these instructions.

## Android binary with Docker

This assumes an Ubuntu host, but it should not be too hard to adapt to another
similar system. The docker commands should be executed in the project's root
folder.

1. Install Docker

    ```
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    $ sudo apt-get update
    $ sudo apt-get install -y docker-ce
    ```

2. Build image

    ```
    $ sudo docker build -t electrum-kicksoccer-android-builder-img electrum/gui/kivy/tools
    ```

3. Build locale files

    ```
    $ ./contrib/make_locale
    ```

4. Prepare pure python dependencies

    ```
    $ sudo ./contrib/make_packages
    ```

5. Build binaries

    ```
    $ sudo docker run -it --rm \
        --name electrum-kicksoccer-android-builder-cont \
        -v $PWD:/home/user/wspace/electrum-kicksoccer \
        -v ~/.keystore:/home/user/.keystore \
        --workdir /home/user/wspace/electrum-kicksoccer \
        electrum-kicksoccer-android-builder-img \
        ./contrib/make_apk
    ```
    This mounts the project dir inside the container,
    and so the modifications will affect it, e.g. `.buildozer` folder
    will be created.

5. The generated binary is in `./bin`.



## FAQ

### I changed something but I don't see any differences on the phone. What did I do wrong?
You probably need to clear the cache: `rm -rf .buildozer/android/platform/build/{build,dists}`


### How do I deploy on connected phone for quick testing?
Assuming `adb` is installed:
```
$ adb -d install -r bin/Electrum*-debug.apk
$ adb shell monkey -p org.electrum.electrum 1
```


### How do I get an interactive shell inside docker?
```
$ sudo docker run -it --rm \
    -v $PWD:/home/user/wspace/electrum-kicksoccer \
    --workdir /home/user/wspace/electrum-kicksoccer \
    electrum-kicksoccer-android-builder-img
```


### How do I get more verbose logs?
See `log_level` in `buildozer.spec`


### Kivy can be run directly on Linux Desktop. How?
Install Kivy.

Build atlas: `(cd electrum/gui/kivy/; make theming)`

Run electrum-kicksoccer with the `-g` switch: `electrum-kicksoccer -g kivy`
