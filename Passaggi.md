# Creazione web server con Raspberry Pi4 con Linux Ubuntu Server e router Linkem :computer:
L'obiettivo finale sarà quello di creare un sito raggiungibile da remoto e un disco di rete NAS.<br>
La configurazione iniziale del Raspberry verrà effettuata in maniera headless, tramite pc
portatile e connessione SSH.<br>
<br>
Per questo progetto abbiamo bisogno di:
>            router Linkem con connessione a internet
>            pacchetto completo Raspberry Pi4
>                  scheda madre completa
>                  alimentatore 15W circa (5.1V o 3.3V)
>                  ventola di raffreddamento e dissipatori
>                  case di protezione
>                  cavi di collegamento HDMI
>                  micro sd almeno 8GB con adattatore per PC
>            computer portatile
>            cavo ethernet (opzionale)
>            software Rufus installato sul pc
>            software Putty installato sul pc
>            software winSCP installato sul pc (usato per trasferire i file da windows al server)
>            ssd o hard disk > 256 GB

[INSTALLAZIONE RASPBERRY PI4](#INSTALLAZIONE-RASPBERRY-PI4)<br>
[INSTALLAZIONE RUFUS](#INSTALLAZIONE-RUFUS)<br>
[DOWNLOAD SISTEMA OPERATIVO](#DOWNLOAD-SISTEMA-OPERATIVO)<br>
[PERSONALIZZAZIONE HOSTNAME](#PERSONALIZZAZIONE-HOSTNAME)<br>
[INSTALLAZIONE PACCHETTI NECESSARI](#INSTALLAZIONE-PACCHETTI)<br>
[CONFIGURAZIONE DI RETE](#CONFIGURAZIONE-DI-RETE)<br>
[CREAZIONE UTENTI](#CREAZIONE-UTENTI)<br>
[CREAZIONE SPAZIO SITI](#CREAZIONE-SPAZIO-SITI)<br>
[CONTROLLO FILE APACHE2](#CONTROLLO-FILE-APACHE2)<br>
[CREAZIONE FILE DI CONFIGURAZIONE DEL SITO](#CREAZIONE-FILE-DI-CONFIGURAZIONE-DEL-SITO)<br>
[CREAZIONE SITO](#CREAZIONE-SITO)<br>
[CERTIFICATO SSL](#CERTIFICATO-SSL)<br>
[INSTALLAZIONE SAMBA](#INSTALLAZIONE-SAMBA)<br>
[CONFIGURAZIONE FTP](#CONFIGURAZIONE-FTP)<br>
[CREAZIONE DISCO DI RETE NAS](#CREAZIONE-DISCO-DI-RETE-NAS)<br>


## INSTALLAZIONE RASPBERRY PI4
### tempo d'esecuzione: 15 min
Una volta acquistato il Raspberry Pi4 dovremo assemblare i componenti.
Le istruzioni contenute all'interno della scatola sono chiare e basilari. Qualora 
non fossero presenti si possono scaricare dal sito ufficiale.<br>

### checkpoint :white_check_mark: <br>
Una volta assemblato saldamento e precisamente il tutto collegare il Raspberry 
alla rete elettrica con il relativo alimentatore e controllare che si accenda
correttamente (la ventola a 5.1V (pin 2 e 6) sarà discretamente rumorosa).Uno dei due led diverrà rosso a conferma che la scheda è correttamente alimentata. <br>

---------------------------------------------------------------------

## INSTALLAZIONE RUFUS
### tempo d'esecuzione: 5 min
Per portare a termine il nostro progetto è necessario installare il software 
Rufus dal [sito ufficiale](https://rufus.ie/). Questa applicazione permetterà di
creare un supporto di memoria esterna contentente un ISO avviabile tramite BOOT<br>

---------------------------------------------------------------------

## DOWNLOAD SISTEMA OPERATIVO
### tempo d'esecuzione: 20 min
A questo punto sarà necessario scaricare il S.O., in questo caso Ubuntu Server 20.04.1 LTS
(long term support) a 64 bit dal [sito ufficiale](https://ubuntu.com/download/raspberry-pi).
Una volta scaricato un file .zip con all'interno l'immagine ISO del S.O. scelto, inserire la scheda micro sd con il relativo adattatore per pc all'interno del nostro
pc e aprire l'applicazione Rufus.
All'interno della schermata di Rufus selezionare l'immagine ISO, cliccare AVVIO e aspettare. La scheda sd verrà formattata.<br>
<br>
### checkpoint :white_check_mark: <br>
Al termine del caricamento cliccare CHIUDI. Usiamo, poi, la combinazione di tasti Windows+R e scriviamo diskmgmt.msc per assicurarci che siano state create due partizioni una (BOOT) formattata FAT32, l'altra non leggibile in Windows.<br>
<br>
Nella finestra Questo PC di Windows, entriamo nella scheda sd e clicchiamo con il tasto destro in un'area libera della cartella, scegliamo Nuovo, Documento di testo. Assegnamo al file così creato il nome ssh assicurandovi che non sia presente l'estensione .txt<br>
Allo stesso modo, creiamo un file wpa_supplicant.conf nella partizione BOOT inserendovi al suo interno quanto segue (può essere aperto con un qualunque editor di testo, va bene anche il Blocco Note di Windows):

>           country=IT
>           ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
>           update_config=1
>
>           network={
>           scan_ssid=1
>           ssid="SOSTITUIRE_SSID"
>           psk="SOSTITUIRE_PASSWORD"
>           }

Al posto di SOSTITUIRE_SSID, indichiamo il nome della WiFi alla quale Raspberry Pi 4 dovrà automaticamente collegarsi; la stringa SOSTITUIRE_PASSWORD va sostituita con la password corretta per l'accesso alla rete WiFi specificata. Noi per sicurezza collegheremo, comunque, il Raspberry al router tramite cavo ethernet.
Estraiamo la scheda SD dal PC e inseriamola nello slot posto al di sotto del Raspberry Pi 4.<br>
<br>
Colleghiamo il cavo ethernet dal router al Raspberry, assicuriamoci che la sd sia inserita correttamente e dopo aver collegato il Raspberry alla rete elettrica aspettiamo qualche secondo.
<br>
Per accedere alla sessmione ssh è necessario sapere l'indirizzo IP privato assegnato dal router al Raspberry Pi4. Per conoscere l'indirizzo IP digitare sul browser del proprio pc 192.168.1.1 e accedere con le credenziali (se non è mai stato effetuato l'accesso cercare sul browser "user e password router linkem"). Nella sezione network del menù, all'interno di Stato, scorrendo in fondo alla pagina sarà visibile l'indirizzo IP del Raspberry (il seguente record potrebbe avere "ubuntu" come nome host).<br>
Venuti a conoscenza dell'ip del raspberry entrare su Putty, scaricabile dal [sito ufficiale](https://www.putty.org/) e digitare l'ip nella relativa casella, e verificare che la porta sia la "22". <br>
Al primo accesso verrà richiesto username e password (inserire "ubuntu" sia come password che come username). Verrà poi richiesto all'utente di inserire una password personale per l'accesso. Consigliamo di cambiare hostname con il comando:<br>

>sudo hostnamectl set-hostname nomepc
> 
>sudo reboot
>

---------------------------------------------------------------------

## PERSONALIZZAZIONE HOSTNAME
### tempo d'esecuzione: 5 min

>sudo hostnamectl set-hostname nuovohostname
>
>sudo reboot
>

oppure 

>sudo nano /etc/hostname
>
>sudo nano /etc/hosts
>
>sudo reboot
>

### checkpoint :white_check_mark: <br>
All'avvio l'hostname sarà cambiato

---------------------------------------------------------------------

## INSTALLAZIONE PACCHETTI
### tempo d'esecuzione: 5 min
:warning: pacchetto APACHE2 per scaricare server APACHE2 con FileZilla<br>
:warning: pacchetto OPENSSH-SERVER per comandi FTP con FileZilla<br>
:warning: pacchetto VSFTPD per comandi FTP con FileZilla

>sudo apt update
>
>sudo apt-get install apache2
>
>sudo apt install openssh-server
>
>sudo apt-get install vsftpd

### checkpoint :white_check_mark: <br>
controllare connettività dopo aver installato APACHE2
>digitare ip su barra di ricerca nel browser
>

---------------------------------------------------------------------

## CONFIGURAZIONE DI RETE
### tempo d'esecuzione: 10 min
:warning: in caso di virtualizzazione è necessario porre scheda in bridge

>cd /etc/netplan/00-installer-config.yaml


>           network:
>                 ethernets:
>                       eth0:
>                              dhcp4: false
>                              optional: false
>                              addresses: [192.168.1.2/24]
>                              gateway4: 192.168.1.1
>                              nameservers:
>                              addresses: [8.8.8.8, 8.8.4.4]
>                 version: 2


Per accettare configurazione appena impostata: <br>
>sudo netplan try

>     [sudo] password for adminuser:
>     Warning: Stopping systemd-networkd.service, but it can still be activated by:
>       systemd-networkd.socket
>     Do you want to keep these settings?
>
>
>     Press ENTER before the timeout to accept the new configuration
>
>
>     Changes will revert in 119 seconds
>     Configuration accepted.

### checkpoint :white_check_mark: <br>
controllare corretta configurazione di rete: <br>
>ip address
>

---------------------------------------------------------------------

## CREAZIONE UTENTI
### tempo d'esecuzione: 5 min
:warning: Per questione di semplicità creeremo gli utenti solo dopo aver creato il sito. <br>

>sudo useradd -s /bin/bash -d /var/www/SitoX -m usersitoX
>
>sudo passwd usersitoX
>

### checkpoint :white_check_mark: <br>
controllare inserimento corretto per accesso unico alla cartella
>exit
>
>         accedere con user e passwd inserite
>

---------------------------------------------------------------------

## CREAZIONE SPAZIO SITI
### tempo d'esecuzione: 5 min
:warning: Lo spazio dei siti può essere creato dall'utente creato precedentemente da root.<br>

>sudo mkdir /var/www/ SitoA
>
>sudo mkdir /var/www/SitoA log
>
>sudo mkdir /var/www/SitoA web
>

--------------------------------------------------------------------

## CONTROLLO FILE APACHE2
### tempo d'esecuzione: 2 min
:warning: controllare che vengano gestiti i file dentro apache2.<br>

>cat cd /etc/apache2/apache2.conf
>

>
>       <>Directory /var/www/>
>            Options Indexes FollowSymLinks
>            AllowOverride None
>            Require all granted
>       </Directory>
>

---------------------------------------------------------------------

## CREAZIONE FILE DI CONFIGURAZIONE DEL SITO
### tempo d'esecuzione: 5 min

>cd /etc/apache2/sites-available
>
>sudo cp 000-deafult.conf 001-default.conf
>
>sudo nano 001-deafult.conf
>
>       DocumentRoot /var/www/SitoA
>
>       ServerName dominio
>
>       ErrorLog /var/www/SitoA/log/error.log
>
>       CustomLog /var/www/SitoA/log/access.log combined
>

---------------------------------------------------------------------

## CREAZIONE SITO
### tempo d'esecuzione: 15 min

dopo aver creato un file index.html nella cartella web dello spazio personale
e dopo aver creato la cartella log (var/www/Sito/web e var/www/Sito/log):<br>

>systemctl reload apache2
>

### checkpoint :white_check_mark: passaggi avvenuti con successo:
>         Authentication is required to reload 'apache2.service'.
>         Authenticating as: adminuser
>         Password:
>         ==== AUTHENTICATION COMPLETE ===
>

>sudo a2ensite 001-default.conf
>

>         Enabling site hm18858-default.
>         To activate the new configuration, you need to run:
>           systemctl reload apache2

### :warning: passaggi non avvenuti con successo

>         [sudo] password for adminuser:
>         Job for apache2.service failed.
>         See "systemctl status apache2.service" and "journalctl -xe" for details.
>

visonare utente che ha generato errore: <br>
>apache2ctl configtest
>

disattivare file dell'utente incriminato:
>sudo a2dissite <fileconf.conf>
>

>         Site hm18858-default disabled.
>         To activate the new configuration, you need to run:
>           systemctl reload apache2

### checkpoint :white_check_mark: controllo finale sul raggiungimento del sito <br>
comandi utili:
>digitare sul browser servername
>
>apache2 is not active -> sudo systemctl enable apache2
>
>cat /var/log/syslog
>
>history | grep enable
>
>sudo systemctl restart apache2.service
>
>sudo systemctl reload apache2
>
  
--------------------------------------------------------------------

## CERTIFICATO SSL
### tempo d'esecuzione: 15 min
Il certificato SSL ci permetterà di avere un sito considerato protetto tramite la creazione di un certificato SSL
che verrà rinnovato automaticamente. 
Per il nostro certificato SSL useremo i comandi di Certbot lets-encrypt dal [sito ufficiale](https://certbot.eff.org/lets-encrypt/ubuntufocal-apache.html). <br>

>sudo apt-get update
>
>sudo apt-get install snapd
>
>sudo snap install core
>
>sudo snap refresh core
>
>sudo snap install --classic certbot
>
>sudo ln -s /snap/bin/certbot /usr/bin/certbot
>
>sudo certbot --apache
>
>sudo nano /etc/apache2/sites-enabled/000-default.conf


>             <VirtualHost *:80>
>                  # The ServerName directive sets the request scheme, hostname and port that
>                  # the server uses to identify itself. This is used when creating
>                  # redirection URLs. In the context of virtual hosts, the ServerName
>                  # specifies what hostname must appear in the request's Host: header to
>                  # match this virtual host. For the default virtual host (this file) this
>                  # value is not decisive as it is used as a last resort host regardless.
>                  # However, you must set it for any further virtual host explicitly.
>                  #ServerName www.example.com
>
>                  ServerAlias dominio
>                  ServerName www.dominio
>                  ServerAdmin webmaster@localhost
>                  Redirect / https://www.dominio
>                  ErrorLog /var/www/utente/log/error.log
>                  CustomLog /var/www/utente/log/access.log combined
>
>                  # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
>                  # error, crit, alert, emerg.
>                  # It is also possible to configure the loglevel for particular
>                  # modules, e.g.
>                  #LogLevel info ssl:warn
>
>                  ErrorLog ${APACHE_LOG_DIR}/error.log
>                  CustomLog ${APACHE_LOG_DIR}/access.log combined
>
>                  # For most configuration files from conf-available/, which are
>                  # enabled or disabled at a global level, it is possible to
>                  # include a line for only one particular virtual host. For example the
>                  # following line enables the CGI configuration for this host only
>                  # after it has been globally disabled with "a2disconf".
>                  #Include conf-available/serve-cgi-bin.conf
>             RewriteEngine on
>             RewriteCond %{SERVER_NAME} =dominio
>             RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
>             </VirtualHost>

:warning: dopo aver svolto i passaggi per la certificazione verrà creato un file di
configurazione /etc/apache2/sites-enabled/000-default-le-ssl.conf <br>
Assicurasi che le impostazioni siano corrette.<br>

>sudo certbot --apache
>
>sudo certbot renew --dry-run
>


>            Processing /etc/letsencrypt/renewal/dominio.conf
>            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
>            Cert not due for renewal, but simulating renewal for dry run
>            Plugins selected: Authenticator apache, Installer apache
>            Simulating renewal of an existing certificate for dominio
>            Performing the following challenges:
>            http-01 challenge for dominio
>            Waiting for verification...
>            Cleaning up challenges
>
>            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
>            new certificate deployed with reload of apache server; fullchain is
>            /etc/letsencrypt/live/dominio/fullchain.pem
>            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
>
>            - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
>            Congratulations, all simulated renewals succeeded:
>              /etc/letsencrypt/live/dominio/fullchain.pem (success)

### checkpoint :white_check_mark: <br>
Andando sul browser e digitando il nostro dominio, dopo aver cliccato invio dovremo vedere il lucchetto chiuso
che indica che il nostro sito è protetto. Infine possiamo scrivere http://dominio e assicurarci che il dominio
venga reindirizzato con lo standard https.<br>

---------------------------------------------------------------------

## INSTALLAZIONE SAMBA
### tempo d'esecuzione: 15 min
Un file server Samba consente la condivisione di file tra diversi sistemi operativi su una rete.<br>
Ti consente di accedere ai file del desktop da un laptop e di condividere file con utenti Windows e macOS.<br>
<bR>
Di cosa avremo bisogno:
- Ubuntu 16.04 LTS
- Una rete locale (LAN) su cui condividere file

>sudo apt update
>
>sudo apt install samba
>
>whereis samba
>

L'output dovrebbe essere il seguente: :white_check_mark:
>     samba: /usr/sbin/samba /usr/lib/samba /etc/samba /usr/share/samba /usr/share/man/man7/samba.7.gz /usr/share/man/man8/samba.8.gz
>

>mkdir /home/<username>/sambashare/
>
>sudo nano /etc/samba/smb.conf

File di configurazione: 
>     [sambashare]
>
>         comment = Samba on Ubuntu
>
>         path = /home/username/sambashare
>
>         read only = no
>
>         browsable = yes
>

>sudo service smbd restart
>
>sudo service nmbd restart
>
>sudo ufw allow samba
>
>sudo smbpasswd -a username
>

Su Ubuntu: apri il file manager predefinito e fai clic su Connetti al server, quindi inserisci:<br>
>smb://ip-address/sambshare
>

Su macOS: nel menu Finder, fai clic su Vai> Connetti al server, quindi inserisci:<br>
>smb://ip-address/sambshare
>

Su Windows, apri File Manager e modifica il percorso del file in:<br>
>\\ip-address\sambashare
>

------------------------------------------------------------

## CONFIGURAZIONE FTP
### tempo d'esecuzione: 10 min
:warning: configurare file vsftpd per usufruire dei comandi FTP da remoto e scambiare file.<br>
>sudo nano /etc/vsftpd.conf
>

>
>     listen=yes
>
>     listen_ipv6=NO
>
>     anonymous_enable=NO
>
>     local_enable=YES
>
>     write_enable=YES
>
>     local_umask=022
>
>     dirmessage_enable=YES
>
>     use_localtime=YES
>
>     xferlog_enable=YES
>
>     connect_from_port_20=YES
>
>     xferlog_file=/var/log/vsftpd.log
>
>     xferlog_std_format=YES
>
>     ftpd_banner=Welcome to our  FTP service.
>
>     chroot_local_user=YES
>
>     local_root=/var/www/$USER
>
>     user_sub_token=$USER
>
>     allow_writeable_chroot=YES
>
>     secure_chroot_dir=/var/run/vsftpd/empty
>
>     pam_service_name=vsftpd
>
>     rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
>
>     rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
>
>     ssl_enable=NO
>
>     session_support=YES
>
>     log_ftp_protocol=YES
>

---------------------------------------------------------------------

## CREAZIONE DISCO DI RETE NAS
### tempo d'esecuzione: 15 min
:warning: Per cause di forze maggiori non configureremo un array RAID.
La scheda ssd o hard disk esterno verrà formattato. Prima dell'operazione è necessario spostare i file
importanti memorizzati su di esso su un altro supporto di memoria.<br>
Innanzitutto è bene controllare gli aggiornamenti dei pacchetti già installati.

>sudo apt get update && sudo apt -y upgrade
>

Digitando il seguente comando si otterrà la lista delle unità USB collegate al Raspberry Pi4.

>sudo blkid
>

>sudo apt update && sudo apt -y upgrade
>
>sudo apt-get install mdadm -y
>

### RAID 1<br>

>sudo mdadm --create --verbose /dev/md/vol1 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1
>

Confermare RAID, salvare configurazione e formattare con ext4 <br>

>sudo mdadm --detail /dev/md/vol1
>
>sudo -i
>
>mdadm --detail --scan >> /etc/mdadm/mdadm.conf
>
>exit
>

### checkpoint :white_check_mark: <br>
Digitando:<br>

>sudo nano /etc/mdadm/mdadm.conf
>

e scorrendo il file, si dovrebbe trovare il riferimento: <br>

>         ARRAY /dev/md/vol1
>

formattiamo e creiamo cluster grandi 4096 KB:<br>

>sudo mkfs.ext4 -v -m .1 -b 4096 -E stride=32,stripe-width=64 /dev/md/vol1
>

montiamo il volume e verifichiamo:<br>

>sudo mount /dev/md/vol1 /mnt
>
>sudo blkid
>
>sudo nano /etc/fstab
>

e modifichiamo:<br>

>       UUID="uuid_vol_raid_chetroviamoconBLKID" /mnt ext4 defaults 0 0
>

### Samba per condividere cartella in locale <br>

>sudo chown -R "utente" /mnt
>
>sudo chmod -R 0777 /mnt
>
>sudo smbpasswd -a "utente"
>
>sudo nano /etc/samba/smb.conf
>

in fondo al file scriviamo:

>             [NAS]
>             path = /mnt
>             comment = Raspberry Pi NAS
>             force users = "utente"
>             writeable = yes 
>             browseable = yes
>             create mask = 0777
>             directory mask = 0777
>             public = no

>sudo systemctl restart smbd
>
>sudo systemctl restart nmbd 
