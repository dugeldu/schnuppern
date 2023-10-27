# Schnuppern

## Gemeindeverwaltung Brienz 

Schnuppern
 
Informatiker EFZ Plattformentwicklung 

Beaufsichtig durch:
Josia Nietlispach, Sören Stegitz
 
## Auftrag:

Der Auftrag ist es einen Proxmox Server aufzusetzen und zu konfigurieren. Anschliessend wirst du auf dem Proxmox Server eine Virtuelle Maschine erstellen und einrichten. Die Virtuelle Maschine wird als Docker Server konfiguriert. In Docker können dann verschiedene Dienste, wie Websites, Home-Assistant, Passwort-Vaults, usw betrieben werden. Es gibt praktisch keine Grenzen.

In diesem Auftrag wird öfters der Begriff «Virtuelle Maschine» vorkommen. Wir kürzen ihn daher ab nun mit «VM» ab.

Hilfsmittel:

- Internet
- Mitarbeiter

Wir fangen mit der Installation von [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE) an. Es ist die Grundlage des Projektes.

## Installation Proxmox:

1.  Nimm den von uns zur Verfügung gestellten USB Stick und stecke ihn in einen USB Port des PCs.
1.  Boote nun von dem USB Stick. Um das zu tun starte den PC und drücke während des startens mehrmals die F12 Taste. Dies machst du solang bis du in das sogenannte Boot Menu kommst.
1.  Hier kannst du nun mit Pfeiltasten navigieren. Navigiere nach unten bis du bei UEFI USB SanDisk.... landest und bestätige dies mit <kbd>Enter</kbd>
1.  Sobald [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE) gestartet hat kannst du auf "Install Proxmox VE (Graphical)" drücken
1.  Bei der EULA klickst du auf "I agree"
1.  Beim nächsten Fenster kannst du unten auf "Next" klicken
1.  Bei Location and Timezone bei Country "Switzerland" auswählen und beim Tastatur Layout auf "Swiss-German" und dann weiter auf "Next"
1.  Als Passwort "Welcome.2022" setzen und das Passwort bei "Confirm Password" nochmals wiederholen und bei der Email "schnuppernbrienz@gmail.com" eingeben, dann "Next"
1.  Netzwerkkonfiguration kannst du auch auf Standard belassen, also einfach auf "Next"
1.  Zuletzt nun noch auf "Install"
1.  Jetzt dauert es ein Bisschen, bis Proxmox installiert ist
---

Glückwunsch. Du hast erfolgreich [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE) konfiguriert.
Auf dem Proxmox-Server steht nun die Anweisung, dass du über den Webbrowser auf das Webgui von Proxmox gehen sollst.
Schreibe dir die IP-Adresse nun auf.
https://IP:8006

Nun kannst du den zweiten PC starten. 
Gleichzeitig steckst das Displayportkabel, das Kabel der Maus und das der Tastatur vom linken in den rechten grossen PC.
Wenn du Hilfe brauchst -> ungeniert fragen :)

Dort öffnest du nun ein Browserfenster im Google Chrome und gibst oben im Suchfeld die eben aufgeschriebene Adresse ein.
"_https://172.18....:8006_"
Damit greifst du auf deinen Proxmox Server zu über das Netz. Hier werden wir dann einige spannende Dinge einrichten ;)

Melde dich als "_root_" mit dem Passwort, dass du vorhin gesetzt hast an ("_Welcome.2022_")
Du bist nun auf dem Webgui von [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE). Von hier aus kannst du Virtuelle Maschinen erstellen, konfigurieren, überwachen und vieles mehr. Schau dich gerne ein wenig um.

Damit wir später eine VM kreeiren können, brauchen wir noch ein ISO-Image, das später auf der VM installiert werden soll. Wir nehmen dafür ein Ubuntu-Image, das ich bereits auf deinem Laptop platziert habe.

Diese musst du nur noch hochladen auf deinen Proxmox-Server. 
Klicke dich dafür zunächst durch die Baumstruktur oben rechts.
Datacenter > pve

Dort auf "_local (pve)_" klicken.
Dann auf "_ISO Images_" -> Upload -> Select File -> Downloads -> Klick auf ubuntu-22.04.3-live-server-amd64 -> "_öffnen_" -> "_Upload_"

