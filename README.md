ToRotate
==================

```
               Docker Container
               -------------------------------------
               (Optional)           <-> Tor Proxy 1
Client <---->   Privoxy <-> HAproxy <-> Tor Proxy 2
                                    <-> Tor Proxy n
```

Environment Variables
-----
 * `tors` - Integer, number of tor instances to run. (Default: 20)
 * `new_circuit_period` - Integer, NewCircuitPeriod parameter value in seconds.
   (Default: 2 minutes)
 * `max_circuit_dirtiness` - Integer, MaxCircuitDirtiness parameter value in
   seconds. (Default: 10 minutes)
 * `circuit_build_timeout` - Integer, CircuitBuildTimeout parameter value in
   seconds. (Default: 60 seconds)
 * `privoxy` - Boolean, whatever to run insance of privoxy in front of haproxy.
 * `privoxy_port` - Integer, port for privoxy. (Default: 8118)
 * `privoxy_permit` - Space-separated list of source addresses for permit-access option. (Default: Unset)
 * `privoxy_deny` - Space-separated list of source addresses for deny-access option. (Default: Unset)
 * `haproxy_port` - Integer, port for haproxy. (Default: 5566)
 * `haproxy_stats` - Integer, port for haproxy monitor. (Default: 2090)
 * `haproxy_login` and `haproxy_pass` - BasicAuth config for haproxy monitor.
   (Default: `admin` in both variables)
 * `test_url` - URL for health check throught Tor proxy.
   (Default: http://google.com)
 * `test_status` - Integer, HTTP status code for `test_url` in working case.
   (Default: 302)

Usage
-----

```bash
# build docker container
docker build -t deepcrawler/torotate:latest .

# start docker with privoxy enabled and exposed
docker run -d -p 8118:8118 -p 2090:2090 -e tors=25 -e privoxy=1 deepcrawler/torotate

# test socket with ...
curl --socks5 localhost:5566 http://httpbin.org/ip

# test http proxy with...
curl --proxy http://localhost:8118 http://httpbin.org/ip

# or to run chromium with your new proxy
chromium --proxy-server="http://localhost:8118" \
    --host-resolver-rules="MAP * 0.0.0.0 , EXCLUDE localhost"

# monitor
# auth login:admin
# auth pass:admin
http://localhost:2090 or http://admin:admin@localhost:2090
