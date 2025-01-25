# First Saying Thanks:
* .. to markv9401 for this project Fake-Gree-server: https://github.com/markv9401/Fake-Gree-server
* .. to emtek-at for their project GreeAC-DummyServer: https://github.com/emtek-at/GreeAC-DummyServer
* .. to tomikaa87 for his project gree-remote: https://github.com/tomikaa87/gree-remote
* .. to all that made this possible.
  
# Fake-Gree-server
Fake Gree server implementation to have Gree HVAC units work without internet connection to home server.

# Why?
Because for Gree AC units' WiFi control does not work unless they can register to a Gree Server successfully and then keep a heartbeat connection up from time to time.<br>
For me starting from 18-01-2025 gree servers ( eu.dis.gree.com and dis.gree.com ) seems to have problems and are offline and as a result HA integration will not work any more so I have to set-up this Fake server to be able to control my AC unit located in amother location.

# How it's working
There are some things you need to have working before you can use this hack solution:<br>
* Your Gree unit connected to wifi.<br>
* Have your Linux server to host this fake server ( python3 ) to respond to AC home calls.<br>
* A firewall or a DNS server ( pfSense/OPNsense... or pihole ) serving a DNS override of `eu.dis.gree.com` with the IP address of your fake server. You could also block all connections sourcing from the AC unit and going to WAN.<br>

# Setting it all up
1. If you can; monitor the communication from AC on firewall to see to what domain/IP and port it is connecting and set-up your DNS override to point to your Server_IP, in my case it is `eu.dis.gree.com` and port 1812 and 5000.<br>
2. Download, review, edit to your needs and change the server IP and in some cases the port 1812 need to be changed to another one: 1813, 5000... this depend of your AC model and firmware version so find what port work for your AC unit if you can.<br>
   Copy gree_server.py and gree_server_tls.py to /opt/fakegree/ and gree_server.service to /etc/systemd/system and test:<br>
`python3 /opt/fakegree/gree_server.py <SERVER_IP> <PORT> eu.dis.gree.com`<br>
For new versions of AC that use TLS you have to use gree_server_tls.py and generate the server certificate ( read how to do it in: Dockerfile ) so you have to change the service to start tls server.<br>
`python3 /opt/fakegree/gree_server_tls.py <SERVER_IP> <PORT> eu.dis.gree.com True`<br>
3. Turn off/on the HVAC unit and in a few seconds the fake server should be receiving all sorts of connections and everything will be working.
4. If it is ok you can copy and set-up the service: gree_server.service in /etc/systemd/system (enable and start).<br>

# Problems
1. If server will encounter an error, I setup the service to restart after 5 min because OS need ~60 sec time to free the port so if you test multiple time please wait or check if port it is free or used: netstat -tulpn | grep 1812
2. I prefer to use the no TLS server version because for me it work.<br>
   For TLS server it looks like you need to edit the python file and change SERVER_HOST with your Server_IP or something it is not right in that implementation ??
