### Background

If you are running FHEM with a lot of devices and want to have Munin graphs for sensor readings, it quickly becomes cumbersome and repetitive to add new graphs. A FHEM system with a little history might also use inconsistent naming which makes things confusing. These plugins aim to automatically pick up changes in your FHEM infrastructure and display new devices automatically.

The plugins use the `list` command of FHEM to find devices with appropriate "readings" for each plugin. That way new devices are picked up automatically and the plugins don't need to care about device names.

I only own and test with HomeMatic devices but I guess these plugins may work for other hardware as well. 

### Plugins

#### fhem_auto_battery

Render voltage level for all FHEM devices with a `batteryLevel` reading.

###### fhem_auto_brightness

Render brightness level for all FHEM devices with a `brightness` reading.

#### fhem_auto_dew

Render calculated dew points for devices with both a `temperature` and a `humidity` reading.

#### fhem_auto_humidity

Render relative humidity for devices with a `humidity` reading.

#### fhem_auto_humidity_abs

Render absolute humidity for devices with both a `temperature` and a `humidity` reading.

#### fhem_auto_motion

Render derived motion events for motion detectors (`subType=motionDetector`). 

#### fhem_auto_power

Render power usage of devices with a `power` reading.

#### fhem_auto_temp

Render temperature readings of (root) devices with either a `temperature` or `measured-temp` reading and, in the latter case, *as long as their `channel_02` is not peered to a different device*. This avoids reporting duplicate values for HomeMatic thermostat valves that use an external temperature sensor (and possibly others).

#### fhem_auto_temp_desired

Render target temperatures of devices with a `desired-temp` reading.

#### fhem_auto_valve

Render valve opening positions of devices with a `ValvePosition` reading.

### Setup

Just drop the plugins into `/etc/munin/plugins` and restart `munin-node`.

If you want more fancy labels or if you want to create graphs with data from several of the pre-defined graphs, you can [borrow data](http://munin-monitoring.org/wiki/LoaningData) like so in `munin.conf`:

```
[home.well-adjusted.de;FHEM]
    use_node_name no
    update no
    temp_wohnzimmer.update no
    temp_wohnzimmer.graph_title Temperaturen Wohnzimmer
    temp_wohnzimmer.graph_args --base 1000
    temp_wohnzimmer.graph_vlabel degrees Celsius
    temp_wohnzimmer.graph_scale no
    temp_wohnzimmer.graph_category fhem
    temp_wohnzimmer.graph_printf %6.1lf
    temp_wohnzimmer.graph_order \
        Wohnzimmer=jigsaw.home.well-adjusted.de:fhem_auto_temp.WandTherm_Wohnzimmer \
        TargetWohnzimmer=jigsaw.home.well-adjusted.de:fhem_auto_temp_desired.WandTherm_Wohnzimmer \
        DewWohnzimmer=jigsaw.home.well-adjusted.de:fhem_auto_dew.Dew_WandTherm_Wohnzimmer_Weather \
        DewAmbient=jigsaw.home.well-adjusted.de:fhem_auto_dew.Dew_THSensor_Veranda
    temp_wohnzimmer.Wohnzimmer.label Ist-Temperatur Wohnzimmer OG
    temp_wohnzimmer.TargetWohnzimmer.label Ziel-Temperatur Wohnzimmer
    temp_wohnzimmer.DewWohnzimmer.label Taupunkt Wohnzimmer
    temp_wohnzimmer.DewAmbient.label Taupunkt aussen
```

### Examples

Here's my setup with default graphs:
http://jigsaw.home.well-adjusted.de/munin/fhem_auto-week.html

And a few custom graphs that only aggregate data from the default graphs:
http://jigsaw.home.well-adjusted.de/munin/home.well-adjusted.de/FHEM/index.html
