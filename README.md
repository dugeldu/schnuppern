# Schnuppern

## Gemeindeverwaltung Brienz 

Schnuppern
 
Informatiker EFZ Plattformentwicklung 

Beaufsichtigung durch:
Josia Nietlispach
 
## Auftrag:

Der Auftrag ist es einen Proxmox Server aufzusetzen und zu konfigurieren. Anschliessend wirst du auf dem Proxmox Server eine Virtuelle Maschine erstellen und einrichten. Die Virtuelle Maschine wird als Docker Server konfiguriert. In Docker können dann verschiedene Dienste, wie Websites, Home-Assistant, Passwort-Vaults, usw betrieben werden. Es gibt praktisch keine Grenzen.

In diesem Auftrag wird öfters der Begriff «Virtuelle Maschine» vorkommen. Wir kürzen ihn daher ab jetzt mit «VM» ab.

Hilfsmittel:

- Internet
- Mitarbeiter

Wir fangen mit der Installation von [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE) an. Es ist die Grundlage des Projektes.

## Installation Proxmox:

- Nimm den von uns zur Verfügung gestellten USB Stick und stecke ihn in einen USB Port des PCs.
- Nun startest du den PC.

> **_INFO💡_**
> Auf dem USB-Stick befindet sich ein sogenanntes "Proxmox-Image".

- Sobald [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE) gestartet hat kannst du auf "Install Proxmox VE (Graphical)" drücken
- Bei der EULA klickst du auf "I agree"
- Beim nächsten Fenster kannst du unten auf "Next" klicken
- Bei Location and Timezone bei Country "Switzerland" auswählen und beim Tastatur Layout auf "Swiss-German" und dann weiter auf "Next"
- Als Passwort "Welcome.2024" setzen und das Passwort bei "Confirm Password" nochmals wiederholen und bei der Email "schnuppernbrienz@gmail.com" eingeben, dann "Next"
- Netzwerkkonfiguration kannst du bei "IP Address (CIDR)" die folgende Adresse eingeben: "172.18.68.38", dann auf "Next"
- Zuletzt nun noch auf "Install"
- Jetzt dauert es ein Bisschen, bis Proxmox installiert ist
- Dann auf "_Reboot_".
---

Glückwunsch. Du hast erfolgreich [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE) konfiguriert.
Auf dem Proxmox-Server steht nun die Anweisung, dass du über den Webbrowser auf das Webgui von Proxmox gehen sollst.

## Installation VM

> **_INFO💡_**
> Auf dem PC links von dir hast du nun Proxmox installiert und die Umgebung läuft nun auf dieser Maschine. Wir wollen nun von einem anderen Computer aus darauf zugreifen, konkret vom Laptop aus.
> Dafür - notiere dir gleich die IP-Adresse und den Port der auf dem Proxmox-Interface steht.
> Zum Beispiel wäre das "192.168.110.1:4008".
> Damit du den Bildschirm in der Mitte nun für den Laptop brauchen kannst, konsultiere mich kurz.

- Nun öffnest du ein Browserfenster im Google Chrome und gibst oben im Suchfeld die IP-Adresse mitsamt dem Port ein, die du soeben aufgeschrieben hast.
- Damit greifst du auf deinen Proxmox Server zu über das Netz. Hier werden wir dann einige spannende Dinge einrichten.

> **_INFO💡_**
> Nun kommt die Meldung "Dies ist keine sichere Verbindung" mit dem Vermerk, dass hier sensible Daten gestohlen werden könnten. 
> Dies ist an und für sich keine Fehlinformation, jedoch ist das hier nicht relevant, unter Anderem, da wir hier keine heiklen Daten austauschen und sich der Server in unserem Netz befindet. Damit ein Angreifer also Daten stehlen könnte, müsste er zuerst auf unser Netz zugreifen können. Wenn du allerdings im Internet auf Seiten stösst mit dieser Warnung gilt es, diese Ernst zu nehmen. Ungesicherte Webseiten sind zu vermeiden. In unserer Schnupper-Laborumgebung kannst du diese Warnung jedoch umgehen, indem du unten auf "Erweitert" und dann auf den unterstrichenen und blau markierten Text "Weiter zu ..ip.." klickst.

**Voila! Der Zugriff auf den Proxmox-Server hat nun geklappt. Gratulation**