Klicke nun oben rechts auf Create [VM](https://de.wikipedia.org/wiki/Virtuelle_Maschine) und kreeire eine [VM](https://de.wikipedia.org/wiki/Virtuelle_Maschine) mit genau diesen Angaben 

- General:
  - Node: so belassen wie es ist
  - VM ID: 100
  - Name: Docker
  - Resource Pool: leer lassen
- OS:
  - Storage: local
  - ISO-Image: ubuntu-22.04.1-live-server-amd64.iso
  - Type: Linux
  - Version: 6.x -2.6 Kernel
- System:
  - Qemu Agent ankreuzen, den Rest so lassen wie es ist
- Disks:
  - Disk Size (GiB): 100 GB, Rest beim Standard belassen
- CPU:
  - Sockets: 1
  - Cores: 8
  - Rest: Standard
- Memory:
  - Memory (MiB): 8192
- Network:
  - Alles so belassen
 
- Confirm:
  - Start after created ankreuzen
  - Dann auf "_Finish_"

Super. Du hast nun deine erste [VM](https://de.wikipedia.org/wiki/Virtuelle_Maschine) erstellt. Starte diese. Das Linux auf der VM muss jetzt noch eingerichtet werden, damit wir anschliessend den Dienst [Docker](<https://de.wikipedia.org/wiki/Docker_(Software)>) auf ihr installieren können.

Nun klickst du oben rechts in der Baumstruktur auf deine VM; 100 (Docker)
Dort dann auf Console klicken.

Nun "_Try or Install Ubuntu anwählen_".

Folgendermassen gehst du nun durch die Installation deines Ubuntu-Servers
1.  Sprache: English
1.  Continue without updating
2.  Hier alles belassen wie es ist und einfach auf "_Done_"
3.  Beim nächsten Fenster sicherstellen, dass oben "_Ubuntu Server_" angekreuzt ist -> Done
4.  Bei den Netzwerkeinstellungen auch alles so lassen wie es ist -> Done
5.  Bei Proxy auch lassen -> Done
6.  Mirror auch lassen -> Done
7.  Beim Fenster "_Guided storage configuration_" musst du auf die Pfeiltaste nach unten klicken, bis die Option "_Done_" grün markiert ist. Danach Enter
9.  Storage Configuration -> Done
10.  Continue

> **Hinweis: Schreibe dir die IP der VM auf, diese wirst du später brauchen.**

Als finalen Schritt musst du nun die Benutzerangaben konfigurieren. Mache dies wie folgt:

- Name: Dein Name
- Servername: docker
- Username: sysadmin
- Passwort: Welcome.2022
> **Hinweis: Überprüfe deine Angaben nochmals, und stelle sicher dass alles richtig ist. Womöglich sind Y und Z bei deinen Eingaben vertauscht**
- Nachdem du deine Angaben korrigiert hast:
- Upgrade: "_Skip for now_" -> Done
- Install OpenSSH server: Ankreuzen mit der Leertaste, danach mit der Pfeiltaste zu "_Done_" navigieren
- Featured Server Snaps: Keine ankreuzen -> Done

Die VM installiert sich nun. Dies dauert einen Moment.
Sobald die Installation abgeschlossen ist, machst du folgendes:
- Klick im Menü links im Proxmox Webgui unter "_Console_" kurz auf "_Hardware_"
- Dort auf CD/DVD Drive
- Und dort entfernst du unser Ubuntu Image mithilfe von "_Remove_", wenn du dir unsicher bist -> besser fragen
- Nun gehst du wieder zurück zu "_Console_"
- Dann die soeben installierte VM neu starten mit "Reboot Now".


> **Nun konsultiere mich bitte kurz, dann verbinden wir uns per SSH mit der VM mit deinem Laptop.**

---
Jetzt sollte dort "_docker login_" stehen.
Dann loggst du dich ein mit den zuvor konfigurierten Benutzerangaben.

User: sysadmin

Passwort: Welcome.2022

Nun müssen wir [Docker](<https://de.wikipedia.org/wiki/Docker_(Software)>) installieren. In [Docker](<https://de.wikipedia.org/wiki/Docker_(Software)>) werden später unsere Dienste laufen.
Um [Docker](<https://de.wikipedia.org/wiki/Docker_(Software)>) zu installieren musst du die folgenden Befehle eingeben -> kopiere diese einfach ins Terminal hinein, abtippen dauert zu lange: 

```
sudo apt-get update
```

```
sudo apt-get install ca-certificates curl gnupg lsb-release
```

```
sudo mkdir -p /etc/apt/keyrings
```

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```
sudo apt-get update
```

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```
sudo service docker start
```
Jetzt wollen wir Portainer installieren. Damit können wir unsere Container über eine grafische Oberfläche verwalten.
Folgendermassen installieren wir das:

```
sudo docker volume create portainer_data
```

```
docker run -d -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Damit hast du nun deinen ersten Container erstellt, auf welchem nun Portainer läuft.
Nun kannst du auf dem PC, wo du das Proxmox-Webinterface geöffnet hast, ein weiteres Browserfenster öffnen.

https://172...:9443

Hier kannst du dich nun mit dem Benutzernamen "_admin_" und dem Passwort "_Welcome.2022_" anmelden.

>**hier gibst du noch eine anleitung an von portainer bis compose**


In diesem Feld wollen wir nun die Konfigurationen für dein erstes Docker-Projekt einfügen.
Und zwar wollen wir mit einem Container einen Dienst namens [Paperless](<https://docs.paperless-ngx.com>) zum Laufen bringen

Dafür klickst du nun auf folgenden Link:

[https://github.com/paperless-ngx/blob/dev/docker/compose/docker-compose.portainer.yml](https://github.com/paperless-ngx/paperless-ngx/blob/dev/docker/compose/docker-compose.portainer.yml)

Dort klickst du auf "_Raw_".
Nun öffnet sich ein Fenster mit all dem Geschriebenen.
Jetzt klickst du <kbd>Ctrl + A</kbd>, danach <kbd>Ctrl + C</kbd> und dann gehst du wieder zu Portainer zurück und fügst das Ganze wieder in dieses Feld ein.

Nun gibst du dem erstellten Stack noch einen Namen, z.B "Teststack".

Danach kannst du auf "_Deploy Stack_" und schon ist unser

```
-p 8000:8000 \
```

```
lscr.io/linuxserver/paperless-ngx:latest
```

Jetzt wollen wir mit einem Container einen Dienst namens [Paperless](<https://docs.paperless-ngx.com>) zum Laufen bringen
Dafür gibst du folgende Befehle ein
