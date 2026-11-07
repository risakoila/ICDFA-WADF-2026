# CIP-B101 Lab 1B - PC System Fundamentals, Disk Sectors and Partition Analysis

**Course:** WADF201 · Basic Computer Skills for Digital Forensics 

**Institution:** International Cybersecurity and Digital Forensics Academy (ICDFA)

**Student:** Risa Thomas Koila  

**Student ID:** C11/26/FCDF/17156

**Date:** 7 November 2026

## Overview

A preliminary, **read-only** forensic examination of a Windows 11 laptop (GPT disk, UEFI) and a Kali Linux VM (MBR disk). The lab covers system identification, evidence-layer mapping, process/PID inspection, sector and disk-size calculations, safe partition-table inspection, boot processes, and file-system identification.

## Repository contents

```
.
├── README.md
├── report/
│   └── CIP-B101_Lab1B_Risa_Koila.pdf         # the submission (single PDF)
├── Screenshot/                             
│   └── Lab1B_Screenshots         # 22 screenshots, named by command/question
└──     
    
```

## Lab parts and where to find them in the report

| Part | Topic | Report section | Figures |
|---|---|---|---|
| A | System identification, storage, evidence-layer mapping | §3 | 1–6 |
| B | Programs, processes, threads, PIDs | §4 | 7 |
| C | Sector and disk-size calculation | §5 | – |
| D | Safe partition-table inspection (Windows + Kali) | §6 | 8–18 |
| E | Boot process, UEFI/BIOS, MBR vs GPT | §7 | 1, 3, 12, 16, 17 |
| F | File-system identification | §8 | 19–22 |
| G | Final task: partitions 1 and 2 | §9 | 10, 14 |

## Required evidence checklist

| # | Evidence | Figure(s) |
|---|---|---|
| 1 | System Information / systeminfo | 1–4 |
| 2 | Disk model and capacity | 5 |
| 3 | Logical volumes and file systems | 6, 11 |
| 4 | Running processes and PIDs | 7 |
| 5 | Disk and partition-table inspection | 9–14, 15–18 |
| 6 | Partition 1 calculation | 10, 14, §9.1 |
| 7 | Partition 2 calculation | 10, 14, §9.1 |
| 8 | BIOS/UEFI boot mode | 1, 3, 12, §7.2 |
| 9 | File-system identification | 19–22 |
| 10 | Summary and conclusion | §10 |

## Key results

**System:** HP Laptop 15-bs0xx · Windows 11 Pro 10.0.22621 · Intel Core i5-7200U · ~16 GB RAM · Acer SSD RE100 512 GB (512,105,932,800 bytes = 1,000,206,900 sectors × 512 B) · **UEFI**, Secure Boot status as documented in the report.

**Final task – partitions 1 and 2** (start sector = offset ÷ 512; size from successive offsets):

| | Type | Start | End | Sectors | Bytes | Size | OS-reported |
|---|---|---|---|---|---|---|---|
| Partition 1 | EFI System, FAT32 | 2,048 | 206,847 | 204,800 | 104,857,600 | 100 MB (0.0977 GB) | 100 MB ✔ |
| Partition 2 | Microsoft Reserved | 206,848 | 239,615 | 32,768 | 16,777,216 | 16 MB (0.0156 GB) | 16 MB ✔ |

**Slide calculation:** 400 × 12 × 17,000 = 81,600,000 sectors × 512 = 41,779,200,000 bytes ≈ **38.91 GB** (÷ 1024³).

**Kali VM cross-check (MBR):** `/dev/sda1` start 2,048, end 167,968,749 → 167,966,702 sectors = 85,998,951,424 B ≈ 80.09 GiB (matches `fdisk`).

## Using the calculator

```bash
python tools/partition_calc.py <start_sector> <end_sector> [bytes_per_sector]
python tools/partition_calc.py --count <sector_count> [bytes_per_sector]

# examples
python tools/partition_calc.py 2048 206847 512     # Partition 1 -> 100 MB
python tools/partition_calc.py --count 32768 512    # Partition 2 -> 16 MB
```

The script only does arithmetic. The report shows every calculation by hand.

## Commands used (all read-only)

**Windows:** `systeminfo`, `msinfo32`, `wmic diskdrive get model,size,serialnumber`, `wmic logicaldisk get caption,filesystem,size,freespace,volumename`, `tasklist`, `Get-Disk`, `Get-Partition`, `Get-Volume`.
**Linux:** `lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT`, `sudo fdisk -l`, `sudo parted -l`, `blkid`, `df -h`, `lsblk -f`, `df -Th`.

## Notes and limitations

- Windows shows exact partition **offsets** but only rounded sizes, so sizes were derived from the difference between consecutive offsets (partitions are contiguous) and cross-checked against Get-Partition.
- A 512-byte sector size is assumed for the Windows disk (disk size and all offsets divide exactly by 512).
- `blkid` was run without `sudo` and printed nothing; file systems were confirmed with `lsblk -f` and `df -Th`.
- The unlettered FAT32/NTFS volumes in `Get-Volume` are matched to the EFI and Recovery partitions by order and size.

## Submission

Submit `report/CIP-B101_Lab1B_Risa_Koila.pdf` as the single PDF report. This repository holds the supporting material.
