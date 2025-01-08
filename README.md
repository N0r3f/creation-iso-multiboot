# Création de l'image iso d'un système Linux personnalisé de la branche Debian

## Préambule

Il existe de nombreuses manières de créer une image iso d'un système d'exploitation. Il est avant tout nécessaire de comprendre de qu'est le format iso.

Le format ISO est un type de fichier d'image disque qui représente une copie exacte du contenu d'un disque optique, tel qu'un CD ou un DVD. Voici les principales caractéristiques du format ISO :

### Structure et contenu

- Un fichier ISO contient une réplique binaire non compressée de toutes les données présentes sur un disque optique, y compris le système de fichiers et les métadonnées
- Il est basé sur la norme internationale ISO 9660, qui définit le système de fichiers utilisé sur les CD-ROM et DVD-ROM

### Utilisation

- Les fichiers ISO sont principalement utilisés pour créer des copies de CD/DVD et stocker des données amorçables pour l'installation de systèmes d'exploitation
- Ils permettent de monter et d'accéder au contenu d'un disque optique sans avoir besoin du support physique

### Compatibilité

- Le format ISO est largement pris en charge par différents systèmes d'exploitation, ce qui facilite l'interopérabilité des données entre différentes plateformes
- Il peut être lu et utilisé sur Windows, Mac OS, et les systèmes de type Unix comme GNU/Linux

### Caractéristiques techniques

- L'extension de fichier courante est .iso
- Le type MIME associé est application/x-iso9660-image
- Les fichiers ISO peuvent être montés directement ou gravés sur des supports physiques pour créer des copies identiques à l'original

En résumé, le format ISO est une méthode standardisée pour créer des images disque fidèles des supports optiques, offrant une grande compatibilité et préservant l'intégrité des données originales.

Afin de créer notre image iso, nous éviterons d'utiliser Linux From Scratch (LFS) car son utilisation est relativement complexe, le système créé n'a pas de couche graphique et ceci ne créé pas automatiquement d'image iso. Nous éviterons aussi Buildroot car l'ajout des composants et notamment de la couche graphique peut se révéler complexe pour un utilisateur débutant. 

Nous préférerons à cela l'utilisation de deux outils bien plus efficients pour répondre à nos besoins :

