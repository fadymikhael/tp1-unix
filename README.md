## Fady Mikhael

# TP 1 : Compte Rendu d'Installation de Debian (Version Bookworm Stable)

# 1. Installation de la Machine Virtuelle

L’objectif de ce TP est de réaliser une installation minimale d’un serveur Linux en utilisant **VirtualBox** avec la distribution **Debian stable** (Bookworm). Le système cible est une architecture **amd64**.

### Récupération de l'Image d'Installation

L’image mini.iso peut être récupérée à partir du miroir Debian, par exemple :
[ftp.lip6.fr](ftp://ftp.lip6.fr/pub/linux/distributions/debian/dists/bookworm/main/installer-amd64/current/images/netboot/).

## 1.2 Installation Minimale

1. **Démarrer avec "Advanced Options" puis "Expert Install".**

2. **Langue** : Choisir l'anglais (`en_US.UTF-8`) et garder le français (`fr_FR.UTF-8`) comme locale régionale.

3. **Configuration réseau** :

   - Nommer la machine : `serveur1`
   - Domaine : `ufr-info-p6.jussieu.fr`
   - Désactiver la recherche d'IPV6.

4. **Partitionnement manuel** :

   - **/** (racine) : 10G, ext4, point de montage `/`.
   - **/tmp** : 4G, ext4, point de montage `/tmp`.
   - **/var/log** : 1G, ext4, point de montage `/var/log`.
   - **swap** : Le reste de l’espace disque pour la partition d’échange.

5. **Installation du système de base**, du noyau Linux, et configuration du gestionnaire de paquets.

6. **GRUB** : Installer GRUB sur `/dev/sda` sans forcer EFI.

7. **Finaliser et redémarrer.**

---

## 2. Configuration Post-Installation

### 2.1 Configuration SSH

1. Connectez-vous en tant que **root** sur votre machine.
2. Recherchez le paquet **SSH** à l'aide de la commande :

   ```bash
   apt search ssh
   ```

3. Installez le serveur SSH avec la commande suivante :

   ```bash
   apt install ssh
   ```

4. Modifiez la configuration de **SSH** pour autoriser les connexions root distantes avec mot de passe :

   - Ouvrez le fichier de configuration SSH :

   ````bash
    nano /etc/ssh/sshd_config
     ```
   - Trouvez et modifiez les lignes suivante :

     ```bash
     PermitRootLogin yes
     ```
     ```bash
     PasswordAuthentication yes

   - Enregistrez le fichier et quittez l'éditeur.

     Pour enregistrer vos modifications dans nano, appuyez sur **Ctrl + O**, puis appuyez sur Entrée.
   Pour quitter l'éditeur, appuyez sur **Ctrl + X**.

   ````

5. Redémarrez le service SSH pour appliquer les changements :

```bash
 systemctl restart ssh
```

### 2.2 Configurer la machine virtuelle pour accepter les connexions SSH

1. Obtenir l'adresse IP de la machine virtuelle

```bash
 ip a
```

2.  Vérifiez les paramètres réseau de la machine virtuelle

Allez dans les paramètres de la machine virtuelle dans l'onglet "Réseau", assurez-vous que l'adaptateur réseau est en mode NAT.

Cliquez sur "Avancé", puis sur "Redirection de port".

Ajoutez une nouvelle règle de redirection en configurant les éléments suivants :

- Nom : SSH
- Protocole : TCP
- Port hôte : 2222 (ou un autre port de votre choix)
- Adresse IP invité : 168.0.2.100 (l'IP de votre machine virtuelle)
- Port invité : 22
- Redémarrez la machine virtuelle.

3. Après cela, essayez de se connecter à la machine virtuelle en utilisant la commande suivante sur la machine hôte.

```bash
 ssh -p 2222 root@l168.0.2.100
```

4. Résultat obtenu

```
Linux debian 6.1.0-25-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.106-3 (2024-08-26) x86_64
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Oct  2 12:32:59 2024
```

```

```

### 2.3 Pour vérifier le nombre total de paquets installés sur le système Debian , on utilise la commande suivante :

```bash
 dpkg-l | wc-l
```

On a obtenu **350 paqutes**.

### 2.4 Utilisation de l'Espace Disque

La commande `df -h` permet d'afficher l'utilisation de l'espace disque :

```bash
 df -h
```

Résultat :

```
Filesystem      Size  Used Avail Use% Mounted on
udev            1.9G     0  1.9G   0% /dev
tmpfs           392M  568K  391M   1% /run
/dev/sda1       9.1G  1.2G  7.5G  14% /
tmpfs           2.0G     0  2.0G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/sda3       921M   38M  820M   5% /var/log
/dev/sda2       3.6G   40K  3.4G   1% /tmp
tmpfs           392M     0  392M   0% /run/user/0
```

Les principales partitions sont :

- `/dev/sda1` : 9.1G (partition principale)
- `/dev/sda3` : 921M (pour les journaux système)
- `/dev/sda2` : 3.6G (pour les fichiers temporaires)

### 2.5 Configuration de la Locale

La commande suivante permet de vérifier la locale active sur le système :

```bash
echo $LANG
```

**Résultat** : `en_US.UTF-8`

### 2.6 Nom de la Machine et Domaine

La commande suivante permet d'afficher le nom de la machine (hostname):

```bash
hostname
```

- **Nom de la machine** : `debian`

La commande suivante permet d'afficher le domaine de la machine :

```bash
hostname -d
```

- **Domaine** : `uf-info-p6.jussieu.fr`

### 2.7 Vérification des Dépôts

Pour vérifier les dépôts configurés, la commande suivante a été utilisée :

```bash
cat /etc/apt/sources.list | grep -vE '^#|^$'
```

**Résultat** :

```
deb http://ftp.lip6.fr/pub/linux/distributions/debian/ bookworm main
deb http://security.debian.org/debian-security bookworm-security main
```

### 2.8 Comptes et Mots de Passe

Les informations sur les comptes utilisateurs ont été récupérées avec la commande suivante :

```bash
cat /etc/passwd | grep -vE 'nologin|sync'
```

**Résultat** :

```
root:x:0:0:root:/root:/bin/bash
```

Les informations des mots de passe cryptés sont également disponibles dans le fichier `/etc/shadow` :

```
root:$y$j9T$h75pLtkBVtbiDdbbf1s7s0$56PBUA4ctqGAPZN404HjlepP/F6tYbyWnVIP45Z98Q7:19998:0:99999:7:::
messagebus:!:19998::::::
avahi-autoipd:!:19998::::::
sshd:!:19998::::::
```

### 2.9 Gestion des Disques

La commande `fdisk -l` permet d'afficher les informations sur les disques et partitions :

```bash
fdisk -l
```

**Résultat** :

```
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
...
/dev/sda1      2048 19531775 19529728  9.3G Linux filesystem
/dev/sda2  19531776 27344895  7813120  3.7G Linux filesystem
/dev/sda3  27344896 29298687  1953792  954M Linux filesystem
/dev/sda4  29298688 41940991 12642304    6G Linux swap
```

La commande `fdisk -x` permet d'obtenir des informations plus détaillées sur les partitions :

```bash
fdisk -x
```

**Résultat** :

```
Device        Start      End  Sectors Type-UUID                            UUID                                 Name
/dev/sda1      2048 19531775 19529728 0FC63DAF-8483-4772-8E79-3D69D8477DE4 3E604A2C-C482-4D54-ADC5-474DCB77825C la_racine
/dev/sda2  19531776 27344895  7813120 0FC63DAF-8483-4772-8E79-3D69D8477DE4 4DB04DD5-904E-4D0B-8800-D38044BE971D espace_tempo
/dev/sda3  27344896 29298687  1953792 0FC63DAF-8483-4772-8E79-3D69D8477DE4 3B2FBABE-D94D-4CBA-9987-0FFF32640C70 les_logs
/dev/sda4  29298688 41940991 12642304 0657FD6D-A4AB-43C4-84E5-0933C84B4F4F 1E60CBCE-50D5-45D4-8770-068D5FF86AAB ma_swap
```

---

## 3. Gestion Avancée des Disques et Partitions

### 3.1 Outils de Gestion des Disques

- **fdisk** : Permet de lister, créer, et supprimer des partitions.
- **parted** : Un outil avancé pour la gestion des partitions.
- **lsblk** : Affiche l’arborescence des périphériques de stockage et partitions.

### 3.2 Utilisation de LVM (Logical Volume Manager)

LVM permet de gérer dynamiquement l'espace disque. Les commandes principales incluent :

- **lvcreate** : Pour créer un volume logique.
- **lvextend** : Pour augmenter la taille d'un volume logique.
- **lvreduce** : Pour réduire la taille d'un volume.

### 3.3 Surveillance des Disques

- **iostat** : Affiche les statistiques d'entrée/sortie pour surveiller les performances des disques.
- **iotop** : Permet de visualiser les processus utilisant le plus d'E/S disque.

### 3.4 Analyse de l'Espace Disque

- **du** : Permet de vérifier l'utilisation de l'espace disque par les fichiers et dossiers.
- **ncdu** : Un outil plus interactif pour visualiser et analyser l'espace disque.

---

## 4. Automatisation de l'Installation avec Preseed

### 4.1 Qu'est-ce que le Preseed ?

Le fichier **preseed** est utilisé pour automatiser l'installation de Debian en préconfigurant les réponses aux questions posées pendant l'installation.

### 4.2 Avantages

- **Automatisation complète** de l'installation, sans intervention manuelle.
- **Standardisation** des installations, utile pour les déploiements en masse.
- **Gain de temps** considérable.

### 4.3 Exemple de Fichier Preseed

- Sélection du disque :
  ```bash
  d-i partman-auto/method string regular
  ```
- Configuration réseau :
  ```bash
  d-i netcfg/get_hostname string my-server
  ```

---

## 5. Réinitialisation du mot de passe root sur une VM Debian

## Méthode 1 : Utilisation du mode Recovery

1. Redémarrez la machine virtuelle.
2. Accédez au menu **GRUB** en appuyant rapidement sur la touche `Shift` ou `Esc` pendant le démarrage.
3. Dans le menu GRUB, sélectionnez l'option **Recovery mode** et appuyez sur `Entrée`.
4. Une fois dans le mode Recovery, un shell root sera disponible.
5. Pour changer le mot de passe root, tapez la commande suivante :

```bash
 passwd root
```

6. Entrez un nouveau mot de passe, puis confirmez-le.
7. Redémarrez la machine avec la commande suivante :

```bash
 reboot
```

## Méthode 2 : Utilisation du Single-User Mode

1. Redémarrez la machine virtuelle.
2. Accédez au menu **GRUB**.
3. Sélectionnez l'entrée normale pour Debian, puis appuyez sur la touche `e` pour modifier les paramètres de démarrage.
4. Recherchez la ligne commençant par `linux`.
5. À la fin de cette ligne, ajoutez `single` (ou remplacez `ro` par `rw single`).
6. Appuyez sur `Ctrl+X` ou `F10` pour démarrer avec ces nouveaux paramètres.
7. Vous accéderez à une session root sans avoir besoin de mot de passe.
8. Pour changer le mot de passe root, tapez la commande suivante :

```bash
 passwd root
```

9. Entrez un nouveau mot de passe, puis confirmez-le.
10. Redémarrez la machine avec la commande suivante :

```bash
reboot
```

---

## 5. Redimensionnement de Partitions

### 5.1 Sauvegarde des Données

Il est recommandé de sauvegarder toutes les données importantes avant de modifier les partitions.

### 5.2 Utilisation d'un Live CD/USB

Pour redimensionner la partition racine, démarrez avec un **live CD/USB** (comme GParted Live).

### 5.3 Étapes pour Redimensionner une Partition

1. Démarrez à partir du live USB.
2. Ouvrez **GParted**.
3. Sélectionnez la partition racine (généralement `/dev/sda1`).
4. Cliquez sur **"Redimensionner/Déplacer"**.
5. Appliquez les changements.

### 5.4 Vérification du Système de Fichiers

Après redimensionnement, vérifiez l'intégrité du système de fichiers avec la commande :

```bash
 e2fsck -f /dev/sdaX
```
