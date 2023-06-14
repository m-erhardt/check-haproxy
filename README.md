# check-haproxy

## About
* this repository contains a Icinga / Nagios plugin to monitor [HAProxy](https://www.haproxy.org/) (via the stats socket file)
* Written for python3
* No additional python modules required, only uses modules from the Python Standard Library

### Compatibility
* I've tested this plugin with various HAProxy versions from 1.X to 2.X

## Documentation
* This plugin offers two check modes
  * `--mode instance` (default)
    * Checks the whole HAProxy instance
      * If all **backend servers** are `UP`
      * If any of the **backend servers** is getting close to it's **session limit**
      * If all **frontends** are `OPEN`
      * If any of the **frontends** is getting close to it's **session limit**
      * If all **backends** are `UP`
      * If any of the **backends** is getting close to it's **session limit**
    * ![instance mode](docs/img/mode_instance_small?raw=true "instance mode")
  * `--mode frontend`
    * Checks single HAProxy frontend
      * If the specified **frontend** is `OPEN`
      * If the specified **frontend** is getting close to it's **session limit**
    * ![frontend mode](docs/img/mode_frontend_small?raw=true "frontend mode")

### Parameters
```
usage: check_haproxy.py [-h] [--socketfile SOCKETFILE] [--mode {instance,frontend}] [--slimwarn SLIM_WARN] [--slimcrit SLIM_CRIT] [--frontend FRONTEND]

Icinga/Nagios plugin which checks a haproxy load balancer

optional arguments:
  -h, --help            show this help message and exit
  --socketfile SOCKETFILE
                        Location of haproxy stats socket file
  --mode {instance,frontend}
                        Plugin mode

Thresholds:
  --slimwarn SLIM_WARN  Exit WARN if sessions reach <slimwarn>% of session limit
  --slimcrit SLIM_CRIT  Exit CRIT if sessions reach <slimcrit>% of session limit

Mode-specific arguments:
  --frontend FRONTEND   Name of frontend to check (only with "--mode frontend")
```

### Usage examples

```bash
# Instance mode
./check_haproxy.py --socketfile "/var/lib/haproxy/stats" \
  --mode instance \
  --slimwarn 80 \
  --slimcrit 90

OK - haproxy running with 13 frontends, 13 backends, 25 servers and 137 sessions -  | 'sessions'=137;;;; 'sessions_total'=6916926;;;; 'frontends'=13;;;; 'backends'=13;;;; 'servers'=25;;;;
```
![instance mode](docs/img/mode_instance?raw=true "instance mode")

```bash
# Frontend mode
./check_haproxy.py --socketfile "/var/lib/haproxy/stats" \
  --mode frontend --frontend "my-haproxy-frontend" \
  --slimwarn 80 \
  --slimcrit 90

OK - HAProxy frontend my-haproxy-frontend is OPEN, Sessions: 143/10000 | 'sessions'=143;8000.0;9000.0;0;10000 'bytein'=10667774618B;;;; 'byteout'=201081437032B;;;;
```
![frontend mode](docs/img/mode_frontend?raw=true "frontend mode")
