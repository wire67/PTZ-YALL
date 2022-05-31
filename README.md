# PTZ-YALL

This custom component allows you to use the Pan and Tilt functions of Chinese cameras that do not fully comply with the ONVIF protocol.

This is a fork from original project available at https://github.com/fjramirez1987/PTZ-YCC365

For some reason, the Home Assistant ONVIF integration does not work with cameras that use the YCC365 Plus, Y05 or YOOSEE apps.

When using ONVIF Device Manager application from PC application it is possible to use pan and tilt functions.

Apparently, these cameras do not fully comply with the ONVIF protocol and the Home Assistant integration cannot integrate them at the moment.

I have looked for a solution to be able to use in Home Assistant with the pan and tilt option.

Using the Wireshark application I have been able to obtain information to use these cameras.

In order to use this custom component, I recommend that you integrate your cameras signal with motionEye. **This component does not integrate the video signal from your cameras, only the pan and tilt function.**

## Setup
You need to install the custom component as usual. Copy the ptz_camera folder from this project to your `/config/custom_components/` directory on your Home Assistant.
If using cameras which relies on apps YOOSEE or Y05, you must set your encrypted password and encryption key into the file `/config/custom_components/_init.py`.
I did not go too deep to check how to get set the proper encryption key to send the password parameter, so you will need to use `WireShark`.
If the password could be set based on Onvif protocol, all the steps below will no longer be required. Maybe a future improvement

