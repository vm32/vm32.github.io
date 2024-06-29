# PoCs: Potential Attacks on Apple Devices via Bluetooth Traffic Sniffing

## Introduction

In this report, I investigate Apple's Bluetooth Low Energy (BLE) Continuity protocol, designed to facilitate seamless interoperability and communication between iOS and macOS devices. However, this convenience comes at a cost: it allows passive adversaries to glean identifying information and behavioral data.

## Investigation Overview

### Reverse Engineering Apple's BLE Continuity Protocol

I uncovered several types of Continuity protocol messages through meticulous reverse engineering and identified unencrypted data fields. Continuity messages are broadcast over BLE in response to various actions such as:

- Locking and unlocking the device's screen
- Copying and pasting information
- Making and accepting phone calls
- Tapping the screen while it is unlocked

### Laboratory Experiments

Laboratory experiments revealed a critical flaw in the latest macOS versions, where BLE Media Access Control (MAC) address randomization is completely bypassed, causing the public MAC address to be broadcast. This flaw enables:

- Fingerprinting the device type and OS version
- Creating behavioural profiles of users
- Tracking Apple devices across different locations and times
- Defeating existing anti-tracking measures like MAC address randomization

## Broader Context of Privacy Risks

The widespread use of wirelessly connected mobile devices in our daily lives has created a significant risk of privacy invasion for modern consumers. Mobile devices constantly send and receive information, even when not in active use, and many of the communication protocols were not designed with privacy in mind.

### Tracking Concerns and Privacy Leaks in Wi-Fi

Wi-Fi clients must actively probe for nearby access points to connect, allowing adversaries to listen to these probes and use the device's MAC address (included in the probes) to identify and track it as it moves. 

### MAC Address Randomization

In response to these privacy concerns, device and OS manufacturers introduced MAC address randomization. This technique aims to prevent tracking by periodically changing the MAC address. However, many implementations of MAC address randomization have proven ineffective due to:

- Extraneous information in higher-layer protocols
- Technologies not being privacy-aware
- Information leaks that can be used to track users and devices

### Bluetooth Low Energy (BLE) Privacy

Bluetooth also uses MAC addresses as hardware identifiers. In this study, I exclusively examined Bluetooth Low Energy (BLE), which includes mechanisms for a device to generate random MAC addresses to enhance privacy.
<img width="493" alt="Screenshot 2024-06-29 at 5 36 47 PM" src="https://github.com/vm32/vm32.github.io/assets/21219411/66ffa351-34fd-4d59-a897-4f887634c23e">

<img width="258" alt="Screenshot 2024-06-29 at 5 39 21 PM" src="https://github.com/vm32/vm32.github.io/assets/21219411/93bf5c00-f791-4259-9ad8-b772a44f585f">


## Analysis

This section is divided into three parts:

### 1. Analysis of Passively Collected Data

I reverse-engineered Apple's BLE Continuity framework's frame format and data attributes. This revealed that Continuity features leak a significant amount of data related to user behaviour.

### 2. Active Attacks

I transmitted tailored BLE frames to provoke responses from Apple devices, uncovering more details about user information and behaviour.

### 3. Effectiveness Evaluation

I comprehensively evaluated how effective these attacks are in enabling an adversary to identify and track devices and users.

## Findings

Since I analyzed various types of Continuity messages, each with different flaws, I organized my findings into the following categories based on the information leaked:

### OS Fingerprinting

Identification of the operating system version being used on the device.

### Device Fingerprinting

Determination of the specific device model.

### Tracking

Tracking devices and users across different locations and times.

### User/Device Activity

Gleaning information about user behaviour based on device activity.

### Device Attributes

Extracting specific attributes of the device.

# Passive Analysis Reverse Engineering

I evaluated Apple's proprietary Continuity protocol by inspecting BLE frames emitted from iOS and macOS devices across Apple's ecosystem and OS versions. I collected BLE frames passively using an Ubertooth with stdout piped to Wireshark, using my custom dissector, allowing for real-time dynamic analysis via a live capture display. Apple Continuity frames are transmitted on all three BLE advertisement channels; as such, my Ubertooth collection setup required monitoring only a single advertisement channel.

By turning the phone on and off, navigating the menu, changing settings, and running different apps (phone, calendar, photos, settings), I identified the fields responsible for the Wi-Fi and buffer status and several types of BLE messages.

- **0x05** - Airdrop
- **0x07** - Airpods
- **0x10** - Nearby
- **0x0b** - Watch Connection
- **0x0c** - Handoff
- **0x0d** - Wi-Fi Settings
- **0x0e** - Hotspot
- **0x0f** - Wi-Fi Join Network

Where status can be:
```
0        1        2                                 5
+--------+--------+--------------------------------+
|        |        |                                |
| status | wifi   |           other                |
|        |        |                                |
+--------+--------+--------------------------------+
```

- **0x0b** - Home screen
- **0x1c** - Home screen
- **0x1b** - Home screen
- **0x11** - Home screen
- **0x03** - Off
- **0x18** - Off
- **0x09** - Off
- **0x13** - Off
- **0x0a** - Off
- **0x1a** - Off
- **0x01** - Off
- **0x07** - Lock screen
- **0x17** - Lock screen
- **0x0e** - Calling
- **0x5b** - Home screen
- **0x5a** - Off

That’s enough to write a simple packet analyzer that allows us to get data from all nearby Apple devices in real-time. !!!

# POC

```python
ash2phone_url = ''  
hash2phone_db = "hash2phone/phones.db"
XXX_key = ''  
xxx_pwd = ''  
xxx_api_url = 'xxx.com/api/xxx/?apikey={}&password={}&msisdn='.format(xxx_key, xxx_pwd)
region_check_url = '' 
imessage_url = ''  # i did some RE for that :)
iwdev = 'wlan0'
apple_company_id = 'ff4c00'
dev_id = 0  
toggle_device(dev_id, True)
sock = 0
titles = ['Mac', 'State', 'Device', 'WI-FI', 'OS', 'Phone', 'Time', 'Notes']
dev_sig = {'02010': 'MacBook', '02011': 'iPhone'}
dev_types = ["iPad", "iPhone", "MacOS", "AirPods", "Powerbeats3", "BeatsX", "Beats Solo3"]
phones = {}
resolved_devs = []
resolved_macs = []
resolved_numbers = []
victims = []
verb_messages = []
phone_number_info = {}
hash2phone = {}
dictOfss = {}
proxies = {}
verify = False
```

---

This report is under progress and I will keep updating this project.
