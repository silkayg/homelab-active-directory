\# Homelab: Active Directory



\## Ziel

Ein kleines Active-Directory-Lab in VirtualBox aufsetzen, bestehend

aus einem Domain Controller (Windows Server 2022) und einem

Windows-Client. Übung von Domänenverwaltung, Benutzern, Gruppen,

OUs und Group Policies.



\## Status

🚧 In Arbeit — aktuelle Sitzung: Server-Installation und DC-Promotion.



\## Setup (geplant)

\- \*\*Host:\*\* Windows 11, 32 GB RAM

\- \*\*Virtualisierung:\*\* VirtualBox 7.x, Internes Netzwerk "ad-lab"

\- \*\*DC:\*\* Windows Server 2022 (Eval), 4 GB RAM, 2 vCPU, 60 GB HDD

\- \*\*Client:\*\* Windows 10/11, 4 GB RAM, 2 vCPU, 50 GB HDD

\- \*\*Domäne:\*\* lab.local



\## Sitzung 1: Server-Installation und DC-Promotion



\### Vorbereitung

\- Windows Server 2022 Eval-ISO von Microsoft Evaluation Center heruntergeladen

\- VM `dc01` in VirtualBox angelegt:

&#x20; - 4 GB RAM, 2 vCPU, 60 GB HDD

&#x20; - Adapter 1: NAT (für Internetzugriff während Setup)

&#x20; - Adapter 2: Internes Netzwerk "ad-lab" (für Domänenkommunikation)

&#x20; - "Skip Unattended Installation" aktiviert



\### Installation

\- Edition: Windows Server 2022 Standard Evaluation (Desktop Experience)

\- Custom Install auf leerer 60-GB-Festplatte



\### Konfiguration des Domain Controllers



\#### Hostname und Netzwerk

\- Hostname: `dc01`

\- Statische IP auf internem Adapter: `192.168.56.10/24`

\- DNS-Server: `127.0.0.1` (DC ist selbst DNS-Server der Domäne)



\#### Domäne

\- Forest und Domain: `lab.local`

\- NetBIOS-Name: `LAB`

\- Funktionsebene: Windows Server 2016



\#### OU-Struktur



\_Lab/

├── Mitarbeiter/

│   ├── IT/

│   ├── Buchhaltung/

│   └── Vertrieb/

├── Gruppen/

└── Computer/



\#### Benutzer und Gruppen

\- 3 Test-User pro Abteilung angelegt (per GUI und PowerShell verglichen)

\- Sicherheitsgruppe `Buchhaltung-User` mit User `bbuch` als Mitglied

\- Sicherheitsgruppe `IT-Admins` mit User `aadmin` als Mitglied



\#### Erste GPO

\- `GPO-Mitarbeiter-Desktop` mit OU `\_Lab/Mitarbeiter` verknüpft

\- Aktiviert: Bildschirmschoner-Pflicht

\- Wird in Sitzung 3 mit Client-VM verifiziert



\### Lessons Learned (Sitzung 1)

\- Unterschied zwischen Group Scopes (Domain Local, Global, Universal)

&#x20; und das A-G-DL-P-Prinzip verstanden

\- AD-Verwaltung sowohl per GUI (ADUC) als auch per PowerShell

&#x20; (`New-ADUser`, `New-ADGroup`) durchgespielt

\- DistinguishedName-Notation (DN) verstanden:

&#x20; `OU=IT,OU=Mitarbeiter,OU=\_Lab,DC=lab,DC=local`

\- Konzept der GPO-Verknüpfung mit OUs erlernt — eine GPO existiert

&#x20; unabhängig, wirkt aber erst durch Verknüpfung

