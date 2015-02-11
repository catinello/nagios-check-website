#!/usr/bin/env bash

# Author: Antonino Catinello
# License: MIT-License
# Description: Nagios HTTP/HTTPS check via wget (with/without Proxy)

#variables
version=0.1
wget=$(which wget)
ssl=""
fake=""
host=""
port=""
proxy=""
url="/"
times=1
timeout=10
warning=500
critical=2000

#functions
#set system proxy from environment
function getProxy() {
	if [ -z "$1" ]; then
		echo $http_proxy | awk -F'http://' '{print $2}'
	else
		echo $https_proxy | awk -F'http://' '{print $2}'
	fi
}


function version() {
	echo "  Version: ${version} - License: MIT"
}

#usage info
function usage() {
	echo '''Usage: check_website [OPTIONS] {HOSTNAME}
  {HOSTNAME}
	host to check (dns/ip)
  [OPTIONS]:
	-p PORT		port to check (default: 80)
	-u URL		url path (default: /)
	-f		use fake agent (windows xp/firefox 25)
	-s 		use SSL via HTTPS (default: 443)
	-P PROXY	proxy access (hostname:port)
	-w WARNING	warning threshold in milliseconds (default: 500)
	-c CRITICAL	critical threshold in milliseconds (default: 2000)
	-n TRIES	number of times to try (default: 1)
	-t TIMEOUT	amount of time to wait in seconds (default: 10)'''
	version
}

#check which threshold was reached
function checkTime() {
	if [ $1 -gt $critical ]; then
		echo -n "CRITICAL"
	elif [ $1 -gt $warning ]; then
		echo -n "WARNING"
	else
		echo -n "OK"
	fi
}

#return code value
function getStatus() {
	if [ $1 -gt $critical ]; then
		retcode=2
	elif [ $1 -gt $warning ]; then
		retcode=1
	else
		retcode=0
	fi
}

#main
#get options
while getopts "w:c:p:sfu:P:n:t:" opt; do
    case $opt in
	w)
            warning=$OPTARG
            ;;
	c)
            critical=$OPTARG
            ;;
        p)
            port=$OPTARG
            ;;
        s)
            ssl=1
            ;;
	f)
	    fake=1
	    ;;
        u)
            url=$OPTARG
            ;;
        P)
            proxy=$OPTARG
            ;;
        n)
            times=$OPTARG
            ;;
        t)
            timeout=$OPTARG
            ;;
        *)
	    usage
            exit 3
            ;;
    esac
done

#define host with last parameter
host=${@: -1}

#hostname is required
if [ -z "$host" ]; then
	echo "Error: host is required"
	usage
	exit 3
fi

#set proxy from environment if available and no proxy option is given
if [ -z "$proxy" ]; then
	proxy="$(getProxy ssl)"
fi

#use ssl or not
if [ -z "$ssl" ]; then
	header="HTTP"
	proxy_cmd="http_proxy=$proxy"
	url_prefix="http://"
else
	header="HTTPS"
	proxy_cmd="https_proxy=$proxy"
	url_prefix="https://"
fi

#different port
if [ -z "$port" ]; then
    url="${url_prefix}${host}${url}"
else
    url="${url_prefix}${host}:${port}${url}"
fi

#check for wget
if [ -z "$wget" ]; then
	echo "Error: wget is missing"
	usage
	exit 3
fi	

#check fake user agent
if [ -z "$fake" ]; then
	#execute and capture execution time and return status of wget
	start=$(echo $(($(date +%s%N)/1000000)))
	$wget --no-check-certificate -t $times --timeout $timeout -O /dev/null -q -e $proxy_cmd $url
	status=$?
	end=$(echo $(($(date +%s%N)/1000000)))
else
	#execute with fake user agent and capture execution time and return status of wget
	start=$(echo $(($(date +%s%N)/1000000)))
	$wget --no-check-certificate -t $times --timeout $timeout -O /dev/null -q -e $proxy_cmd $url \
	--header="User-Agent: Mozilla/5.0 (Windows NT 5.1; rv:25.0) Gecko/20100101 Firefox/25.0" \
	--header="Accept: image/png,image/*;q=0.8,*/*;q=0.5" \
	--header="Accept-Language: de-DE,de;q=0.5" \
	--header="Accept-Encoding: gzip, deflate" 
	status=$?
	end=$(echo $(($(date +%s%N)/1000000)))
fi

#decide output by return code
if [ $status -eq 0 ] ; then
	echo "${header} $(checkTime $((end - start))): $((end - start))ms - ${url}|time=$((end - start))ms;${warning};${critical};0;"
	getStatus $((end - start))
	exit $retcode
else
	echo "${header} UNKNOWN: N/A - ${url}"
	exit 3
fi
