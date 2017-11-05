nagios-check-website
===

Nagios HTTP/HTTPS check via wget (with/without Proxy). 


## Installation:

    $ wget https://raw.github.com/catinello/nagios-check-website/master/check_website
    $ chmod +x check_website
    $ mv check_website /#to#/#your#/nagios/libexec/

## Usage:

    Usage: check_website [OPTIONS] {HOSTNAME}
      {HOSTNAME}
    	host to check (dns/ip)
      [OPTIONS]:
    	-p PORT		port to check (default: 80)
    	-u URL		url path (default: /)
    	-f		use fake agent (windows xp/firefox 25)
    	-s		use SSL via HTTPS (default: 443)
    	-P PROXY	proxy access (hostname:port)
    	-w WARNING	warning threshold in milliseconds (default: 500)
    	-c CRITICAL	critical threshold in milliseconds (default: 2000)
    	-n TRIES	number of times to try (default: 1)
    	-t TIMEOUT	amount of time to wait in seconds (default: 10)
    	-C CERTIFICATE	client certificate stored in file location (PEM AND DER file types allowed)
    	-b IP		bind ip address used by wget (default: primary system address)

## Examples:

Check with no options.

    $ ./check_website www.example.com
    HTTP OK: 174ms - http://www.example.com/|time=174ms;500;2000;0;

Check with fake agent results into a warning because 740ms is above the default value of 500.

    $ ./check_website -f www.example.com
    HTTP WARNING: 740ms - http://www.example.com/|time=740ms;500;2000;0;

Check on a non default port with a fixed url expecting a ssl connection via proxy. 

    $ ./check_website -p 8080 -u /index.html -s -P 192.168.27.111:3128 -c 4000 -w 1500 www.example.com
    HTTPS OK: 274ms - https://www.example.com:8080/index.html|time=274ms;1500;4000;0;
	
Check through proxy passing client certificate for authentication.

    ./check_website -P proxy.example.com:3128 -u /ping -s -w 2000 -c 5000 -C /root/certs/client_cert.pem www.example.com
    HTTPS OK: 412ms - https://www.example.com/ping|time=412ms;2000;5000;0;

## Return Values:

Nagios return codes are used.

    0 = OK
    1 = WARNING
    2 = CRITICAL
    3 = UNKNOWN

## License:

[&copy; Antonino Catinello][HOME] - [MIT-License][MIT]

[MIT]:https://github.com/catinello/nagios-check-website/blob/master/LICENSE
[HOME]:http://antonino.catinello.eu
