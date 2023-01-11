Config for load balancer.

vrrp_instance VI_1 {
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
