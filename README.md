fetchconfig
===========

original project from: http://download.savannah.gnu.org/releases/fetchconfig/

INTRODUCTION
============

	fetchconfig is a Perl script for retrieving configuration of
	multiple devices. It has been tested under Linux and Windows,
	and currently supports a variety of devices, including Cisco
	IOS, Cisco Catalyst, Cisco ASA, Cisco PIX, FortiGate
	Firewalls, ProCurve, Alcatel Ethernet Routers (aka
	Riverstone), Dell PowerConnect Switches, Terayon 3200/3500
	CMTS, Datacom DmSwitch Switches, HP MSR Routers, Mikrotik
	Routers, Tellabs MSR Routers, Juniper EX Switch JunOS, Oracle
	Acme Packet, Audiocodes Mediant.

LICENSE
=======

        fetchconfig - Retrieving configuration for multiple devices
        Copyright (C) 2006 Everton da Silva Marques

        fetchconfig is free software; you can redistribute it and/or
        modify it under the terms of the GNU General Public License as
        published by the Free Software Foundation; either version 2,
        or (at your option) any later version.

        fetchconfig is distributed in the hope that it will be useful,
        but WITHOUT ANY WARRANTY; without even the implied warranty of
        MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
        GNU General Public License for more details.

	You should have received a copy of the GNU General Public
	License along with fetchconfig; see the file COPYING. If not,
	write to the Free Software Foundation, Inc., 51 Franklin St,
	Fifth Floor, Boston, MA 02110-1301 USA.

INSTALLATION (UNIX/Linux)
=========================

	1. fetchconfig.pl expects a Perl interpreter at /usr/bin/perl.

	2. fetchconfig.pl requires the Net::Telnet module.
	(Use "cpan install Net::Telnet" if you don't have it.)

	3. Unpack the fetchconfig tarball at a suitable
        location. Example:

	[ fetchconfig tarball available at /tmp/fetchconfig-0.0.tar.gz ]

	cd /usr/local
	tar xzf /tmp/fetchconfig-0.0.tar.gz
	ln -s fetchconfig-0.0 fetchconfig

	[ fetchconfig is now available under /usr/local/fetchconfig ]

INSTALLATION (Windows)
======================

	1. fetchconfig.pl expects to find a Perl interpreter in the PATH.

	2. fetchconfig.pl requires the Net::Telnet module.
	(Use "cpan install Net::Telnet" if you don't have it.)

	3. Unpack the fetchconfig tarball at a suitable
	location.  From the command line, gzip and tar (links
	below) can be used.
	http://www.gzip.org
	http://gnuwin32.sourceforge.net/packages/tar.htm

	Alternatively, some commercial programs (such as WinZip)
	offer a GUI.

	Command Line Example:

	[ fetchconfig tarball available at c:\temp\fetchconfig-0.0.tar.gz ]

	c:
	cd \
	gzip -cd c:\temp\fetchconfig-0.0.tar.gz | tar xvf -
	move fetchconfig-0.0 fetchconfig

	[ fetchconfig is now available under c:\fetchconfig ]

USAGE
=====

	1. Edit the file "device_table.example" according your needs,
        and save it as "device_table". Example:

	[ fetchconfig available under /usr/local/fetchconfig ]

	cd /usr/local/fetchconfig
	cp device_table.example device_table
	vi device_table

	2. Run fetchconfig.pl, as in the following example:

	[ fetchconfig available under /usr/local/fetchconfig ]

	cd /usr/local/fetchconfig
	./fetchconfig.pl -devices=device_table

	The fetchconfig.pl script will scan the "device_table" file,
	trying to retrieve the configuration for every device, storing
	configuration files under the "repository".

DEVICE TABLE
============

	The device table assigned with '-device=' switch is a text
        file which supports two types of lines:

	1. Default options lines assign default options to a device
        model. The following example adds some default options to the
        "cisco-ios" model:

	#default	model		model-default-options
	#
	default:        cisco-ios       user=backup,pass=fran,enable=jose

	2. Device lines define attributes of every device and
        optionally assign device-specific options. The following
        example define a device named "vpn-gw":

	#model		dev-unique-id	device-host	[device-options]
	#
	cisco-ios       vpn-gw          192.168.0.1     keep=10,changes_only=1

	The dev-unique-id must be:
	a) an unique identifier across all devices;
	b) a valid filesystem's directory name.

EMBEDDING DEVICE TABLE INTO COMMAND LINE
========================================

	Valid lines for the device table file can also be passed
	directly through the -line=string command line switch.

	Example:
	/path/to/fetchconfig.pl -line="default: tellabs-msr user=backup,pass=s&cr&t,timeout=10,keep=5,changes_only=0,fetch_timeout=1200,filename_append_suffix=.txt,repository=/path/to/repo" -line="tellabs-msr lab-router 10.0.0.1"

