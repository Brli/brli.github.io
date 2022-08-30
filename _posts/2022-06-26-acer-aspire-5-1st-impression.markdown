---
layout: post
title:  "acer 宏碁 12 代文書筆電 Aspire5 初探"
date:   2022-06-26 20:30:00 +0800
tag: laptop installation

---

```
0000:00:00.0 Host bridge: Intel Corporation Device 4601 (rev 04)
0000:00:02.0 VGA compatible controller: Intel Corporation Device 46a8 (rev 0c)
0000:00:04.0 Signal processing controller: Intel Corporation Alder Lake Innovation Platform Framework Processor Participant (rev 04)
0000:00:06.0 System peripheral: Intel Corporation Device 09ab
0000:00:07.0 PCI bridge: Intel Corporation Alder Lake-P Thunderbolt 4 PCI Express Root Port #0 (rev 04)
0000:00:08.0 System peripheral: Intel Corporation 12th Gen Core Processor Gaussian & Neural Accelerator (rev 04)
0000:00:0d.0 USB controller: Intel Corporation Alder Lake-P Thunderbolt 4 USB Controller (rev 04)
0000:00:0d.2 USB controller: Intel Corporation Alder Lake-P Thunderbolt 4 NHI #0 (rev 04)
0000:00:0e.0 RAID bus controller: Intel Corporation Volume Management Device NVMe RAID Controller
0000:00:14.0 USB controller: Intel Corporation Alder Lake PCH USB 3.2 xHCI Host Controller (rev 01)
0000:00:14.2 RAM memory: Intel Corporation Alder Lake PCH Shared SRAM (rev 01)
0000:00:15.0 Serial bus controller: Intel Corporation Alder Lake PCH Serial IO I2C Controller #0 (rev 01)
0000:00:15.1 Serial bus controller: Intel Corporation Alder Lake PCH Serial IO I2C Controller #1 (rev 01)
0000:00:16.0 Communication controller: Intel Corporation Alder Lake PCH HECI Controller (rev 01)
0000:00:17.0 System peripheral: Intel Corporation Device 09ab
0000:00:19.0 Serial bus controller: Intel Corporation Alder Lake-P Serial IO I2C Controller #0 (rev 01)
0000:00:19.1 Serial bus controller: Intel Corporation Alder Lake-P Serial IO I2C Controller #1 (rev 01)
0000:00:1d.0 PCI bridge: Intel Corporation Device 51b0 (rev 01)
0000:00:1d.3 PCI bridge: Intel Corporation Device 51b3 (rev 01)
0000:00:1f.0 ISA bridge: Intel Corporation Alder Lake PCH eSPI Controller (rev 01)
0000:00:1f.3 Multimedia audio controller: Intel Corporation Alder Lake PCH-P High Definition Audio Controller (rev 01)
0000:00:1f.4 SMBus: Intel Corporation Alder Lake PCH-P SMBus Host Controller (rev 01)
0000:00:1f.5 Serial bus controller: Intel Corporation Alder Lake-P PCH SPI Controller (rev 01)
0000:2a:00.0 Network controller: MEDIATEK Corp. MT7921 802.11ax PCI Express Wireless Network Adapter
0000:2b:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 15)
10000:e0:06.0 PCI bridge: Intel Corporation 12th Gen Core Processor PCI Express x4 Controller #0 (rev 04)
10000:e0:17.0 SATA controller: Intel Corporation Alder Lake-P SATA AHCI Controller (rev 01)
10000:e0:1c.0 System peripheral: Intel Corporation Device 09ab
10000:e0:1c.4 PCI bridge: Intel Corporation Device 51bc (rev 01)
10000:e1:00.0 Non-Volatile memory controller: Micron/Crucial Technology Device 5407
10000:e2:00.0 Non-Volatile memory controller: SK hynix Gold P31 SSD
```

The detailed model number is `acer Aspire5 A515-57-52NZ`

# Audio

Requires pacakge: `sof-firmware` Sound Open Firmware, or non of the ALSA, PulseAudio, pipewire would work.

# Video

VA-API hardware based acceleration requires: `intel-media-driver`, and export `LIBVA_DRIVER_NAME=iHD`

The webcam is not standard v4l2/uvc, thus out of function.

# Ethernet

Even though it shows `RTL8111/8168/8411`, it does work with `r8169` kernel module, doesn't require extra works of installing `r8168` package.