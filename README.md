## check_haproxy_health: nagios plugin to check haproxy stats

The installation requires python3, the python3 header (python3-devel on CentOS7/Fedora) files and gcc, 
otherwise the installation of haproxyadmin / nagiosplugin with pip3 will fail.

#### Ensure your haproxy.cfg contains a definition for a stats socket, e.g.

    stats socket /var/run/haproxy.sock gid 991 mode 660 level admin

##### The user executing the script must be permitted to use the socket, in this case the icinga user has GID 991


### Basic Usage:

#### Show option and their explanation.

    ./check_haproxy_health.py --help

#### Show available ha resources and their state.

    ./check_haproxy_health.py -f /var/run/haproxy.sock --scan

#### Get percentage of HTTP 5XX requests on backend "app".

    ./check_haproxy_health.py -f /var/run/haproxy.sock --backend app --metric http5XXpct

#### Check for HTTP 4XX on backend "app" without resetting the stats counters after completion. Every value > 10 results in a warning, bigger than 20 is critical.

    ./check_haproxy_health.py -f /var/run/haproxy.sock --nozerocounters --backend app --metric http4XXpct -w 10 -c 20

```text
CHECKHAPROXYHEALTH CRITICAL - Backend "app" reports: 27.06% of all requests returned HTTP 4XX (outside range 0:20) | http_4XX_pct=27.06%;10;20;0;100
```

#### Get total traffic pushed out of fronted "staging_one" in MB.

    ./check_haproxy_health.py -f /var/run/haproxy.sock --nozerocounters --frontend stagingone --metric totalmegabytesout
    
```text
CHECKHAPROXYHEALTH OK - Frontend "staging_one" reports: 0.14MB sent in total | total_megabytes_out=0.14MB;;;0
```

#### Report availability of servers attached to backend "app". Notifiy with a warning if less than 60% of servers are available ("UP"), critical at < 49%

    ./check_haproxy_health.py -f /var/run/haproxy.sock --backend app --metric activeservers -w 60: -c 49:

```text
  CHECKHAPROXYHEALTH WARNING - Backend "app" reports: 50.0% of all servers available are active (outside range 60:) | active_servers=50.0%;60:;49:;0;100
```

#### Report the sum of new sessions for the last second for backend "app". For data reporting tools, reasonable --min and --max values help with graphing the resulting plots.

    ./check_haproxy_health.py --metric newsessions -w 1800 -c 2200 --backend app --nozerocounters --max 3000

```text
  CHECKHAPROXYHEALTH OK - Backend "app" reports: Counted 50 new sessions during previous second | new_sessions=50c;1800;2200;0;3000
```
