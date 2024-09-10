---
layout: post
title:  "Visual Packet Tracer"
date:   2024-03-05
author: C. Casquatch
comments: false
tag: ['python', 'packet-tracer', 'programming']
---

> The main purpose of this project is to map the IP addresses from a Wireshark packet capture from the users computer. By inputing the public IP addresses and Wireshark packet capture, we are able to map in Google Maps where our traffic is coming from.


For the Project:

Don't forget to add your own Wireshark packet capture to the folder/IDE you are using for this project. You need this for the program to read the different IP addresses to map.

At the beginning, my program would output the header and footer but did not give me any feedback on my IP or my packet capture. I did a few differnt things to troubleshoot this. 
One; I added a few more checks to the main code to be able to better read where I needed to focus my efforts and where the code was not working. 
From here I was able to create a few different pieces of code to check if the GeoLiteCity file was readable, if my IP was in that file to be read, if the file was locked or not, and the destination source.

To be able to run this project, the only code we need is the Main.py. The other .py files are for troubleshooting only. I put them here for other people's benefit if they are getting stuck.

### Main.py
```python
import dpkt
import socket
import pygeoip

gi = pygeoip.GeoIP('/path/to/GeoLiteCity.dat') #Use your own path to the file

def retKML(dstip, srcip):
    dst = gi.record_by_name(dstip)
    src = gi.record_by_name('127.0.0.1')  # Make sure to put in own Public IP
    try:
        dstlongitude = dst['longitude']
        dstlatitude = dst['latitude']
        srclongitude = src['longitude']
        srclatitude = src['latitude']
        kml = (
            '<Placemark>\n'
            '<name>%s</name>\n'
            '<extrude>1</extrude>\n'
            '<tessellate>1</tessellate>\n'
            '<styleUrl>#transBluePoly</styleUrl>\n'
            '<LineString>\n'
            '<coordinates>%f,%f\n%f,%f</coordinates>\n'
            '</LineString>\n'
            '</Placemark>\n'
        ) % (dstip, dstlongitude, dstlatitude, srclongitude, srclatitude)
        return kml
    except Exception as e:  # Added more specific exception handling
        print(f"Error generating KML for IPs {srcip} and {dstip}: {e}")
        return ''

def plotIPs(pcap):
    kmlPts = ''
    for (ts, buf) in pcap:
        try:
            eth = dpkt.ethernet.Ethernet(buf)
            ip = eth.data
            src = socket.inet_ntoa(ip.src)
            dst = socket.inet_ntoa(ip.dst)
            KML = retKML(dst, src)
            kmlPts += KML  # Changed concatenation
        except Exception as e:  # Added more specific exception handling
            print(f"Error processing packet: {e}")
    return kmlPts

def main():
    try:
        with open('WiresharkCapture.pcap', 'rb') as f:  # Use with statement for file handling MAKE SURE TO ADD YOUR WIRESHARK FILE HERE*****
            pcap = dpkt.pcap.Reader(f)
            kmlheader = '<?xml version="1.0" encoding="UTF-8"?> \n<kml xmlns="http://www.opengis.net/kml/2.2">\n<Document>\n' \
                        '<Style id="transBluePoly">' \
                        '<LineStyle>' \
                        '<width>1.5</width>' \
                        '<color>501400E6</color>' \
                        '</LineStyle>' \
                        '</Style>'
            kmlfooter = '</Document>\n</kml>\n'
            kmldoc = kmlheader + plotIPs(pcap) + kmlfooter
            print(kmldoc)
    except IOError as e:
        print(f"Error opening or reading file: {e}")

if __name__ == '__main__':
    main()
```

* * * 

## The rest of the code from here will be the checks that I did to troubleshoot my own issues. 


#### This code is to check the destination and source IPs. 

```python
def retKML(dstip, srcip):
    print(f"Destination IP: {dstip}")
    print(f"Source IP: {srcip}")

    dst = gi.record_by_name(dstip)
    src = gi.record_by_name('YOUR_PUBLIC_IP')  # Make sure to put in own IP
    try:
        dstlongitude = dst['longitude']
        dstlatitude = dst['latitude']
        srclongitude = src['longitude']
        srclatitude = src['latitude']
        kml = (
            '<Placemark>\n'
            '<name>%s</name>\n'
            '<extrude>1</extrude>\n'
            '<tessellate>1</tessellate>\n'
            '<styleUrl>#transBluePoly</styleUrl>\n'
            '<LineString>\n'  # Added missing opening tag
            '<coordinates>%f,%f\n%f,%f</coordinates>\n'  # Corrected formatting
            '</LineString>\n'  # Corrected closing tag
            '</Placemark>\n'  # Corrected closing tag
        ) % (dstip, dstlongitude, dstlatitude, srclongitude, srclatitude)  # Corrected formatting
        return kml
    except Exception as e:  # Added more specific exception handling
        print(f"Error generating KML for IPs {srcip} and {dstip}: {e}")
        return ''
```

* * * 

#### This code is to check if the file is locked. 

```python
geoip_db_path = '/path/to/GeoLiteCity.dat' #change to your own path to the file

try:
    with open(geoip_db_path, 'rb') as f:
        # Read data from the file
        pass  # Replace 'pass' with your file processing logic
except IOError as e:
    print(f"Error opening or reading file: {e}")
```

* * *

#### This is to check if the file you are referencing in main.py exists. 

```python
import os

# Specify the file path to the GeoIP database
geoip_db_path = '/path/to/GeoLiteCity.dat' #change to your own path to file

# Check if the GeoIP database file exists
if not os.path.exists(geoip_db_path):
    print(f"GeoIP database file '{geoip_db_path}' not found.")
```

* * *

#### This is to double check your own IP address. 

```python
import pygeoip

# Initialize GeoIP database
gi = pygeoip.GeoIP('/PATH/TO/GeoLiteCity.dat') #file path to your GeoLiteCity.dat file

# Specify IP addresses to query
ip_addresses = ['8.8.8.8', '127.0.0.1']  # Example IP addresses - use your own IP

# Query the GeoIP database for each IP address
for ip in ip_addresses:
    record = gi.record_by_name(ip)
    if record:
        print(f"Information for IP address {ip}:")
        print(f"Country: {record['country_name']}")
        print(f"City: {record['city']}")
        print(f"Latitude: {record['latitude']}")
        print(f"Longitude: {record['longitude']}")
        print()
    else:
        print(f"No information available for IP address {ip}")
```

* * *

<button onclick="history.back()">Go Back</button>
