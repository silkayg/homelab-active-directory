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


## Sitzung 2/3: Client-VM und Domain-Join

### Vorbereitung
- Windows 11 Enterprise Evaluation ISO heruntergeladen (90 Tage)
- VM `client01` angelegt: 4 GB RAM, 2 vCPU, 50 GB HDD, EFI + TPM 2.0
- Internes Netzwerk `ad-lab` als einziger Adapter
- "Skip Unattended Installation" aktiv

### Stolpersteine
- Windows 11 zwingt im OOBE zur Microsoft-Account-Anmeldung. Umgangen
  mit `oobe\bypassnro` aus der Eingabeaufforderung — danach lokaler
  User `localadmin` möglich.
- TPM-Prüfung im Setup wäre fehlgeschlagen, weil [hier eintragen falls
  passiert]. Lösung: Registry-Workaround unter
  HKLM\SYSTEM\Setup\LabConfig.

### Netzwerk
- Statische IP: `192.168.56.20/24`
- Default Gateway: `192.168.56.10` (DC)
- DNS-Server: `192.168.56.10` (DC ist DNS-Authority für lab.local)
- Verifiziert: `ping 192.168.56.10` und `nslookup lab.local` erfolgreich

### Domain-Join
- Per PowerShell: `Add-Computer -DomainName "lab.local" -Restart`
- Anmeldung mit `lab\administrator`
- Nach Reboot: Login als Domänen-User `lab\aadmin` erfolgreich

### GPO-Verifikation
- `gpresult /r` zeigt `GPO-Mitarbeiter-Desktop` als angewendet
- Bildschirmschoner-Pflicht ist aktiv

## Lessons Learned (gesamt)
- Active-Directory-Aufbau verstanden: Forest, Domain, OU, User, Gruppe, GPO
- Praktischer Unterschied zwischen Windows-Editionen Pro/Enterprise
  (domänenfähig) vs. Home (nicht domänenfähig)
- DNS ist im AD-Kontext kritisch — der DC muss DNS-Server für die Clients
  sein, sonst funktioniert keine Domänen-Auflösung
- Internes VirtualBox-Netzwerk simuliert ein abgeschlossenes Firmennetz
  ohne Internetverbindung — für AD-Lab ideal, weil sauber abgeschottet
- Windows 11 OOBE-Bypass per `oobe\bypassnro` für lokale Konten
- A-G-DL-P-Schema für AD-Berechtigungen verstanden

