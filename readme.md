# 🚀 Deploy a Full-Stack React + Java Application on DigitalOcean

This repository contains a application built with **React** and **Java**.

This guide walks through deploying the app on a DigitalOcean Droplet: configuring the firewall, installing Java, building the project, and running the application.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [1. Create a DigitalOcean Droplet](#1-create-a-digitalocean-droplet)
- [2. Generate and Add an SSH Key](#2-generate-and-add-an-ssh-key)
- [3. Configure the DigitalOcean Firewall](#3-configure-the-digitalocean-firewall)
- [4. SSH Into the Droplet](#4-ssh-into-the-droplet)
- [5. Create a Non-Root User](#5-create-a-non-root-user)
- [6. Install Java 17](#6-install-java-17)
- [7. Build the Java Backend Locally](#7-build-the-java-backend-locally)
- [8. Transfer the JAR to the Droplet](#8-transfer-the-jar-to-the-droplet)
- [9. Run the Java Application](#9-run-the-java-application)
- [10. Open the Application in a Browser](#10-open-the-application-in-a-browser)
- [Author](#author)


---

## Overview

This project demonstrates how to deploy a Java app on DigitalOcean. The backend runs on **Java 17**, is built with **Gradle**, and is exposed on **port 7071**.


---


## Prerequisites

- A DigitalOcean account
- SSH installed locally
- Java 17 installed locally
- Gradle installed locally
- This repository cloned locally

---

## 1. Create a DigitalOcean Droplet

1. Log in to DigitalOcean.
2. Click **Create → Droplet**.
3. Choose region and image (Ubuntu recommended — e.g., 22.04 LTS).
4. Choose plan/size. (Basic/CPU Option:Regular/)
5. Under **Authentication**, add your SSH key (see [step 2](#2-generate-and-add-an-ssh-key) if you don't have one yet).
6. Click **Create Droplet**.

<img src="/images/region-image.png" width="600">

---

## 2. Generate and Add an SSH Key

If you don't already have an SSH key, generate one locally:

```bash
ssh-keygen -t ed25519 -C "digitalocean"
```

Add the **public** key (`~/.ssh/id_ed25519.pub`) to your DigitalOcean account or directly to the Droplet during creation.
<img src="/images/sshkey.png" width="600">

---

## 3. Configure the DigitalOcean Firewall

Navigate to **Networking → Firewalls → Create Firewall**.


### Required Rules

**🔐 SSH (Port 22)**
- Remove the default "All IPv4" / "All IPv6" sources.
- Add **only your own IP address**.
<img src="/images/edit-inbound-rule.png" width="600">



**🌐 Java App (Tomcat server for this App is Port 7071)**
- Add a Custom TCP rule for port `7071`.
- Source: All IPv4 (or restrict to specific IPs if you don't need public access).

Name the firewall (e.g., `my-droplet-firewall`) and attach it to your Droplet.

---

## 4. SSH Into the Droplet

```bash
ssh root@YOUR_DROPLET_IP
```

---

## 5. Create a Non-Root User

You'll need root access once to set things up, but you shouldn't keep using the root account for day-to-day work — installing packages, building, and running the app. If anything on the server is ever compromised, an account without root privileges limits the damage.

Create a new user:

```bash
adduser deploy
```

Grant it `sudo` privileges:

```bash
usermod -aG sudo deploy
```

Switch to the new user:

```bash
su - deploy
```

From here on, run all commands as `deploy` instead of `root`.


