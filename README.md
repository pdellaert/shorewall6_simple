shorewall6_simple
=================

_WARNING: This role can be dangerous to use. If you lose network connectivity
to your target host by incorrectly configuring your firewall, you may be
unable to recover without physical access to the machine._

This role installs and configures Shorewall6 for a simple, single network interface (can be a bond, of course) server.

Requirements
------------

This role requires Ansible 1.4 or higher and platform requirements are listed
in the metadata file.

Role Variables
--------------

The variables that can be passed to this role and a brief description about
them are as follows. These are all based on the configuration variables of the
Shorewall6 configuration.

    shorewall6_enabled: "Yes"
    shorewall6_startup: 1
    shorewall6_wait_interface: "eth0"
    shorewall6_options: ""
    shorewall6_startoptions: ""
    shorewall6_restartoptions: ""
    shorewall6_initlog: "/var/log/shorewall6_init.log"
    shorewall6_safestop: 0
    
    shorewall6_zones:
    - zone: "fw"
      type: "firewall"
    - zone: "net"
      type: "ipv6"
      options="-"
      options_in="strict"
      options_out="-"
    
    shorewall6_interfaces: 
    - interface: "eth0"
      zone: "net"
      broadcast: "detect"
      options: "dhcp,tcpflags,nosmurfs"
    
    shorewall6_policies:
    - source: "$FW"
      destination: "net"
      policy: "ACCEPT"
    - source: "net"
      destination: "$FW"
      policy: "ACCEPT"
    - source: "all"
      destination: "all"
      policy: "DROP"
      log_level: "info"
      burst_limit: "10/second:100"
    
    shorewall6_rules:
    - section: "NEW"
      rules:
      - action: "ACCEPT"
        source: "net"
        destination: "$FW"
        protocol: "tcp"
        destination_port: 22
        source_port: "-"
        original_destination: "-"
        rate_limit: "-"
        user_group: "-"
        mark: "-"
        connection_limit: "-"
        time: "-"
        headers: "-"
        switch: "-"

Examples
========

1) Example allowing all traffic in and out

    - hosts: all
      - roles:
        - role: shorewall6_simple
          shorewall6_enabled: "Yes"    
          shorewall6_zones:
          - zone: "fw"
            type: "firewall"
          - zone: "net"
            type: "ipv6"
          shorewall6_interfaces: 
          - interface: "eth0"
            zone: "net"
            broadcast: "detect"
            options: "dhcp,tcpflags,nosmurfs"
          shorewall6_policies:
          - source: "all"
            destination: "all"
            policy: "ACCEPT"

2) Example allowing all outgoing traffic but block incomming traffic and log
it, but allow incomming SSH traffic and accept Ping

    - hosts: all
      - roles:
        - role: shorewall6_simple
          shorewall6_enabled: "Yes"    
          shorewall6_zones:
          - zone: "fw"
            type: "firewall"
          - zone: "net"
            type: "ipv6"
          shorewall6_interfaces: 
          - interface: "eth0"
            zone: "net"
            broadcast: "detect"
            options: "dhcp,tcpflags,nosmurfs"
          shorewall6_policies:
          - source: "$FW"
            destination: "net"
            policy: "ACCEPT"
          - source: "net"
            destination: "$FW"
            policy: "DROP"
            log_level: "info"
          - source: "all"
            destination: "all"
            policy: "DROP"
          shorewall6_rules:
          - section: "NEW"
            rules:
            - action: "Ping/ACCEPT"
              source: "net"
              destination: "$FW"
            - action: "ACCEPT"
              source: "net"
              destination: "$FW"
              protocol: "tcp"
              destination_port: 22

Dependencies
------------

All systems:
- ip6tables

Red Hat based distributions:
- EPEL

License
-------

BSD

Author Information
------------------

Philippe Dellaert


