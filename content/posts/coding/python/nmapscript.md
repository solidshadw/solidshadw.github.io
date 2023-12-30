---
title: "Nmap Python Script"
summary : "Nmap python script to help automate"
date: 2023-02-01
layout: "simple"
tags: [coding, python, copilot, nmap]
categories: [coding, python]
---

One of my goals lately has being to learn how to program and what better way than to just find something that you can automate/improve and try to code it out. 
In this case, I wanted to create a nmap script that would do a quick scan mostly to just find out what ports where open, it is like a ping scan but it is full handshake. After it grabs all the open ports, then it will do a more comprehensive scan with the `-sV` argument and the `default` and `safe` nmap scripts. Here is the code I came up with, with some help of Copilot:

```python3
#!/usr/bin/python3

import subprocess
import os
import argparse
import shutil

# Create ArgumentParser object
parser = argparse.ArgumentParser(description='Perform nmap scan.')
group = parser.add_mutually_exclusive_group(required=True)
group.add_argument('-ip', help='Single IP address to scan')
group.add_argument('-f', '--file', help='File with list of hosts to scan, one per line')
parser.add_argument('-s', '--save', help='Directory to save the output files (optional)')
args = parser.parse_args()

# Check if nmap is installed on the host
if not shutil.which("nmap"):
    print("nmap not found. Please install nmap and try again.")
    exit(1)

# Create the directory if it doesn't exist
if args.save:
    os.makedirs(args.save, exist_ok=True)

try:
    hosts = []
    if args.ip:
        hosts.append(args.ip)
    elif args.file:
        with open(args.file, 'r') as file:
            hosts = file.read().splitlines()

    for ip in hosts:
        print(f"Scanning {ip}...")

        # Perform quick scan
        print("Performing quick scan of ALL ports...")
        quickScanResult = subprocess.run(["nmap", "-Pn", "-p-", "--min-rate=1000", "-T4", ip], capture_output=True, text=True)

        # Extract open ports
        open_ports = [line.split("/")[0] for line in quickScanResult.stdout.split("\n") if "open" in line]
        print("Open ports:", ",".join(open_ports))

        # Perform more comprehensive scan
        print("\nPerforming comprehensive scan with Default and safe nmap script...\n")
        comprehensiveScanResults = subprocess.run(["nmap", "-Pn", "-sV", "-p" + ",".join(open_ports), "--script=default,vuln", ip], capture_output=True)

        # Save results of comprehensive scan to file
        if args.save:
            underscoreIP = ip.replace(".", "_")
            output_file = os.path.join(args.save, "nmapComprehensive-" + underscoreIP + ".txt")
            with open(output_file, "w") as file:
                file.write(comprehensiveScanResults.stdout.decode())

        # Print results of comprehensive scan
        print(comprehensiveScanResults.stdout.decode())

except Exception as e:
    print(f"An error occurred: {e}")
```