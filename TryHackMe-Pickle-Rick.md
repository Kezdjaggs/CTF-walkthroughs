# 🏴 TryHackMe: Pickle Rick - Complete Walkthrough

**Date:** August 20, 2026  
**Author:** Kehinde Jaggs (CEH)  
**Difficulty:** Easy  
**Machine IP:** 10.10.XXX.XXX  
**Room Link:** [TryHackMe Pickle Rick](https://tryhackme.com/room/picklerick)

---

## 📋 Table of Contents
1. [Reconnaissance](#reconnaissance)
2. [Web Enumeration](#web-enumeration)
3. [Finding the First Ingredient](#finding-the-first-ingredient)
4. [Gaining Access](#gaining-access)
5. [Privilege Escalation](#privilege-escalation)
6. [Flags Captured](#flags-captured)
7. [Lessons Learned](#lessons-learned)

---

## 🔍 Reconnaissance

### Nmap Scan
First, I ran a full port scan to discover open services:

```bash
nmap -sV -sC -T4 -p- 10.10.XXX.XXX
