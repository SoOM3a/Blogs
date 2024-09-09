---
title: Threat Intelligence Attribution
date: 2024-09-09 08:25:41 AM
categories: [Threat Intelligence]
tags: [ThreatIntelligence] 
render_with_liquid: false
media_subpath: /assets/img/ThreatIntelligence/Attribution
authors: [0]
---

## Whodunit Tool

![whodunit](whodunt.jpg)

Whodunit is a tool that can be used to identify the most likely Advanced Persistent Threat (APT) group responsible for an attack. The tool ingests a cyber security report that contains MITRE ATT&CK techniques identified in an attack and then outputs the APT groups most likely to have been responsible for the incident.

### Installation 
```bash
git clone https://gitlab.com/bontchev/whodunit.git
cd whodunit
pip install -r requirements.txt
python3 whodunit.py -h
```
![Tool Run](run.jpg)

### Running

![Tool Run](running-whodunit-tool.jpg)

### How Can whodunit Help Me?
Adversary attribution is a very difficult problem to solve. and in most cases Cyber intellgience prefer not to attribute the attack to known groups as leak of some knowlege inreport. but attribution is still matter. 

- Did the Russians do this cyber attack?
- Did China do this cyber attack but pretended to be Russia to avoid blame?
- Did the US do this cyber attack but framed China framing Russia to achieve some political motive?