Avahi-Alias
===========
There are currently no configuration options to set up CNAME mDNS entries with
avahi-daemon.  These scripts are an adaption of the script at
http://www.avahi.org/wiki/Examples/PythonPublishAlias with the following
modification:

* Turn the script into a daemon with a debian init script.
* This adaption checks the services every 60 seconds for any new CNAMES to be configured.

Installation
============
1. Copy avahi-alias to /usr/sbin/avahi-alias.
1. Copy avahi-alias.init to /etc/init.d/avahi-alias
1. Configure your services to allow CNAME's to be configured by this script.  See Usage below.
1. Enable the init script with
1.1. /usr/sbin/invoke-rc.d avahi-alias defaults

Usage
=====
The script parses all the existing services every 60 seconds to configure CNAME's. You'll
need to make sure your services are configured with an additional <subtype /> field with
\_cname in the string.

	<?xml version="1.0" standalone='no'?>
	<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
	<service-group>
		<name replace-wildcards="yes">HAProxy: <%= @domain %> (%h)</name>
		<service>
			<type>_http._tcp</type>
			*<subtype>_cname._http._tcp</subtype>*
			<host-name><%= @domain %></host-name>
			<port><%= @port %></port>
		</service>
	</service-group>

Motivation
==========
This script is to inject the equivalent of domain CNAME resource records into
local DNS-SD records for entries.  This allows multiple hostname to resolve to the current
host.  The need for this is because avahi.hosts will automatically filter out name collisions,
and there's currently no means for adding these values through existing service configuration.
