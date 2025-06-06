| Supported Targets | ESP32-H2 | ESP32-C6 |
| ----------------- | -------- | -------- |

# Green Power proxy combo Example 

This example demonstrates how to configure Zigbee Green Power combo (proxy + sink) and use it as on/off light to receive Zigbee Green Power frame from Zigbee Green device.

## Hardware Required

* One 802.15.4 enabled development board (e.g., ESP32-H2 or ESP32-C6) running this example.
* A second board running as Zigbee Green Power device (see [green power device](../esp_zigbee_gpd/) example)

## Configure the project

Before project configuration and build, make sure to set the correct chip target using `idf.py set-target TARGET` command.

## Erase the NVRAM 

Before flash it to the board, it is recommended to erase NVRAM if user doesn't want to keep the previous examples or other projects stored info 
using `idf.py -p PORT erase-flash`

## Build and Flash

Build the project, flash it to the board, and start the monitor tool to view the serial output by running `idf.py -p PORT flash monitor`.

(To exit the serial monitor, type ``Ctrl-]``.)

## Application Functions

- When the program starts, the board, acting as the Zigbee Coordinator with the `Home Automation On/Off Light` function and forms an open Zigbee network within 180 seconds.
```
I (459) main_task: Calling app_main()
W (469) rmt: channel resolution loss, real=10666666
I (469) gpio: GPIO[8]| InputEn: 0| OutputEn: 1| OpenDrain: 0| Pullup: 1| Pulldown: 0| Intr:0 
I (479) gpio: GPIO[9]| InputEn: 1| OutputEn: 0| OpenDrain: 0| Pullup: 1| Pulldown: 0| Intr:2 
I (499) phy: phy_version: 220,2, 37a29de, Dec 29 2023, 16:30:13
I (499) phy: libbtbb version: 944f18e, Dec 29 2023, 16:30:30
I (499) main_task: Returned from app_main()
I (579) ESP_ZGP_COMBO: ZDO signal: ZDO Config Ready (0x17), status: ESP_FAIL
I (579) ESP_ZGP_COMBO: Zigbee stack initialized
I (589) ESP_ZGP_COMBO: Start network formation
W (749) ESP_ZGP_COMBO: Network(0x6bd2) closed, devices joining not allowed.
I (749) ESP_ZGP_COMBO: Formed network successfully (Extended PAN ID: 60:55:f9:ff:fe:f7:2e:16, PAN ID: 0x6bd2, Channel:24, Short Address: 0x0000)
I (1359) ESP_ZGP_COMBO: Network(0x6bd2) is open for 180 seconds
I (1359) ESP_ZGP_COMBO: Network steering started
```
- If `ZGP_COMBO_PROXY_ENABLED` is enabled, the device acts as the `Green Power Combo` and can handle the Green Power device message by itself.

- Pressing the `BOOT` button on the board will initiate Green Power commissioning mode, and the board will wait for the `Green Power Device` to commission it.
```
I (1359) ESP_ZGP_COMBO: Network steering started
I (5479) ESP_ZGP_COMBO: Enter commissioning mode
I (5479) ESP_ZGP_COMBO: ZGP new mode 1 cause: 2
I (9619) ESP_ZGP_COMBO: handle accept commissioning information
I (10219) ESP_ZGP_COMBO: Commissioning done with status: 0
I (10219) ESP_ZGP_COMBO: Commissioning information: app_id 0 endpoint 0 and src_id is 0x12345678
I (10229) ESP_ZGP_COMBO: ZGP new mode 0 cause: 3
```

- If the commissioning is successful, the `Green Power Device` can send a `toggle` command to control the LED on the board.
```
I (11929) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (11929) ESP_ZGP_COMBO: Light sets to On
I (12779) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (12779) ESP_ZGP_COMBO: Light sets to Off
I (13489) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (13489) ESP_ZGP_COMBO: Light sets to On
I (13999) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (13999) ESP_ZGP_COMBO: Light sets to Off
```

- If `ZGP_COMBO_PROXY_ENABLED` is disabled, the device acts as the Green Power sink and requires a `Green Power Proxy` to forward the Green Power command.

- Pressing the `BOOT` button on the board will request the `Green Power Proxy` on the network to proxy the Green Power command.
```
I (18650) ESP_ZGP_COMBO: Enter commissioning mode
I (18650) ESP_ZGP_COMBO: Send commissioning mode to ZGP proxy
I (18650) ESP_ZGP_COMBO: ZGP new mode 1 cause: 2
I (21620) ESP_ZGP_COMBO: Received ZGP sink message: endpoint(242), cluster(0x21), command(0x4)
I (21630) ESP_ZGP_COMBO: handle accept commissioning information
I (21780) ESP_ZGP_COMBO: Commissioning done with status: 0
I (21780) ESP_ZGP_COMBO: Commissioning information: app_id 0 endpoint 0 and src_id is 0x12345678
I (21790) ESP_ZGP_COMBO: ZGP new mode 0 cause: 3
```

- If the `Green Power Device` sends a `toggle` command to the network, the board will receive the forwarded message from the `Green Power Proxy`.
```
I (25310) ESP_ZGP_COMBO: Received ZGP sink message: endpoint(242), cluster(0x21), command(0x0)
I (25310) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (25320) ESP_ZGP_COMBO: Light sets to On
I (26060) ESP_ZGP_COMBO: Received ZGP sink message: endpoint(242), cluster(0x21), command(0x0)
I (26060) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (26070) ESP_ZGP_COMBO: Light sets to Off
I (26550) ESP_ZGP_COMBO: Received ZGP sink message: endpoint(242), cluster(0x21), command(0x0)
I (26560) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (26560) ESP_ZGP_COMBO: Light sets to On
I (27470) ESP_ZGP_COMBO: Received ZGP sink message: endpoint(242), cluster(0x21), command(0x0)
I (27480) ESP_ZGP_COMBO: Received message: endpoint(10), cluster(0x6), attribute(0x0), data size(1)
I (27480) ESP_ZGP_COMBO: Light sets to Off
```

## Troubleshooting

For any technical queries, please open an [issue](https://github.com/espressif/esp-zigbee-sdk/issues) on GitHub. We will get back to you soon.
