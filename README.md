# Tasmota Dashboard

This is a web based dashboard, showing your tasmota devices.
This is how it might look.
<img width="1239" height="910" alt="dashboard" src="https://github.com/user-attachments/assets/988ecd4a-e1b2-4ee7-9109-f5803a8e0fc1" />

Just add the IP Addresses of your tasmota devices in the index.html.


## Installation

For installation, you need a reverse proxy running, because otherwise CORS
policy prevents any interaction with the tasmota devices, like polling the tasmota
version or getting the relay state or sending toggle commands.

The general working principle would be like this:
```                                                                                    
 ┌────────────┐┌───────────────┐┌─────────────────┐┌────────────┐┌────────────┐     
 │PC (Browser)││nginx Webserver││nginx Webserver  ││Tasmota1    ││Tasmota2    │     
 │192.168.0.50││192.168.0.10:80││192.168.0.10:8080││192.168.0.20││291.168.0.21│     
 └─────┬──────┘└───────┬───────┘└───────┬─────────┘└─────┬──────┘└─────┬──────┘     
       │               │                │                │             │            
       ├──────────────►│                │                │             │            
       │   index.html  │                │                │             │            
       │◄──────────────┤                │                │             │            
       │               │          (1)   │                │             │            
       ├───────────────┼───────────────►│       (2)      │             │            
       │               │                ├───────────────►│             │            
       │               │                │◄───────────────┤             │            
       │◄──────────────┼────────────────┤       (3)      │             │            
       │               │                │                │             │            
       ├───────────────┼───────────────►│                │             │            
       │               │                ├────────────────┼────────────►│            
       │               │                │◄───────────────┼─────────────┤            
       │◄──────────────┼────────────────┤                │             │            
       │               │                │                │             │            
       │               │                │                │             │            
       │               │                │                │             │            
       │               │                │                │             │
```                                                                               
                                                                                    
  (1) http://192.168.0.10:8080/tasmota/192.168.0.20/cm?cmnd=Status%202                                                   
  (2) http://192.168.0.20/cm?cmnd=Status%202                                                                                                                      
  (3) {"StatusFWR":{"Version":"15.2.0(release-tasmota)","BuildDateTime":"2025-12-12

So, as you can see in the figure above, the nginx works for convenience on port 80
serving the index.html
Secondly, you need an additional site in nginx that forwards the request from the
dashboard to the tasmota devices. This a achieved by adding the the sample config file
provided in this repo named tasmota-dashboard to the nginx config directory sites-available
and sites-enabled.
                                                                                    
