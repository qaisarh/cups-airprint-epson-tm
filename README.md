## Ubuntu Xenial Docker image with CUPS and additional Epson TM-T20II drivers
It is intended to be used as an AirPrint relay on the Synology NAS, and the local Avahi will be used to advertise printers on the network.

Сhanges in comparison with the [original](https://github.com/quadportnick/docker-cups-airprint):
* updated version of Ubuntu
* added prebuilded [rastertozj filter](https://github.com/nemik/epson-tm-t20-cups/blob/master/rastertozj) 
* added tm-t20-orig.ppd is tm-t20ii-rastertotmt.ppd provided on Epson site modifited to use rastertozj filter
* added tm-t20-NP.ppd is tm-t20-orig.ppd modifited to make possible print Nova Poshta Zebra Markings 100x100mm from iOS (without fit-to-page option)

This is also a reason to dive deeper into GitHub / Docker / CUPS, so why not? Hope this is helpful to someone.

### Prereqs
* No other printers should be shared under Control Panel>External Devices>Printer so that the DSM's CUPS is not running. 
* `Enable Bonjour service discovery` needs to be marked under Control Panel>Network>DSM Settings 

### Configuration

#### Volumes:
* `/config`: where the persistent printer configs will be stored
* `/services`: where the Avahi service files will be generated

#### Variables:
* `CUPSADMIN`: the CUPS admin user you want created
* `CUPSPASSWORD`: the password for the CUPS admin user

#### Ports:
* `631`: the TCP port for CUPS must be exposed

### Using
CUPS will be configurable at http://[diskstation]:631 using the CUPSADMIN/CUPSPASSWORD when you do something administrative.

### CLI mode running
```
docker run -d \
  --name=cups \
  -p 631:631 \
  -v /volume/docker/cups/config:/config \
  -v /etc/avahi/services:/services \
  -e CUPSADMIN="admin" \
  -e CUPSPASSWORD="admin" \
  xyzroe/cups-airprint-epson-tm
```

### Notes
* If the `/services` volume isn't mapping to `/etc/avahi/services` then you will have to manually copy the .service files to that path at the command line.
* CUPS doesn't write out `printers.conf` immediately when making changes even though they're live in CUPS. Therefore it will take a few moments before the services files update
* Don't stop the container immediately if you intend to have a persistent configuration for this same reason

