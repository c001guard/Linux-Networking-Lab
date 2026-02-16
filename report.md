## Part 1. ipcalc tool

### 1.1 Networks and Masks

1. Define network address of _192.167.38.54/13_
  
  ![1](data/1.png)

2. Converse:
  - Mask 255.255.255.0 to prefix and binary
    ![2](data/2.png)

    Prefix - 24
    Binary - 11111111.11111111.11111111.00000000

  - 15 to normal and binary
    ![3](data/3.png)

    Normal - 255.254.0.0
    Binary - 11111111.11111110.000000000.0000000

  - 11111111.11111111.11111111.11110000 to normal and prefix
    ![4](data/4.png)

    Normal - 255.255.255.240
    Prefix - 28

3. Define minimum and maximum hosts in _12.167.38.4_ with masks:
  - _/8_
    ![5](data/5.png)
    
    Min - 12.0.0.1
    Max - 12.255.255.24
  
  - _/11111111.11111111.00000000.00000000_
    ![6](data/6.png)
    
    Min - 12.167.38.1
    Max - 12.167.38.354
  
  - _/255.255.255.254_
    ![7](data/7.png)

    Min - 12.167.38.4
    Max - 12.167.38.5

  - _/4_
    ![8](data/8.png)

    Min - 0.0.0.1
    Max - 15.255.255.254

### 1.2 localhost

| IP            | Is Access |
| ------------- | --------- |
| 194.34.23.100 | False     |
| 127.0.0.2     | True      |
| 127.1.0.1     | True      |
| 128.0.0.1     | False     |

### 1.3 Network ranges and segments
  - Public/Private IP:
    | IP             | Range   |
    | -------------- | ------- |
    | 10.0.0.45      | Private |
    | 134.43.0.2     | Public  |
    | 192.168.4.2    | Private |
    | 172.20.250.4   | Private |
    | 172.0.2.1      | Public  |
    | 192.172.0.1    | Public  |
    | 172.68.0.2     | Public  |
    | 172.16.255.255 | Private |
    | 10.10.10.10    | Private |
    | 192.169.168.1  | Public  |
  
  - Possible gateway IP addresses for _10.10.0.0/18_ network:
    | IP          | Possible |
    |-------------|----------|
    | 10.0.0.1    | False    |
    | 10.10.0.2   | True     |
    | 10.10.10.10 | True     |
    | 10.10.100.1 | False    |
    | 10.10.1.255 | True     |


## Part 2. Static routing between two machines

  - Existing network interfaces on _ws1_ and _ws2_ with the `ip a` command:
    - _ws1_
    ![9](data/9.png)

    - _ws2_
    ![10](data/10.png)

  - Describe the network interface for _ws1_ and _ws2_:
    - _ws1_
    ![11](data/11.png)
    - _ws2_
    ![12](data/12.png)

  - Restart the network service with `netplan apply` command:
    - _ws1_
    ![13](data/13.png)
    - _ws2_
    ![14](data/14.png)

### 2.1 Adding a static route manually
  
  - Add static route:
    - from _ws1_ to _ws2_
      ![15](data/15.png)
  
    - from _ws2_ to _ws1_
      ![16](data/16.png)
  
  - Ping:
    - From _ws1_ to _ws2_ 
      ![17](data/17.png)
    - From _ws2_ to _ws1_
      ![18](data/18.png)

### 2.2 Adding a static route with saving

  - Add static route to another using _/etc/netplan/00-installer-config.yaml_:
    - From _ws1_ to _ws2_
      ![19](data/19.png)
    
    - From _ws2_ to _ws1_
      ![20](data/20.png)

  - Ping:
    - _ws1_
      ![21](data/21.png)
    - _ws2_
      ![22](data/22.png)
      

## Part 3. perf3 utility

### 3.1. Connection speed

| Base    | Convert      |
|---------|--------------|
| 8 Mbps  | 1 MB/s       |
| 10 MB/s | 819 200 Kbps |
| 1 Gbps  | 1024 Mbps    |


### 3.2. iperf3 utility

  - Measure connection speed between _ws1_ and _ws2_:
    - _ws1_ (Server)
      ![23](data/23.png)
    - _ws2_ (Client)
      ![24](data/24.png)


## Part 4. Network firewall

### 4.1. iptables utility

  - Create file _/etc/firewall.sh_ and add rules from the task
    - _ws1_
      ![25](data/25.png)
    - _ws2_
      ![26](data/26.png)
  
  - Run files 
    - _ws1_
      ![27](data/27.png)
    - _ws2_
      ![28](data/28.png)

### 4.2. nmap utility

  - Use `ping` command to find a machine whoch no pinged:
    - _ws1_
      ![29](data/29.png)
    - _ws2_
      ![30](data/30.png)

  - Use the `nmap` utility to show that the machine host is up
    ![31](data/31.png) 


## Part 5. Static network routing
### 5.1. Configuration of machine addresses

  - Set up the machine configurations in _etc/netplan/00-installer-config.yaml_:
    - _ws11_
      ![32](data/32.png)
    - _ws21_
      ![33](data/33.png)
    - _ws22_
      ![34](data/34.png)
    - _r1_
      ![35](data/35.png)
    - _r2_
      ![36](data/36.png)
  
  - Result of  `ip -4 a` command:
    - _ws11_
      ![37](data/37.png)
    - _ws21_
      ![38](data/38.png)
    - _ws22_
      ![39](data/39.png)
    - _r1_
      ![40](data/40.png)
    - _r2_
      ![41](data/41.png)
  
  - Ping:
    - _ws21_ -> _ws22_
      ![42](data/42.png)
    - _ws11_ -> _r1_
      ![43](data/43.png)

