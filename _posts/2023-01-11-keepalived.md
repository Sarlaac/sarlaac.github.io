---
title: Keepalive Configurations
date: 2023-01-12 05:30 # HH:MM format
categories: howto
tags: homelab howto
---
# Keepalive Configurations

### Keepalive install
```
sudo apt install keepalived libipset13
```
## Configuration for High Availability
Configuration should be done on the two nodes, setting one as master and one as backup. Master will serve traffic unless unavailable, at which time the backup node will take over serving traffic until master is available again.

### Master Configuration
```
# /etc/keepalived/keepalived.conf
# Keepalive configuration for Master node

vrrp_instance DNS {
  state MASTER
  interface eth0
  virtual_router_id 55  # Must match backup
  priority 150  # Higher than backup
  advert_int 1
  unicast_src_ip 192.168.0.10
  unicast_peer {
    192.168.0.11
  }

  authentication {
    auth_type PASS
    auth_pass password  # Use same on backup
  }

  virtual_ipaddress {
    192.168.0.100/24
  }
}
```
### Backup Configuration
```
# /etc/keepalived/keepalived.conf
# Keepalive configuration for backup node

vrrp_instance DNS {
  state BACKUP
  interface eth0
  virtual_router_id 55
  priority 100
  advert_int 1
  unicast_src_ip 192.168.0.11
  unicast_peer {
    192.168.0.10
  }

  authentication {
    auth_type PASS
    auth_pass password
  }

  virtual_ipaddress {
    192.168.0.100/24
  }
}
```

## Configuration as load balancer.
Alternatively to being used for high availability, keepalive can also be configured as a load balancer using the below configuration. Additionally, both HA and load balance can be combined, however that configuration is not covered here.
```
# /etc/keepalived/keepalived.conf
# Load Balancer node

vrrp_instance LB1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    virtual_ipaddress {
        192.168.0.100
    }
}

virtual_server 192.168.0.100 80 {
    delay_loop 6
    lb_algo rr
    lb_kind NAT
    protocol TCP

    real_server 192.168.0.10 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 3
        }
    }

    real_server 192.168.0.11 80 {
        weight 100
        TCP_CHECK {
            connect_timeout 3
        }
    }
}
```
