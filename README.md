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
You just need to install the custom component as usual. Copy the ptz_camera folder from this project to your /config/custom_components/ directory on your Home Assistant.

## Setting
In your configuration.yaml:

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
      
![](tarjeta.jpg)
