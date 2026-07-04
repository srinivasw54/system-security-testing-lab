# Lab Rules of Engagement

This repository documents activity performed strictly within a private
home lab for learning purposes.

1. **Ownership** — All target machines (Windows 7, Windows 10, Metasploitable2,
   DVWA) are personally owned or officially distributed for security training
   (e.g., Metasploitable2 is intentionally released by Rapid7 for this purpose).
2. **Network** — VMs were run on a local network (VirtualBox/VMware). Target
   IPs shown in screenshots (e.g. `192.168.1.x`) are on a home-router-style
   subnet rather than a fully isolated host-only network — **the recommended
   setup going forward is host-only or internal networking mode**, so
   intentionally vulnerable services (like Metasploitable2's default
   credentials or DVWA) are never reachable by other devices on the network.
3. **No third-party targets** — No scanning, exploitation, or traffic capture
   was performed against any system outside this lab.
4. **Purpose** — The goal is skill-building for red team / blue team /
   SOC analyst roles, not unauthorized access.

If you build a similar lab, always get explicit written authorization before
testing anything you do not personally own, and use host-only/internal
network adapters so vulnerable services are never exposed to your broader
home network or the internet.
