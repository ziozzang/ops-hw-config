# High level design of ops-hw-config
OpenSwitch protocol daemons are designed to be hardware independent. Hardware dependency is isolated to the platform daemons, which dynamically acquire the supported hardware and capability information from hardware description files.

The ops-hw-config repository stores all the hardware description files for each platform for each vendor.

## Design choices
The hardware description files are written in [YAML](http://yaml.org). [JSON](http://www.json.org/) and [XML](http://www.w3.org/XML/) were also considered. While each of these have advantages, the simple syntax of YAML was considered to be better suited for describing hardware capabilities.

## Relationships to external OpenSwitch entities
```ditaa
  +----------+        +------------------+
  | platform |        | config-yaml lib  |      +----------------------+
  | daemons  +------->|                  |----->| hw description files |
  |          |        |                  |      +----------------------+
  |          |        |                  |
  |          |        |           +------+      +----------+
  |          |        |           | i2c  |      | hardware |
  |          +------------------->|      |----->|          |
  |          |        |           |      |      |          |
  +----------+        +-----------+------+      +----------+
```

The platform daemons read and parse the hardware description files using the [config-yaml library](http://www.openswitch.net/documents/dev/ops-config-yaml/DESIGN). The hardware description files contain the information required to perform i2c reads and writes to the devices. i2c accesses are performed using the config-yaml library i2c functions.

## Hardware description files
The hardware description files are:
* manifest.yaml
* devices.yaml
* ports.yaml
* thermal.yaml
* fans.yaml
* leds.yaml
* power.yaml

### manifest.yaml
The manifest.yaml file is the one required file in the directory. The purpose of the manifest.yaml file is to list the hardware description files that exist for a given product. Files should be present (and listed) only if the hardware type for that file is supported. For example, if a product has no LEDs, then the manifest.yaml file would not list a leds.yaml file.

### devices.yaml
The devices.yaml file enumerates the i2c buses, devices, and device addressing. The information includes the register addresses and register masks needed to read/write the devices.

### ports.yaml
The ports.yaml file lists the physical interfaces (face-plate ports), their type (SFP/SFP+, QSFP+, etc), and capabilities.

### thermal.yaml
The thermal.yaml file includes the specification for the temperature sensors. The alarm and fan thresholds are given to correlate temperature to required fan speed.

### fans.yaml
The fans.yaml file details the fan speed settings, lists the fan FRUs, and indicates if the airflow for the fan FRU is back-to-front or front-to-back.

### leds.yaml
The leds.yaml file includes the LED types and capabilities.

### power.yaml
The power.yaml file lists the power supply units and how to read their status.

## References
* [YAML](http://yaml.org)
* [JSON](http://www.json.org/)
* [XML](http://www.w3.org/XML/)
* [config-yaml library](http://www.openswitch.net/documents/dev/ops-config-yaml/DESIGN)