- [Penguins Eggs](https://penguins-eggs.net/) pour ajouter des distributions à nos serveurs PXE
- [Linux-Live-Kit](https://www.linux-live.org/) pour ajouter des distributions à nos clés USB personnalisées

## Création d'ISO pour nos serveurs PXE

### Prérequis

Afin de créer sa distribution Linux (branche Debian) personnalisée en vue de démarrer dessus via un serveur PXE, il est nécessaire d'avoir réussi la création d'un serveur PXE ([voir creer_son_serveur_PXE.pdf](https://github.com/N0r3f/creer-son-serveur-PXE)).

Pour choisir une distribution de la branche Debian, veuillez vous référer au document suivant

[https://lacapsule.github.io/timeline/](https://lacapsule.github.io/timeline/)

Il sera aussi nécessaire d'avoir une distribution de la branche Debian installée en dur, en VM ou en live PXE afin d'en produire une image ISO bootable. Il est intéressant de la personnaliser avant de créer l'image en installant des logiciels spécifiques par exemple, ou encore en modifiant le fond d'écran, le thème du système et toutes autres personnalisations qui en feront un système singulier.

### Installation de Penguins' Eggs

1. Il est possible d'installer Penguins' Eggs depuis les sources [ICI](https://github.com/pieroproietti/penguins-eggs) mais pour faciliter le processus de prise en main, La Capsule a mis à disposition un outil simplifié via son logiciel téléchargeable via l'adresse suivante :

https://store.lacapsule.org/SOFT/

Pour installer le logiciel TOOLBOX, il suffira de double cliquer sur le fichier téléchargé et de cliquer sur "installer" en validant l'installation des dépendances. Si l'installation ne se fait pas via GDEBI, il faudra alors télécharger GDEBI via la commande suivante :

```bash
sudo apt install gdebi
```

Puis, une fois l'installation de gdebi terminée, double cliquer sur le fichier précédemment téléchargé.

2. Une fois le logiciel installé, ouvrir le menu des applications et chercher "LACAPSULE" et cliquer pour ouvrir le logiciel.

3. Cliquer sur "ISO", taper le mot de passe administrateur et laisser le processus se dérouler jusqu'à ce que vous soit demandé l'installation de Calamares.

   <u>Qu'est-ce que calamares ?</u>

   *Calamares vise à simplifier et standardiser le processus d'installation des systèmes Linux, offrant une expérience cohérente quelle que soit la distribution.*

    *Son développement a débuté en 2014 et il est actuellement maintenu par l'équipe Calamares, composée principalement de développeurs KDE.*

   Il ne sera pas nécessaire d'installer Calamares, nous pouvons donc sélectionner "no" et poursuivre le processus de création de l'image ISO.

   Une seconde proposition nous est faite afin de vérifier que nous possédons assez d'espace disque pour permettre la création de notre image ISO.

   Si nous possédons au moins 10 giga de disque disponible, nous pouvons sélectionner "yes" et valider notre choix. 

   Le processus de création de l'image ISO débute.

4. Une fois le processus de création terminé, nous devrons nous rendre à la racine de /home pour apercevoir un nouveau dossier nommé "/eggs"

   ```bash
   cd /home && ls
   ```

   Si le dossier "/eggs" est visible, notre image ISO semble bien avoir été créée. Il va falloir le copier sur un support amovible (clé USB par exemple) afin de la transférer sur notre serveur PXE.

### Problèmes potentiellement rencontrés

1. "Le processus de création d'ISO démarre mais s'arrête de manière inattendue"

   Dans ce cas, il est fort probable que votre distribution ne soit pas prise en compte ou plus récente que la version de penguins' eggs. Dans ce cas, il sera nécessaire d'inscrire le nom de version dans le fichier derivatives.yaml en l'ouvrant dans un éditeur de texte comme suit :

   ```bash
   sudo nano /usr/lib/penguins-eggs/conf/derivatives.yaml
   ```

   Puis d'ajouter le nom de votre version comme pour la version wilma de Linux Mint par exemple :

   ```bash
   # jammy derivated
   - id: jammy
     distroLike: Ubuntu
     family: debian
     ids:
       - horus # elementary OS 7 Horus
       - jolnir # Elementary jolnir
       - ShiFt # Makululu
       - ulyana # Linux Mint
       - ulyssa # Linux Mint
       - uma # Linux Mint
       - una # Linux Mint
       - vanessa # Linux Mint
       - vera # Linux Mint
       - victoria # Linux Mint
       - wilma ### Version ajoutée par l'utilisateur afin de produire l'iso du système
   ```

2. Une fois la version ajouté et les modifications sauvegardées (Ctrl+o pour écrire)+(touche entrée pour valider le nom)+(Ctrl+x pour quitter nano), il sera nécessaire de mettre à jour penguins' eggs grâce à la commande suivante :

   ```bash
   sudo eggs dad -d
   ```

   Si le message suivant apparaît dans la console une fois l'opération terminée :

   ```bash
   Your configuration was saved on: /etc/penguins-eggs.d
   
   You can create a clean ISO with: sudo eggs produce
   or a full personal clone: sudo eggs produce --clone
   
   ```

    c'est que l'opération a réussi. Le cas échéant, il est possible que vous ayez fait une faute de frappe ou que votre distribution ne soit pas prise en charge. Dans le dernier cas, veuillez consulter le documentation suivante : 

   [https://github.com/pieroproietti/penguins-eggs](https://github.com/pieroproietti/penguins-eggs)

### Transférer l'image ISO vers notre PXE

Pour transférer notre image ISO sur clé USB en vue de l'intégrer dans le menu de démarrage de notre serveur PXE, nous devrons suivre les étapes suivantes :

1. Copier le dossier "iso" depuis le dossier /home/eggs/mnt vers notre clé USB :

   ```bash
   sudo cp -r /home/eggs/mnt/iso /media/$USER/uuid_de_ma_clé_USB
   ```

   NB : *pour obtenir le chemin de votre clé USB, vous pouvez ouvrir celle-ci dans un explorateur de fichiers et glisser son chemin dans le terminal ou taper la commande `ls /media/$USER` dans un terminal.*

2. Une fois la copie terminée, le prompt s'affiche de nouveau mais vous devez impérativement démonter votre support de stockage pour assurer la fin absolue de la copie par clic sur le bouton éjecter dans l'explorateur de fichiers ou par la commande umount :

   ```bash
   sudo umount /media/$USER/uuid_de_ma_clé_USB
   ```

3. Une fois le support correctement démonté, vous pouvez brancher celui-ci sur la machine contenant votre serveur PXE et copier le contenu, à savoir le dossier "/iso" à la racine du dossier "/tftpboot" comme dans l'exemple à l'étape 7 point 8 du tutoriel sur la création d'un serveur PXE :

   ```bash
   sudo cp -r /mnt/iso/* /tftpboot/le_nom_de_ma_distribution
   ```

   Pensez à créer le dossier dans tftpboot avant et à remplacer "le_nom_de_ma_distribution" par le nom que vous souhaitez donner à votre distribution personnalisée :

   ```bash
   sudo mkdir /tftpboot/le_nom_de_ma_distribution && 
   sudo cp -r /media/$USER/uuid_de_ma_clé_USB /tftpboot/le_nom_de_ma_distribution
   ```

4. Il ne vous reste plus qu'à prendre les droits sur tous les fichiers nouvellement importés et à ajouter votre entrée de menu dans le fichier *default*, comme à l'étape 9 du tutoriel sur la création d'un serveur PXE.

## Création d'ISO pour nos clés USB personnalisées

à suivre...

