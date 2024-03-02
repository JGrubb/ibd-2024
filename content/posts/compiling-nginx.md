---
title: Compiling Nginx
date: 2014-02-22
tags: 
  - devops
alias: 225-compiling-nginx
---

Because I have to look this up every. single. time.

pcre ---

`apt-get install libpcre3 libpcre3-dev`

zlib ---

`apt-get install zlibc zlib1g zlib1g-dev`

#### init script

~~~sh
#! /bin/sh

### BEGIN INIT INFO
# Provides: nginx
# Required-Start: $all
# Required-Stop: $all
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: starts the nginx web server
# Description: starts nginx using start-stop-daemon
### END INIT INFO

PATH=/usr/local/nginx/sbin:/sbin:/bin:/usr/sbin:/usr/bin
DAEMON=/usr/local/nginx/sbin/nginx
NAME=nginx
DESC=nginx

test -x $DAEMON || exit 0

# Include nginx defaults if available
if [ -f /etc/default/nginx ] ; then
 . /etc/default/nginx
fi

set -e

case "$1" in
  start)
  echo -n "Starting $DESC: "
  start-stop-daemon --start --quiet --pidfile /var/run/$NAME.pid \
  --exec $DAEMON -- $DAEMON_OPTS
  echo "$NAME."
  ;;
  stop)
  echo -n "Stopping $DESC: "
  start-stop-daemon --stop --quiet --pidfile /var/run/$NAME.pid \
  --exec $DAEMON
  echo "$NAME."
  ;;
  restart|force-reload)
  echo -n "Restarting $DESC: "
  start-stop-daemon --stop --quiet --pidfile \
  /var/run/$NAME.pid --exec $DAEMON
  sleep 1
  start-stop-daemon --start --quiet --pidfile \
  /var/run/$NAME.pid --exec $DAEMON -- $DAEMON_OPTS
  echo "$NAME."
  ;;
  reload)
  echo -n "Reloading $DESC configuration: "
  start-stop-daemon --stop --signal HUP --quiet --pidfile /var/run/$NAME.pid \
  --exec $DAEMON
  echo "$NAME."
  ;;
  \*)
  N=/etc/init.d/$NAME
  echo "Usage: $N {start|stop|restart|reload|force-reload}" >&2
  exit 1
  ;;
  esac

  exit 0
~~~

Also, try running configure with 

`./configure --with-http_realip_module --with-http_gzip_static_module`