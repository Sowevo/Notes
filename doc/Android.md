# adb同步时间

- 连接adb

  - ```shell
    $ adb connect 10.0.0.247
    ```

- 同步时间

  - ```shell
    $ adb shell date `date +%m%d%H%M%G.%S`
    ```

- 发送按键

  - ```shell
    # 发送F4
    $ adb shell input keyevent KEYCODE_F4
    ```