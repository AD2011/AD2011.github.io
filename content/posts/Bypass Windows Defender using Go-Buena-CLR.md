+++
title = "Bypass Windows Defender Using Go Buena CLR"
date = "2025-05-12T13:13:49+05:30"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Abir Dey"
authorTwitter = "AD2011"
cover = ""
tags = ["EDREvasion", "MalwareDevelopment", "AMSI", "CLR"]
keywords = ["EDR Evasion", "Malware Development", "AMSI", "CLR"]
description = "Run malicious .NET Libraries without getting detected by patching AMSI using CLR"
showFullContent = false
readingTime = false
hideComments = false
+++

# Bypassing Windows Defender for .NET Malware with Go-Buena-CLR

Windows Defender, with its Anti-Malware Scan Interface (AMSI), poses a formidable challenge for .NET-based malware developers. However, the evolving landscape of offensive security continuously introduces innovative techniques to bypass these defenses. This post explores how to use **Go-Buena-CLR**, a custom CLR host written in Go, to effectively bypass Windows Defender’s AMSI and execute .NET malware without detection.

## Why Bypass AMSI for .NET?

AMSI is a security interface that allows Windows Defender and other anti-malware solutions to inspect scripts and code loaded into memory, making it a primary target for bypass techniques. For .NET malware, AMSI can easily flag suspicious assemblies, preventing execution. Our method leverages **Go-Buena-CLR**, a custom CLR host that allows .NET binaries to run without triggering AMSI’s detection.

## What is Go-Buena-CLR?

**Go-Buena-CLR** is an open-source project that provides a custom CLR host written in Go. It is based on the concept of custom CLR hosting, a technique detailed in the IBM X-Force Red’s research article, ["Being a Good CLR Host"](https://www.ibm.com/think/x-force/being-a-good-clr-host-modernizing-offensive-net-tradecraft) by Joshua Magri.

* **GitHub Repository:** [https://github.com/almounah/go-buena-clr](https://github.com/almounah/go-buena-clr)
* **Core Idea:** It allows .NET assemblies to be loaded and executed in a custom environment, effectively bypassing AMSI and signature-based detections.

## AMSI Evasion Technique

This technique works by embedding a .NET binary (such as Rubeus) within a Go program, which is then compiled into an executable. The resulting binary avoids AMSI detection because it does not follow the standard .NET execution flow, making it difficult for Defender to analyze.

### Step-by-Step Guide

#### Requirements

* **Host Machine:** Kali Linux (for preparation)
* **Target Machine:** Windows 11 (with the latest Windows Defender)
* **Tools:** Go-Buena-CLR, Rubeus (from Ghostpack)

#### 1. Prepare Rubeus with Go-Buena-CLR

1. Download Rubeus.exe from [Ghostpack-CompiledBinaries](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries).
2. Place Rubeus.exe in the `BuenaVillage/` directory of Go-Buena-CLR.
3. Use the provided `helper.go` script to embed the .NET binary:

```bash
cd BuenaVillage
go run helper/helper.go -file=Rubeus.exe && GOOS=windows GOARCH=amd64 go build
```

#### 2. Testing Execution on Windows 11

* Transfer the original `Rubeus.exe` to the Windows 11 machine – Windows Defender flags it immediately.
* Transfer and execute the `Buenavillage.exe` – it bypasses detection, and Rubeus functions as expected.

## Advanced Obfuscation Techniques

To further enhance stealth, you can perform minor code modifications in Go-Buena-CLR:

* Change variable names in the Go source code (e.g., `testNet` to `testNet2`).
* Adjust code structure to avoid static signature detection.

## How This Works (Technical Deep-Dive)

Go-Buena-CLR acts as a custom CLR host, loading and executing the .NET assembly (like Rubeus) within its memory space. Since AMSI typically monitors standard .NET loading procedures, this approach avoids triggering detection by:

* Hosting CLR in a non-standard process (Go executable).
* Executing .NET code in a context that AMSI cannot easily intercept.

## Conclusion

Bypassing Windows Defender using Go-Buena-CLR is a powerful demonstration of the adaptability of offensive techniques. As defenders improve, attackers find new ways to evade detection, maintaining the ongoing cat-and-mouse game in cybersecurity.

## References

* Go-Buena-CLR GitHub: [https://github.com/almounah/go-buena-clr](https://github.com/almounah/go-buena-clr)
* Ghostpack-CompiledBinaries (for Rubeus): [https://github.com/r3motecontrol/Ghostpack-CompiledBinaries](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries)
* IBM X-Force Red Research: [Being a Good CLR Host](https://www.ibm.com/think/x-force/being-a-good-clr-host-modernizing-offensive-net-tradecraft)