DEVICE SUPPORT
==============

	The following devices are known to be supported.  This
	is not a comprehensive list.  Other devices and versions
	are likely to work but have not been verified.

	Model Name	Devices/OS Supported
	----------	------------------------------------------------

	cisco-cat	CatOS switches

	cisco-ios	IOS routers
			2500 series, 2600 series, 2800 series,
			CS-516 (with show_cmd=wrterm option)

	cisco-asa	Cisco ASA firewalls

	cisco-pix	Cisco PIX firewalls

	fortigate	FortiOS 3.0

	parks		Parks 209R

	riverstone	Alcatel Ethernet Router 3100 (Riverstone RS3100)

	terayon-os	Terayon 3200/3500 CMTS

	dmswitch	Datacom DmSwitch

	3com-msr	3Com MSR routers

	mikrotik	MikroTik routers

	tellabs-msr	Tellabs MSR routers

	junos		Juniper EX Switch JunOS

	acme		Oracle Acme Packet

	mediant		Audiocodes Mediant


OPTIONS
=======

	The modules CiscoIOS, CiscoCAT, CiscoASA and CiscoPIX
	recognize the following options:

	pass		A mandatory option, specifies the login password.

	user		Specifies the login username. It can be omitted
                        for devices not requiring a login username.

	enable		Mandatory, specifies the enable password.

	timeout		Mandatory, how long to wait for TELNET responses
                        from devices.

	fetch_timeout	Optional. If given, will override the timeout
                        value used to fetch all the configuration lines.
                        It is useful for devices which spend several
                        seconds building the configuration.

	repository	Mandatory, specifies the base directory for
                        saving the configuration files.

	keep		Mandatory, the maximum number of config files
                        to retain for the device. When this limit is
                        reached, the older files are discarded.

	changes_only 	Optional. Boolean. If specified as
			changes_only=1, only new configurations are
			saved. Otherwise, configurations are saved
			whenever the script runs; if not defined as
			changes_only=1, the script might possibly
			retain multiple identical configuration files.

	on_fetch_run	Optional. Path to an external program to be
			called whenever the configuration is fetched.
			The following environmental variables are
			passed to the program:
			FETCHCONFIG_DEV_ID = device unique id
			FETCHCONFIG_DEV_HOST = device hostname (or IP addr)
			FETCHCONFIG_PREV = pathname to previous config,
                                           undefined if none exists
			FETCHCONFIG_CURR = pathname to current config
			Example:
			on_fetch_run=/usr/local/bin/mail_diff_to_admin.sh
			Please notice that, if changes_only=1, the
			backup file referenced to FETCHCONFIG_CURR
			will be erased right after the external program
			is finished. This option is available for all
			modules.

	on_fetch_cat	Optional. Boolean. If specified as
			on_fetch_cat=1, the currently fetched
			configuration is issued to standard
			output. This option is available for all
			modules.

	timezone	Optional. If defined as timezone=hide, it will
			supress timezone information from config filename.

	filename_append_suffix
			Optional. If defined, it is appended to the
			generated filename. This option is available
			for all modules.
			Example: filename_append_suffix=.txt

	Options specific to CiscoIOS:

	show_cmd	Optional. If specified, its value will replace
			the usual "show run" command. This is necessary
                        for some older devices. It may be useful to
			send alternative commands to capture the
			configuration. To keep compatibility with
			previous versions of fetchconfig, if specified
			as show_cmd=wrterm, "write term" will be used
			in place of the usual "show run" command.

	The fortigate module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	timeout		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The parks module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	timeout		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	Options specific to Parks:

	banner_timeout	Optional. If given, will override the timeout
                        value used to wait for the 'login:' prompt.
                        It is useful since the Parks 209R spends
 			many seconds before issuing the initial
			login prompt for TELNET connections.

	The riverstone module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	enable		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	timeout		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The terayon-os module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	enable		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	timeout		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The dmswitch module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	timeout		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The 3com-msr module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	enable		Mandatory.
	timeout		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The mikrotik module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	enable		Mandatory.
	timeout		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The tellabs module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	enable		Mandatory.
	timeout		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The junos module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	timeout		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The acme module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Optional.
	pass		Mandatory.
	timeout		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

	The mediant module recognizes the following options:
	(See above cisco-ios/cisco-cat section for descriptions.)

	user		Mandatory.
	pass		Mandatory.
	timeout		Mandatory.
	repository	Mandatory.
	keep		Mandatory.
	fetch_timeout	Optional.
	changes_only	Optional.
	on_fetch_run	Optional.
	on_fetch_cat	Optional.
	timezone	Optional.
	filename_append_suffix Optional.