### 5.2. Enabling IP forwarding
  - Enable IP forwarding on the routers with `sysctl -w net.ipv4.ip_forward=1` command:
    - _r1_
      ![44](data/44.png)
    - _r2_
      ![45](data/45.png)
  - Open /etc/sysctl.conf file and add `net.ipv4.ip_forward=1`
    - _r1_
      ![46](data/46.png)
    - _r2_
      ![47](data/47.png)

### 5.3. Default route configuration
  - Configure the defaul route for the workstations at `/etc/netplan/00-installer-config.yaml`:
    - _ws11_
      ![48](data/48.png)
    - _ws21_
      ![49](data/49.png)
    - _ws22_
      ![50](data/50.png)
  
  - Call `ip r`
    - _ws11_
      ![51](data/51.png)
    - _ws21_
      ![52](data/52.png)
    - _ws22_
      ![53](data/53.png)
  
  - Ping _ws11_ -> _r2
  - _ws11_
    ![54](data/54.png)
  - _r2_
    ![55](data/55.png)

### 5.4. Adding static routes
 
  - Add static routes to _r1_ and _r2_ in configuration files:
    - _r1_
      ![56](data/56.png)
    - _r2_
      ![57](data/57.png)
  
  - Call `ip r` and show route tables:
    - _r1_
      ![58](data/58.png)
    - _r2_
      ![59](data/59.png)
    
  - Run `ip r list 10.10.0.0/[netmask]` and `ip r list 0.0.0.0/0` on _ws11_:
    - `ip r list 10.10.0.0/[netmsk]`
      ![60](data/60.png)
    - `ip r list 0.0.0.0/0`
      ![61](data/61.png)
    - The reason the `10.10.0.0/18`network has a route other than `0.0.0.0/0` is because it’s more specific. A `/18` route covers a much smaller range of addresses, while `/0` matches literally every IP.

### 5.5. Making a router list

  - Run the `tcpdump -tnv -i enp1s01` dump command on _r1_
    ![62](data/62.png)

  - Build a list of routers from _ws11_ to _ws21_ using the `traceroute` utility.
    ![63](data/63.png)
   - The traceroute tool works by showing all the routers a packet travels through on its way to the destination. It does this using the TTL (Time-to-Live) field and ICMP messages.
   TTL is basically a counter in every IP packet that tells how many “hops” the packet can make before it’s discarded. The first packet starts with a TTL of 1. When it reaches the first router, the router reduces the TTL by one — it hits zero — and the router sends back an ICMP “Time Exceeded” message to let you know it was there.
   Then traceroute sends another packet, this time with TTL set to 2. That one gets through the first router and expires at the second one, which also replies.
   Traceroute keeps repeating this, increasing TTL by one each time, so you can see the full path your packets take to reach the destination.

### 5.6. Using ICMP protocol in routing
  - Run on _r1_ network traffic capture with the `tcpdump -n -i enp1s0 icmp` command
  - Ping non-existent IP (10.30.0.111) from _ws11_
    ![64](data/64.png)
  - Result of ping at dump:
    ![65](data/65.png)

## Part 6. Dynamic IP configuration using DHCP
  - Configure the DHCP service in the /etc/dhcp/dhcpd.conf file for _r2_:
    ![66](data/66.png)
  - Write `nameserver 8.8.8.8` in _/etc/resolv.conf_ file
    ![67](data/67.png)
  - Restart the DHCP service with `systemctl restart isc-dhcp-server`
    ![68](data/68.png)
  - Reboot the _ws21_ machine with `reboot`
  - Result of `ip a`
   ![69](data/69.png)
  - Ping _ws21_ -> _ws22_
    ![70](data/70.png)
  - Add `macaddress` parameter at _ws11_
    ![71](data/71.png)
  - Configure _r1_ the same way as _r2_, but make the assignment of addresse stricly linked to the MAC-address (_ws11_):
    - Configure DHCP service
      ![72](data/72.png)
    - Write `nameserver 8.8.8.8` in _/etc/resolv.conf_ file
      ![73](data/73.png)
    - Restart the DHCP service
      ![74](data/74.png)
    - Reboot _ws11_
    - Result of `ip a`
      ![75](data/75.png)
  - Request IP address update from _ws21_
    - Before
      ![76](data/76.png)
    - After
      ![77](data/77.png)
  - Used DHCP options:
    - _subnet_ - defines the network for which the DHCP server assigns IP addresses.
    -  _netmask_ - specifies the subnetmask.
    -  _range [IP-min][IP-max] - sets the range of IP addresses available for clients to obtain DHCP server.
    -  _option routers_ - defines the default gateway used to access networks outside the local one.
    -  _option domain-name-servers_ - secifies the addresses of DNS servers.
    -  _host_ - assigns static IP addresses based on a machine`s MAC address.

## Part 7. NAT

- Make the __Apache2__ server public:
  - _r1_
    ![78](data/78.png)
  - _ws22_
    ![79](data/79.png)
- Start the Apache web server with `service apache2 start` command:
  - _r1_
    ![80](data/80.png)
  - _ws22_
    ![81](data/81.png)
- Add to the firewall on _r2_, created similary to the firewall from __Part 4__, folowing rules:
  ![82](data/82.png)
- Run the file as in __Part 4__:
  - ![83](data/83.png)
  - ![84](data/84.png)
- Check connection between _ws22_ and _r1_:
  ![85](data/85.png)
- Enable SNAT (Source Network Address Translation) and DNAT (Destination Network Address Translation), and allow connections via the TCP protocol on port 80:
  ![86](data/86.png)
  