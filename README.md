# [`nvidia` module](https://github.com/viam-modules/nvidia)

This [nvidia module](https://app.viam.com/module/viam/nvidia) implements an [NVIDIA Jetson Orin Module and Developer Kit](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-orin/), [NVIDIA Jetson AGX](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-agx-xavier/), or [NVIDIA Jetson Nano](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-nano/) using the [`rdk:component:board` API](https://docs.viam.com/appendix/apis/components/board/).

> [!NOTE]
Before configuring your board, you must [create a machine](https://docs.viam.com/cloud/machines/#add-a-new-machine).

> [!CAUTION]
The GPIO pins on Jetson boards are rated for 3.3V signals.
5V signals from encoders and sensors can cause damage to a pin.
We recommend selecting hardware that can operate 3.3V signals or lower.
For details, see your board's specification.
For the Jetson Nano, see pages 1-3 of the [Jetson Nano Developer Kit 40-Pin Expansion Header GPIO Usage Considerations Applications Note](https://developer.nvidia.com/jetson-nano-developer-kit-40-pin-expansion-header-gpio-usage-considerations-applications-note).

## Setup
* Follow the [Jetson setup instructions](https://www.jetsonhacks.com/2020/05/16/nvidia-jetson-xavier-nx-developer-kit/)
* Also run `sudo modprobe spidev` or add as a start up script

## Configure your jetson board

Navigate to the [**CONFIGURE** tab](https://docs.viam.com/configure/) of your [machine](https://docs.viam.com/fleet/machines/) in the [Viam app](https://app.viam.com/).
[Add board / nvidia:jetson to your machine](https://docs.viam.com/configure/#components).

> [!NOTE]
> For more information, see [Configure a Machine](https://docs.viam.com/configure/).

### Attributes

The following attributes are available for `viam:nvidia:jetson` boards:

| Attribute | Type | Required? | Description |
| --------- | ---- | --------- | ----------  |
| `digital_interrupts` | object | Optional | Any digital interrupts's pin number and name. |

For instructions on implementing digital interrupts, see [Digital interrupt configuration](#Digital-interrupt-configuration)

## Example configuration

### `viam:nvidia:jetson`
```json
  {
     "name": "<your-nvidia-jetson-board-name>",
      "model": "viam:nvidia:jetson",
      "type": "board",
      "namespace": "rdk",
      "attributes": {},
      "depends_on": []
  }
```

### Next Steps
- To test your board, expand the **TEST** section of its configuration pane or go to the [**CONTROL** tab](https://docs.viam.com/fleet/control/).
- To write code against your board, use one of the [available SDKs](https://docs.viam.com/sdks/).
- To view examples using a board component, explore [these tutorials](https://docs.viam.com/tutorials/).

## Digital interrupt configuration
[Interrupts](https://en.wikipedia.org/wiki/Interrupt) are a method of signaling precise state changes.
Configuring digital interrupts to monitor GPIO pins on your board is useful when your application needs to know precisely when there is a change in GPIO value between high and low.

- When an interrupt configured on your board processes a change in the state of the GPIO pin it is configured to monitor, it ticks to record the state change.
  You can stream these ticks with the board API's [`StreamTicks()`](https://docs.viam.com/appendix/apis/components/board/#streamticks), or get the current value of the digital interrupt with [`Value()`](https://docs.viam.com/appendix/apis/components/board/#value).
- Calling [`GetGPIO()`](https://docs.viam.com/appendix/apis/components/board/#getgpio) on a GPIO pin, which you can do without configuring interrupts, is useful when you want to know a pin's value at specific points in your program, but is less precise and convenient than using an interrupt.

Integrate `digital_interrupts` into your machine in the `attributes` of your board by adding the following to your board's `attributes` configuration:

```json {class="line-numbers linkable-line-numbers"}
{
  "digital_interrupts": [
    {
      "name": "<your-digital-interrupt-name>",
      "pin": "<your-digital-interrupt-pin-number>"
    }
  ]
}
```

### Attributes

The following attributes are available for `digital_interrupts`:

<!-- prettier-ignore -->
| Name | Type | Required? | Description |
| ---- | ---- | --------- | ----------- |
|`name` | string | **Required** | Your name for the digital interrupt. |
|`pin`| string | **Required** | The pin number of the board's GPIO pin that you wish to configure the digital interrupt for. |

### Example configuration

```json {class="line-numbers linkable-line-numbers"}
{
  "components": [
    {
      "name": "<your-up-upboard-board-name>",
      "model": "viam:up:upboard",
      "type": "board",
      "namespace": "rdk",
      "attributes": {
        "digital_interrupts": [
          {
            "name": "your-interrupt-1",
            "pin": "15"
          },
          {
            "name": "your-interrupt-2",
            "pin": "16"
          }
        ]
      }
    }
  ]
}
```