- Melde dich als "_root_" mit dem Passwort an, dass du vorhin gesetzt hast an ("_Welcome.2024_")
- Du bist nun auf dem Webgui von [Proxmox](https://de.wikipedia.org/wiki/Proxmox_VE). Von hier aus kannst du Virtuelle Maschinen erstellen, konfigurieren, überwachen und vieles mehr.
- Schau dich gerne ein wenig um.

- Damit wir später eine VM kreieren können, brauchen wir noch ein ISO-Image, das später auf der VM installiert werden soll.
- Wir nehmen dafür ein Ubuntu-Image, das ich bereits auf deinem Laptop platziert habe.

- Dieses musst du nur noch hochladen auf deinen Proxmox-Server.
- Klicke dich dafür zunächst durch die Baumstruktur oben rechts "Datacenter > pve" und klappe "pve" aus.
- Dort dann auf "_local (pve)_" klicken.
- Dann auf "_ISO Images_" -> Upload -> Select File -> Downloads -> Klick auf ubuntu-22.04.4-live-server-amd64 -> "_öffnen_" -> "_Upload_".

Dann öffnet sich so ein Fenster, dass du einfach schliessen kannst, also nicht auf Download klicken.

Klicke nun oben rechts auf Create [VM](https://de.wikipedia.org/wiki/Virtuelle_Maschine) und kreiere eine [VM](https://de.wikipedia.org/wiki/Virtuelle_Maschine) mit genau diesen Angaben 

- General:
  - Node: so belassen wie es ist
  - VM ID: 100
  - Name: Docker
  - Resource Pool: leer lassen
- OS:
  - Storage: local
  - ISO-Image: ubuntu-22.04.4-live-server-amd64.iso
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

- Nun klickst du oben rechts in der Baumstruktur auf deine VM; 100 (Docker)
- Dort dann auf Console klicken.
- Nun "_Try or Install Ubuntu Server anwählen_".

Folgendermassen gehst du nun durch die Installation deines Ubuntu-Servers
1.  Sprache: English
2.  Continue without updating
3.  Hier alles belassen wie es ist und einfach auf "_Done_"
4.  Beim nächsten Fenster sicherstellen, dass oben "_Ubuntu Server_" angekreuzt ist -> Done
5.  Bei den Netzwerkeinstellungen auch alles so lassen wie es ist aber hier etwas wichtiges noch kurz
  > **Schreibe dir die IP der VM auf, diese wirst du später brauchen.**
1.  Wenn du die IP-Adresse aufgeschrieben hast -> Done
2.  Bei Proxy auch lassen -> Done
3.  Mirror auch lassen -> Done -> Continue
4.  Beim Fenster "_Guided storage configuration_" musst du auf die Pfeiltaste nach unten klicken, bis die Option "_Done_" grün markiert ist. Danach Enter
5.   Storage Configuration -> Done
6.   Continue

Als finalen Schritt musst du nun die Benutzerangaben konfigurieren. Mache dies wie folgt:

- Name: Dein Name
- Servername: docker
- Username: sysadmin
- Passwort: Welcome.2024
> **Hinweis: Überprüfe deine Angaben nochmals, und stelle sicher dass alles richtig ist. Womöglich sind Y und Z bei deinen Eingaben vertauscht**
- Nachdem du deine Angaben korrigiert hast: Done
- Upgrade: "_Skip for now_" -> Continue
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
## Installation Docker
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
sudo apt-get install docker-ce=5:25.0.0-1~ubuntu.22.04~jammy docker-ce-cli=5:25.0.0-1~ubuntu.22.04~jammy containerd.io docker-compose-plugin
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
sudo docker run -d -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Damit hast du nun deinen ersten Container erstellt, auf welchem nun Portainer läuft.

## Konfiguration Portainer
Nun kannst du auf dem PC, wo du das Proxmox-Webinterface geöffnet hast, ein weiteres Browserfenster öffnen.
Dort gibst du nun die aufgeschriebene IP deiner VM ein mit dem Port 9443
https://172.18.68.x:9443

Hier kannst du dich nun mit dem Benutzernamen "_admin_" und dem Passwort "_Welcome.2024_" anmelden.

Nun bist du auf der Oberfläche von Portainer.
Hier klickst du nun auf "_Get Started_".
Danach wird dir eine Umgebung angezeigt namens "_local_".
Öffne diese.

## Bereitstellung Paperless
Links hast du nun eine Menüleiste
- Klick auf "_Stacks_"
- Oben rechts dann auf "_Add Stack_"
- Namen "_teststack_" definieren
- Build Method auf "_Web editor_" belassen
- Klick in den Web editor

In diesem Feld wollen wir nun die Konfigurationen für dein erstes Docker-Projekt einfügen.
Und zwar wollen wir mit einem Container einen Dienst namens [Paperless](<https://docs.paperless-ngx.com>) zum Laufen bringen

Dafür machst du folgendes
- klick <kbd>Windows + E</kbd>
- gehe zu "_Dokumente_"
- Öffne nun das paperless.txt-File
- Nun klickst du <kbd>Ctrl + A</kbd> und markierst alles
- Danach klickst du <kbd>Ctrl + C</kbd> um es zu kopieren
- Dann gehst du wieder zu Portainer zurück und fügst das Ganze in den Web-Editor ein mit <kbd>Ctrl + V</kbd>.

Danach kannst du auf "_Deploy the Stack_" ganz unten

---
Nun wollen wir noch einen User generieren für dein Paperless.
Dafür müssen wir in die Konsole im Container drin.
- Klick auf "_Containers_" in Portainer
- Dann klickst du auf "_teststack-webserver..._"
- Unten hast du nun eine Leiste mit verschiedenen funktionen wie "_Logs_", "_Inspect_", "_Stats_" etc.
- Wir klicken hier auf "_Console_"
- Dann einfach auf "_Connect_"
- Und nun geben wir folgenden Befehl ein:
```
python3 manage.py createsuperuser
```
- Bei Username gibst du "_admin_" an.
- Bei Email einfach <kbd>Enter</kbd>
- PW: "_Welcome.2024_" wie du es dir gewohnt bist ;) 

Nun öffnest du nochmals einen neuen Tab im Chrome und tippst dort die IP von der VM und den entsprechenden Port, welcher in dem Fall 8010 ist ein.

http://172.18.68.x:8010

Dort meldest du dich nun mit deinem vorhin erstellten User an.
Nun solltest du die Oberfläche von Paperless sehen.

- Jetzt klickst du rechts bei Upload new documents auf "_Browse files_"
- Dann auf "_Desktop_"
- Danach wählst du das "_Dokument.pdf_" an
- Und dann auf "_öffnen_"
- Unter Documents im Paperless, hast du nun dein erstes Dokument hochgeladen.
- Herzlichen Glückwunsch ;)