1. Download and install the [ONVIF Device Manager](https://sourceforge.net/projects/onvifdm/)
2. Download and install [WireShark](https://www.wireshark.org/#download)
3. Open the ONVIF Device Manager and connect to your camera

3.1. If using YOOSEE cameras, firstly make sure to activate the option for "NVR connection" into camera settings in the app.

4. Try the `PTZ Control` option into ONVIF Device Manager.

4.1. Make sure to select the option `Continuous move` in the PTZ Control settings.

4.2. If it works, go to the next step.

4.3. If not, check the item 3 or your camera's IP into your router.

5. Select your network card or Wi-Fi connect and activate the WireShark packets capturing: `Capture --> Start`
6. Return to ONVIF Device Manager and move your camera to any direction.
7. Stop the WireShark packet capturing:  `Capture --> Stop`
8. In the WireShark filter field, search for commands sent from your computer to the camera: `ip.dst == 192.168.1.244`
9. Look for an entry with protocol like "HTTP/XML" and POST event. Rigth-click and go to `Follow -> TCP Stream`. Look for tags `<Password>` and `<Nonce>`.
![](wireshark.png)
10. Copy them and modify the file `/config/custom_components/_init.py` to add your camera's information.
11. Add the `ptz_camera:` entry into your `configuration.yaml` file.
12. Restart Home Assistant.
13. Setup your card with controls (see below) and try your camera with pan and tilt controls (no zoom, but full view with webRTC option is possible. See Y05 and YCC365 examples).

## Setting
In your `configuration.yaml`:

```yaml
ptz_camera:
```
## Services
This custom component creates several services with domain ptz_camera. To obtain information about these services you can use “Developer Tools” > Services. It will have detailed information about the arguments to call each service.

## Camera Entity

You can create a camera in the usual way. I recommend you to use the [motionEye](https://addons.community/) addon and create a mjpeg camera. It is the best setting I have found with a low delay. An example configuration would be:

```yaml
camera:
  - platform: mjpeg
    name: camera_1
    mjpeg_url: http://192.168.1.111:8083
```

## Card with controls
An easy way to use the pan and tilt controls is to overlay the controls on top of a camera image. Replace the IP address in this example with the IP address of your camera.

Example for YOOSEE camera.

```yaml
type: picture-elements
camera_view: live
camera_image: camera.camera_1
elements:
  - type: icon
    icon: mdi:arrow-left-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_left
      service_data:
        host: 192.168.1.244
        camera_type: YOOSEE
        move_time: 0.7
    style:
      bottom: 45%
      left: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-right-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_right
      service_data:
        host: 192.168.1.244
        camera_type: YOOSEE
        move_time: 0.7
    style:
      bottom: 45%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-up-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_up
      service_data:
        host: 192.168.1.244
        camera_type: YOOSEE
        move_time: 0.7
    style:
      top: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-down-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_down
      service_data:
        host: 192.168.1.244
        camera_type: YOOSEE
        move_time: 0.7
    style:
      bottom: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:home-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_origin
      service_data:
        host: 192.168.1.244
        camera_type: YOOSEE
        move_time: 0.7
        move_steps: 29
    style:
      bottom: 5%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:cctv-off
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_privacy
      service_data:
        host: 192.168.1.244
        camera_type: YOOSEE
        move_time: 0.7
    style:
      bottom: 5%
      left: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
```
Example for Y05 camera:
```yaml
type: picture-elements
camera_view: live
camera_image: camera.camera_1
elements:
  - type: icon
    icon: mdi:arrow-left-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_left
      service_data:
        host: 192.168.1.244
        camera_type: Y05
    style:
      bottom: 45%
      left: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-right-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_right
      service_data:
        host: 192.168.1.244
        camera_type: Y05
    style:
      bottom: 45%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-up-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_up
      service_data:
        host: 192.168.1.244
        camera_type: Y05
    style:
      top: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-down-drop-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_down
      service_data:
        host: 192.168.1.244
        camera_type: Y05
    style:
      bottom: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:arrow-expand-all
    hold_action:
      action: none
    tap_action:
      action: fire-dom-event
      browser_mod:
        command: popup
        deviceID:
          - this
        title: Camera Full View
        large: true
        card:
          type: custom:webrtc-camera
          muted: true
          url: rtsp://admin:xxxxxx@192.168.1.244:8554/profile0
    style:
      top: 5%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:home-circle
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_origin
      service_data:
        host: 192.168.1.244
        camera_type: Y05
        move_time: 0.7
    style:
      bottom: 5%
      right: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
  - type: icon
    icon: mdi:cctv-off
    hold_action:
      action: none
    tap_action:
      action: call-service
      service: ptz_camera.move_privacy
      service_data:
        host: 192.168.1.244
        camera_type: Y05
    style:
      bottom: 5%
      left: 5%
      color: white
      opacity: 0.5
      transform: scale(1.5, 1.5)
```

Example for YCC365
```yaml
type: picture-elements
camera_view: live
camera_image: camera.salon
elements:
  - type: icon
    icon: 'mdi:arrow-left-drop-circle'
    tap_action:
      action: call-service
      service: ptz_camera.move_left
      service_data:
        host: 192.168.1.244
    style:
      bottom: 45%
      left: 5%
      color: white
      opacity: 0.5
      transform: 'scale(1.5, 1.5)'
  - type: icon
    icon: 'mdi:arrow-right-drop-circle'
    tap_action:
      action: call-service
      service: ptz_camera.move_right
      service_data:
        host: 192.168.1.244
    style:
      bottom: 45%
      right: 5%
      color: white
      opacity: 0.5
      transform: 'scale(1.5, 1.5)'
  - type: icon
    icon: 'mdi:arrow-up-drop-circle'
    tap_action:
      action: call-service
      service: ptz_camera.move_up
      service_data:
        host: 192.168.1.244
    style:
      top: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: 'scale(1.5, 1.5)'
  - type: icon
    icon: 'mdi:arrow-down-drop-circle'
    tap_action:
      action: call-service
      service: ptz_camera.move_down
      service_data:
        host: 192.168.1.244
    style:
      bottom: 10%
      left: 46%
      color: white
      opacity: 0.5
      transform: 'scale(1.5, 1.5)'
  - type: icon
    icon: 'mdi:arrow-expand-all'
    tap_action:
      action: more-info
    entity: camera.salon
    style:
      top: 5%
      right: 5%
      color: white
      opacity: 0.5
      transform: 'scale(1.5, 1.5)'
```
![](tarjeta.jpg)
