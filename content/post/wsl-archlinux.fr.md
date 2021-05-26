---
date: "2018-06-05T23:00:00+02:00"
title: "Comment installer Archlinux sous Windows 10 (version 1803) avec WSL"
authors: []
categories:
  - Linux
tags:
  - Linux
  - Windows
  - Archlinux
  - WSL
draft: false
outputs:
  - HTML
  - GEMINI
---

Il y a quelques jours, j'ai décidé de tenter d'installer un ArchLinux sous [WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux), pour avoir accès à une distribution Linux que je maitrise lors de mes sessions sous Windows 10 et tout simplement pour le sport. Voici donc comment installer Archlinux avec Windows Subsystem for Linux (ou toute autre distribution non disponible dans le store Microsoft, mais je ne renterais pas dans les détails spécifiques de toutes les distributions Linux, j'ai un savoir limité). Enfin, je vais surtout raconter ma tentative et les problèmes que j'ai rencontrés.

Je me suis inspiré de la méthode expliquée par David Wood sur son [site internet](https://davidtw.co/writings/2017/archlinux-on-the-windows-subsystem-for-linux) et je l'ai actualisé pour prendre en compte les évolutions des outils utilisés.

_Avertissement : ArchLinux n'est pas officiellement supportée par WSL, des évolutions de WSL et/ou LxRunOffline peuvent conduire à rendre ce document obsolète et je ne peux être tenu responsable pour les mauvaises manipulations que vous pourrez faire._

# Installation de LxRunOffline

La première étape consiste à installer l'utilitaire [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline). Cet utilitaire permet de gérer des instances WSL sans devoir passer par le Microsoft store et de créer des instances de distribution Linux non supportée.

Pour l´installation, je préfère passer par [Chocolatey](https://chocolatey.org/) :

```batch
choco install -y lxrunoffline
```

Via Chocolatey, l'installation de LxRunOffline va conduire à l'installation de WSL et d'une modification dans la base de registre. Un redémarrage de Windows est nécessaire par la suite.

# Création de l'instance d'ArchLinux
Une fois LxRunOffline installé, il faut récupérer le bootstrap d'Archlinux qui est disponible via ce [site](https://lxrunoffline.apphb.com/download/ArchLinux/) ou de récupérer le fichier archlinux-bootstrap-{yyyy.mm.dd}-x86_64.tar.gz (où {yyyy.mm.dd} est la date de création du bootstrap, par exemple 2018.06.01 au moment de la rédaction de l'article) dans le dossier iso/latest/ de n'importe quel miroir ArchLinux. Puis dans une console Windows (ou PowerShell) de lancer :

```batch
lxrunoffline install -n ArchLinux -f X:/where/is/archlinux-bootstrap-{yyyy.mm.dd}-x86_64.tar.gz -d D:/WSL/ArchLinux -r root.x86_64
```

* -n ArchLinux : permet de définir le nom de l'instance WSL
* -f X:/where/is/archlinux-bootstrap-{yyyy.mm.dd}-x86_64.tar.gz : permet de définir l'archive qui sert de base à la création du système racine de l'instance
* -d D:/WSL/ArchLinux : permet de définir le dossier qui va accueillir les fichiers de l´instance
* -r root.x86_64 : défini le dossier dans l'archive qui contient le système racine du bootstrap

_Note : Pour la création de l'instance, je suis passé par une phase de recréation d'une archive racine pour LxRunOffline comme indiquée dans l'article de David Wood ayant découvert l'option -r via le [wiki de LxRunOffline](https://github.com/DDoSolitary/LxRunOffline/wiki) trop tard._

Si l'exécution de la commande précédente finie par une erreur du genre :

```batch
[ERROR] Couldn't open the file or directory "D:\WSL\ArchLinux\rootfs\usr\share\perl5\core_perl\pod\".
```

C'est que vous avez oublié de redémarrer Windows après l'installation de LxRunOffline. Dans ce cas, vous devez supprimer le dossier crée (D:\WSL\ArchLinux dans l'exemple), redémarrer Windows et recommencer.

# Configuration d'ArchLinux

Une limitation depuis la dernière version (1803) de Windows 10 oblige de faire la première exécution de l'instance WSL que vous venez de créer en tant qu'administrateur

Donc dans une console de commande/PowerShell administrateur vous devez exécuter la commande suivante :

```batch
lxrunoffline run -n ArchLinux -w
```

* -n ArchLinux : Le nom de l'instance WSL qu'on souhaite lancer
* -w : Permet de ne pas utiliser le dossier en cours dans l'instance WSL

Si vous le ne faite pas vous aurez une erreur de ce genre :
```batch
Error : 0x80070005
```

Après ce premier lancement, vous pouvez lancer l'instance WSL dans une console/un PowerShell avec un utilisateur standard.

Vous vous trouvez directement dans ArchLinux dans un shell root.

Avant de procéder à l'installation des paquets de base, vous devrez choisir un miroir ArchLinux (j'ai pris le premier miroir français de la liste)
```bash
echo 'Server = http://archlinux.de-labrusse.fr/$repo/os/$arch' >> /etc/pacman.d/mirrorlist 
```
Pour vous devez initialiser le porte-clés pour la validation des signatures des paquets ArchLinux et je recommande installer les clés des constructeurs de paquets en récupérant le paquet directement sur le [site d'ArchLinux](https://www.archlinux.org/packages/core/any/archlinux-keyring/) en cliquant sur _Download From Mirror_. 
```bash
pacman-key --init
pacman -U /mnt/X/where/is/archlinux-keyring-20180404-1-any.pkg.tar.xz
```

Puis vous pourrez lancer l'installation des paquets de base d'ArchLinux
```bash
pacman -Syu base base-devel
```
Et faire les manipulations que vous souhaitez pour votre instance WSL ArchLinux (changer de langue, installer les outils que vous souhaitez, etc.)

# Détails post installation spécifiques

## Réparation de fakeroot
Si vous souhaitez construire des paquets en provenance d'[AUR](https://aur.archlinux.org) (ou de construire vos propres paquets maisons) sous WSL, vous devez installer une version spécifique de fakeroot. En effet, le fakeroot fourni par ArchLinux ne fonctionne pas correctement sous WSL.

Sous une installation ArchLinux fonctionnelle vous devez récupérer les sources pour construire le paquet [fakeroot-tcp](https://aur.archlinux.org/packages/fakeroot-tcp).

```bash
git clone https://aur.archlinux.org/fakeroot-tcp.git
cd fakeroot-tcp
makepkg -s
```
Récupérez le paquet fakeroot-tcp-{x.xx-x}-x86_64.pkg.tar.xz généré (où le {x.xx-x} correspond à la version du dit paquet).
Puis dans votre instance vous devez exécuter :
```bash
pacman -U /mnt/X/where/is/fakeroot-tcp-1.22-2-x86_64.pkg.tar.xz
```

## Définition de l'utilisateur par défaut
Une fois que vous aurez créée un utilisateur pour votre instance, vous pourrez modifier votre instance WSL pour qu'elle démarre sur l'utilisateur de votre choix via la commande suivante :
```batch
lxrunoffline config-uid -n ArchLinux -v {uid}
```
* -n ArchLinux : toujours le nom de l'instance qu'on souhaite modifier
* -v {uid} : la valeur qu'on souhaite donner, avec {uid} l'uid de l'utilisateur qu'on souhaite utiliser par défaut. On récupère cette valeur via la commande `id`.

_Note : Cette modification n'a pas été pris en compte immédiatement dans mon cas et j'ai dû fermer ma session Windows/redémarer Windows pour que ça soit pris en compte._

## Création d'un raccourci pour lancer l'instance WSL

Je vous renvoie au [wiki](https://github.com/DDoSolitary/LxRunOffline/wiki/Creating-shortcuts-to-installations) de LxRunOffline pour la création du raccourci. David Wood fournit une [icône](https://www.dropbox.com/sh/w3x7ajxwxig3up1/AAAnhLUctzTeAhshV7TJlqcZa?dl=0) pour embellir votre raccourci ArchLinux.

# Reste à faire

Il me reste à voir si je ne pourrais pas supprimer certains paquets de base (comme le paquet _linux_ ou _mkinitcpio_) qui ne sont pas utile dans le contexte de WSL.
