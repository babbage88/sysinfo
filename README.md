# Sysinfo

[![Build Status](https://github.com/babbage88/sysinfo/actions/workflows/go.yml/badge.svg)](https://github.com/babbage88/sysinfo/actions/workflows/go.yml)
[![Go Report Card](https://goreportcard.com/badge/github.com/babbage88/sysinfo)](https://goreportcard.com/report/github.com/babbage88/sysinfo)
[![GoDoc](https://godoc.org/github.com/babbage88/sysinfo?status.svg)](https://godoc.org/github.com/babbage88/sysinfo)
[![License](https://img.shields.io/badge/license-MIT-a31f34.svg?maxAge=2592000)](https://github.com/babbage88/sysinfo/blob/master/LICENSE)
[![Powered by](https://img.shields.io/badge/powered_by-Go-5272b4.svg?maxAge=2592000)](https://go.dev/)
[![Platform](https://img.shields.io/badge/platform-Linux-009bde.svg?maxAge=2592000)](https://www.linuxfoundation.org/)

Package sysinfo is a Go library providing Linux OS / kernel / hardware system information. It's completely standalone,
has no dependencies on the host system and doesn't execute external programs. It collects only "inventory type"
information, things that don't change often.

## Code Example

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os/user"

	"github.com/babbage88/sysinfo"
)

func main() {
	current, err := user.Current()
	if err != nil {
		log.Fatal(err)
	}

	if current.Uid != "0" {
		log.Fatal("requires superuser privilege")
	}

	var si sysinfo.SysInfo

	si.GetSysInfo()

	data, err := json.MarshalIndent(&si, "", "  ")
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(string(data))
}
```

## Motivation

I couldn't find any self-contained library that would provide set of data/features I needed. So another sysinfo was
born.

The purpose of the library is to collect only inventory info. No metrics like CPU usage or load average will be added.
The rule of thumb is, if it's changing during the day, every day, it doesn't belong in the library.

The library should work well on any modern/supported Linux distribution. There are no plans to add support for older
unsupported Linux distributions/kernels, to keep the code clean and robust and reduce the maintenance burden.

## Requirements

Sysinfo requires:

- Linux kernel 4.2 or later
- access to /sys & /proc Linux virtual file systems
- access to various files in /etc, /var, /run FS hierarchy
- superuser privileges (to access SMBIOS/DMI table and detect RAM size and properties)

Sysinfo doesn't require ANY other external utility on the target system, which is its primary strength, IMHO.

It depends on Linux internals heavily, so there are no plans to support other operating systems.

## Installation

Just use go get.

```
go get github.com/babbage88/sysinfo
```

There's also a very simple utility demonstrating sysinfo library capabilities. Start it (as superuser) to get pretty
formatted JSON output of all the info that sysinfo library provides. Due to its simplicity, the source code of the
utility also doubles down as an example of how to use the library.

```
go get github.com/babbage88/sysinfo/cmd/sysinfo
```

--

Build demo utility in Docker container:  https://github.com/mattscilipoti/compile_sysinfo

## Sample output

```json
{
  "sysinfo": {
    "version": "0.9.1",
    "timestamp": "2016-09-24T13:30:28.369498856+02:00"
  },
  "node": {
    "hostname": "web12",
    "machineid": "04aa55927ebd390829367c1757b98cac",
    "timezone": "Europe/Zagreb"
  },
  "os": {
    "name": "CentOS Linux 7 (Core)",
    "vendor": "centos",
    "version": "7",
    "release": "7.2.1511",
    "architecture": "amd64"
  },
  "kernel": {
    "release": "3.10.0-327.13.1.el7.x86_64",
    "version": "#1 SMP Thu Mar 31 16:04:38 UTC 2016",
    "architecture": "x86_64"
  },
  "product": {
    "name": "RH2288H V3",
    "vendor": "Huawei",
    "version": "V100R003",
    "serial": "2103711GEL10F3430658"
  },
  "board": {
    "name": "BC11HGSA0",
    "vendor": "Huawei",
    "version": "V100R003",
    "serial": "033HXVCNG3107624"
  },
  "chassis": {
    "type": 17,
    "vendor": "Huawei"
  },
  "bios": {
    "vendor": "Insyde Corp.",
    "version": "3.16",
    "date": "03/16/2016"
  },
  "cpu": {
    "vendor": "GenuineIntel",
    "model": "Intel(R) Xeon(R) CPU E5-2630 v3 @ 2.40GHz",
    "speed": 2400,
    "cache": 20480,
    "cpus": 1,
    "cores": 8,
    "threads": 16
  },
  "memory": {
    "type": "DRAM",
    "speed": 2133,
    "size": 65536
  },
  "storage": [
    {
      "name": "sda",
      "driver": "sd",
      "vendor": "ATA",
      "model": "ST9500620NS",
      "serial": "9XF2HZ9K",
      "size": 500
    }
  ],
  "network": [
    {
      "name": "enp3s0f1",
      "driver": "ixgbe",
      "macaddress": "84:ad:5a:e3:79:71",
      "port": "fibre",
      "speed": 10000
    }
  ]
}
```

## Contributors

Contributors are welcome, just open a new issue / pull request.

## License

```
The MIT License (MIT)

Copyright © 2016 Zlatko Čalušić

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
