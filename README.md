nagios-check-website
===

Nagios HTTP/HTTPS check via wget (with/without Proxy). 


##Installation:##

    # wget https://raw.github.com/catinello/nagios-check-website/master/check_website
    # chmod +x check_website
    # cp check_website /#to#/#your#/nagios/libexec/

##Usage:##

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

##Examples:##

Check with no options.

    $ ./check_website www.google.com
    HTTP OK: 174ms - http://www.google.com/|time=174ms;500;2000;0;

Check with fake agent results into a warning because 740ms is above the default value of 500.

    $ ./check_website -f www.amazon.com
    HTTP WARNING: 740ms - http://www.amazon.com/|time=740ms;500;2000;0;

Check on a non default port with a fixed url expecting a ssl connection via proxy. 

    $ ./check_website -p 8080 -u /index.html -s -P 192.168.27.111:3128 -c 4000 -w 1500 www.myweb.com
    HTTPS OK: 274ms - https://www.myweb.com:8080/index.html|time=274ms;500;2000;0;

##Return Values:##

Nagios return codes are used.

    0 = OK
    1 = WARNING
    2 = CRITICAL
    3 = UNKNOWN

##License:##

[&copy; Antonino Catinello][HOME] - [MIT-License][MIT]

[MIT]:https://github.com/catinello/nagios-check-website/blob/master/LICENSE
[HOME]:http://antonino.catinello.eu
