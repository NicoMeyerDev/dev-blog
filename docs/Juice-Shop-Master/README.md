# Juice Shop Master

> [!IMPORTANT]
> This project is exclusively used as part of my professional training. No personal data, credentials, or sensitive information is used. All work is conducted on a Kali Linux machine.

This project documents the analysis and exploitation of selected security vulnerabilities within the OWASP Juice Shop application. All findings, demonstrations, and exploit scenarios are conducted strictly for educational and research purposes in an authorized test environment.

## Table of Contents

- [Project Overview](#project-overview)
- [Quickstart](#quickstart)
- [Challenges Documentation](#challenges-documentation)
  - [1. Admin Registration](#1-admin-registration)
  - [2. Deluxe Fraud](#2-deluxe-fraud)

## Project Overview

This repository contains the documentation of 2 challenges from the OWASP Juice Shop. Each challenge represents a different type of attack.
The goal of this project is to carry out selected attacks against the fictional shop, understand how and why each attack works, identify the risks that can arise during software development, and derive what needs to be considered to prevent them.

## Quickstart

- Download and install VirtualBox:

```bash
sudo apt update && sudo apt install -y virtualbox
```

- Create a virtual machine and install Kali Linux:
https://www.kali.org/get-kali/#kali-platforms

- Clone the Juice Shop repository:

```bash
git clone git@github.com:juice-shop/juice-shop.git && cd juice-shop   
```

- Install dependencies:

```bash
sudo apt update && sudo apt install -y nodejs npm    
```

- Install and start Juice Shop:

```bash
npm install && npm start
```
   

- Open your browser at `127.0.0.1:3000`

- Try out the challenges yourself

## Challenges Documentation

### 1. Admin Registration

**Category:** Improper Input Validation (Mass Assignment)

By manipulating the registration request, a new user account can be created with administrator privileges — a role that is not assignable through the regular registration form.

📄 [Full documentation](Challenges/admin-registration/README.md)

### 2. Deluxe Fraud

**Category:** Broken Access Control (Business Logic Flaw)

By manipulating the payment request, a Deluxe Membership can be obtained without an actual payment being processed.

📄 [Full documentation](Challenges/deluxe-Fraud/README.md)

---
*This documentation is for educational purposes only, as part of a structured security training exercise.*
