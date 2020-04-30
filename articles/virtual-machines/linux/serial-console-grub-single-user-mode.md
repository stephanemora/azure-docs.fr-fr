---
title: Console série Azure pour le GRUB et mode mono-utilisateur
description: Utilisation d’une console série pour le grub dans des machines virtuelles Azure.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758510"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utiliser la console série pour accéder au GRUB et au mode mono-utilisateur
GRUB est l’acronyme du programme d’amorçage GRand Unified Bootloader. Un GRUB vous permet de modifier votre configuration d’amorçage, notamment pour démarrer en mode mono-utilisateur.

Le mode mono-utilisateur est un environnement minimal comportant des fonctionnalités minimales. Il peut être utile pour examiner des problèmes de démarrage, des problèmes de système de fichiers ou des problèmes de réseau. Les services pouvant s’exécuter en arrière-plan sont moins nombreux et, selon le niveau d’exécution, il peut même arriver qu’un système de fichiers ne soit pas monté automatiquement.

Le mode mono-utilisateur est également utile dans les situations où votre machine virtuelle ne peut être configurée que pour accepter des clés SSH pour la connexion. Dans ce cas, il se peut que vous puissiez utiliser le mode mono-utilisateur pour créer un compte avec authentification par mot de passe.

Pour passer en mode mono-utilisateur, vous devrez entrer un GRUB lors de du démarrage de votre machine virtuelle, puis modifier la configuration d’amorçage dans le GRUB. Vous pouvez faire cela avec la console série de machine virtuelle.

## <a name="general-grub-access"></a>Accès général au GRUB
Pour accéder au GRUB, vous devez redémarrer votre machine virtuelle en laissant ouvert le panneau de la console série. Certaines distributions nécessitent une entrée au clavier pour afficher le GRUB. D’autres l’affichent automatiquement pendant quelques secondes afin de permettre à l’utilisateur d’effectuer une entrée au clavier pour annuler le délai d’expiration. 

Vous devez vous assurer que le GRUB est activé sur votre machine virtuelle afin de pouvoir accéder au mode mono-utilisateur. En fonction de votre distribution, vous devrez peut-être procéder à une configuration spécifique pour vous assurer que le GRUB est activé. Des informations spécifiques de la distribution sont disponibles ci-dessous.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Redémarrer votre machine virtuelle pour accéder au GRUB dans la console série
Vous pouvez redémarrer votre machine virtuelle avec le panneau de la console série ouvert à l’aide d’une commande SysRq `'b'` si [SysRq](./serial-console-nmi-sysrq.md) est activé, ou en cliquant sur le bouton Redémarrer dans le panneau Vue d’ensemble (ouvrir la machine virtuelle dans un nouvel onglet de navigateur pour redémarrer sans fermer le panneau de la console série). Pour voir ce que vous pouvez attendre du GRUB lorsque vous redémarrez, suivez les instructions spécifiques de la distribution ci-dessous.

## <a name="general-single-user-mode-access"></a>Accès général au mode mono-utilisateur
Un accès manuel au mode mono-utilisateur peut être nécessaire dans les situations où vous n’avez pas configuré de compte avec authentification par mot de passe. Vous devez modifier la configuration du GRUB pour passer manuellement en mode mono-utilisateur. Une fois cela fait, voir Utiliser la console série pour accéder au GRUB et au mode mono-utilisateur pour obtenir des instructions supplémentaires.

Dans les cas où la machine virtuelle ne parvient pas à démarrer, les distributions vous ramènent souvent automatiquement au mode mono-utilisateur ou au mode urgence. Toutefois, certaines distributions nécessitent une configuration supplémentaire (telle que la définition d’un mot de passe racine) pour pouvoir vous ramener automatiquement au mode mono-utilisateur ou au mode urgence.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utiliser le mode mono-utilisateur pour réinitialiser ou ajouter un mot de passe
Une fois en mode mono-utilisateur, procédez comme suit pour ajouter un nouvel utilisateur doté de privilèges sudo :
1. Exécutez `useradd <username>` pour ajouter un utilisateur.
1. Exécutez `sudo usermod -a -G sudo <username>` pour accorder au nouvel utilisateur des privilèges racine
1. Utilisez `passwd <username>` pour définir le mot de passe pour le nouvel utilisateur. Vous pouvez ensuite vous connecter en tant que nouvel utilisateur.

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Accès pour Red Hat Enterprise Linux (RHEL)
RHEL vous amène directement en mode mono-utilisateur s’il ne peut pas démarrer normalement. Toutefois, si vous n’avez pas configuré d’accès racine pour le mode mono-utilisateur, vous n’aurez pas de mot de passe racine et vous ne pourrez pas vous connecter. Il existe une solution (voir « Entrer manuellement en mode mono-utilisateur » ci-dessous), mais il est conseillé de configurer un accès racine dès le départ.

### <a name="grub-access-in-rhel"></a>Accès au GRUB dans RHEL
RHEL est fourni avec le GRUB activé par défaut. Pour entrer dans le GRUB, redémarrez votre machine virtuelle avec `sudo reboot` et appuyez sur n’importe quelle touche. Vous verrez apparaître l’écran GRUB.

> Remarque : Red Hat fournit également la documentation de démarrage en mode de récupération, mode d’urgence et mode de débogage, ainsi que la documentation de réinitialisation du mot de passe racine. [Cliquez ici pour y accéder](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurer l’accès racine pour le mode mono-utilisateur dans RHEL
Dans RHEL, le mode mono-utilisateur nécessite l’activation d’un utilisateur racine, qui est désactivé par défaut. Si vous avez besoin d’activer le mode d’utilisateur unique, appliquez les instructions suivantes :

1. Connectez-vous au système Red Hat via SSH
1. Basculer vers la racine
1. Activez le mot de passe pour l’utilisateur racine 
    * `passwd root` (définissez un mot de passe racine fort)
1. Assurez-vous que l’utilisateur racine peut se connecter uniquement via ttyS0
    * `edit /etc/ssh/sshd_config` et veillez à ce que la connexion PermitRootLogIn soit désactivée
    * `edit /etc/securetty file` pour autoriser uniquement la connexion via ttyS0 

Désormais, si le système démarre en mode d’utilisateur unique, vous pouvez vous connecter uniquement à l’aide du mot de passe racine.

Sinon, pour RHEL 7.4+ ou 6.9+, vous pouvez activer le mode d’utilisateur unique dans les invites GRUB ; consultez les instructions [ici](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Entrer manuellement en mode mono-utilisateur dans RHEL
Si vous avez configuré le GRUB et l’accès racine conformément aux instructions ci-dessus, vous pouvez entrer en mode mono-utilisateur en procédant comme suit :

1. Appuyez sur « Échap » lors du redémarrage de la machine virtuelle pour entrer dans le GRUB
1. Dans le GRUB, appuyez sur « e » pour modifier le système d’exploitation sélectionné dans lequel que vous voulez démarrer (généralement la première ligne)
1. Recherchez la ligne du noyau : dans Azure, elle commence par `linux16`
1. Appuyez sur Ctrl + E pour accéder à la fin de la ligne
1. Ajoutez le code suivant à la fin de la ligne : `systemd.unit=rescue.target`
    * Cela vous permet de démarrer en mode mono-utilisateur. Si vous souhaitez utiliser le mode d’urgence, ajoutez `systemd.unit=emergency.target` à la fin de la ligne au lieu de `systemd.unit=rescue.target`
1. Appuyez sur Ctrl + X pour quitter et redémarrer votre ordinateur avec les paramètres appliqués
1. Vous serez invité à indiquer le mot de passe administrateur avant de pouvoir entrer en mode mono-utilisateur : il s’agit du mot de passe que vous avez créé en suivant les instructions ci-dessus    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Entrez en mode mono-utilisateur unique sans compte racine activé dans RHEL
Si vous n’avez pas suivi les étapes ci-dessus pour activer l’utilisateur racine, vous pouvez toujours réinitialiser votre mot de passe racine. Suivez ces instructions :

> Remarque : si vous utilisez SELinux, vérifiez que vous avez suivi les étapes supplémentaires décrites dans la documentation Red Hat [ici](https://aka.ms/rhel7grubterminal) lors de la réinitialisation de mot de passe racine.

1. Appuyez sur « Échap » lors du redémarrage de la machine virtuelle pour entrer dans le GRUB
1. Dans le GRUB, appuyez sur « e » pour modifier le système d’exploitation sélectionné dans lequel que vous voulez démarrer (généralement la première ligne)
1. Recherchez la ligne du noyau : dans Azure, elle commence par `linux16`
1. Ajoutez `rd.break` à la fin de la ligne, tout en vous assurant de la présence d’un espace avant `rd.break` (voir l’exemple ci-dessous)
    - Cela interrompt le processus de démarrage avant que le contrôle ne passe de `initramfs` à `systemd`, comme décrit dans la documentation Red Hat [ici](https://aka.ms/rhel7rootpassword).
1. Appuyez sur Ctrl + X pour quitter et redémarrer votre ordinateur avec les paramètres appliqués
1. Lorsque vous démarrez, vous êtes amené en mode d’urgence avec un système de fichiers en lecture seule. Entrez `mount -o remount,rw /sysroot` dans l’interpréteur de commandes pour remonter le système de fichiers racine avec des autorisations de lecture/écriture
1. Lorsque vous démarrez en mode mono-utilisateur, saisissez `chroot /sysroot` pour basculer vers la prison `sysroot`
1. Vous êtes maintenant au niveau de la racine. Vous pouvez réinitialiser votre mot de passe racine avec `passwd`, puis suivre les instructions ci-dessus pour entrer en mode mono-utilisateur. Entrez `reboot -f` pour redémarrer après avoir terminé.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Remarque : les instructions ci-dessus vous amènent dans l’interpréteur de commandes d’urgence, ce qui vous permet d’effectuer des tâches telles que la modification de `fstab`. Toutefois, il est généralement conseillé de réinitialiser votre mot de passe racine et de l’utiliser pour entrer en mode mono-utilisateur. 


## <a name="access-for-centos"></a>Accès CentOS
Un peu comme Red Hat Enterprise Linux, le mode mono-utilisateur dans CentOS nécessite l’activation du GRUB et de l’utilisateur racine. 

### <a name="grub-access-in-centos"></a>Accès au GRUB dans CentOS
CentOS est fourni avec le GRUB activé par défaut. Pour entrer dans le GRUB, redémarrez votre machine virtuelle avec `sudo reboot` et appuyez sur n’importe quelle touche. Vous verrez apparaître l’écran GRUB.

### <a name="single-user-mode-in-centos"></a>Mode mono-utilisateur dans CentOS
Suivez les instructions pour RHEL ci-dessus afin d’activer le mode mono-utilisateur dans CentOS.

## <a name="access-for-ubuntu"></a>Accès Ubuntu 
Les images Ubuntu ne nécessitent pas de mot de passe racine. Si le système démarre en mode mono-utilisateur, vous pouvez l’utiliser sans informations d’identification supplémentaires. 

### <a name="grub-access-in-ubuntu"></a>Accès au GRUB dans Ubuntu
Pour accéder au GRUB, maintenez la touche « Échap » enfoncée pendant le démarrage de la machine virtuelle. 

Par défaut, les images Ubuntu n’affichent pas automatiquement l’écran GRUB. Vous pouvez modifier cela en procédant comme suit :
1. Ouvrez `/etc/default/grub.d/50-cloudimg-settings.cfg` dans un éditeur de texte de votre choix.
1. Remplacez la valeur `GRUB_TIMEOUT` par une valeur autre que zéro.
1. Ouvrez `/etc/default/grub` dans un éditeur de texte de votre choix.
1. Commentez la ligne `GRUB_HIDDEN_TIMEOUT=1`.
1. Exécutez `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Mode mono-utilisateur dans Ubuntu
Ubuntu vous amène directement en mode mono-utilisateur s’il ne peut pas démarrer normalement. Pour entrer manuellement dans le mode mono-utilisateur, appliquez les instructions suivantes :

1. À partir du GRUB, appuyez sur « e » pour modifier votre entrée de démarrage (entrée Ubuntu)
1. Recherchez la ligne qui commence par `linux`, puis recherchez `ro`
1. Ajoutez `single` après `ro`, tout en vous assurant de la présence d’un espace avant et après `single`
1. Appuyez sur Ctrl + X pour redémarrer avec ces paramètres, puis entrez en mode mono-utilisateur

## <a name="access-for-coreos"></a>Accès CoreOS
Le mode mono-utilisateur dans CoreOS nécessite l’activation du GRUB. 

### <a name="grub-access-in-coreos"></a>Accès au GRUB dans CoreOS
Pour accéder au GRUB, appuyez sur n’importe quelle touche lors du démarrage de votre machine virtuelle.

### <a name="single-user-mode-in-coreos"></a>Mode mono-utilisateur dans CoreOS
CoreOS vous amène directement en mode mono-utilisateur s’il ne peut pas démarrer normalement. Pour entrer manuellement dans le mode mono-utilisateur, appliquez les instructions suivantes :
1. À partir du GRUB, appuyez sur « e » pour modifier votre entrée de démarrage
1. Recherchez la ligne qui commence par `linux$`. Il doit y en avoir 2, encapsulées dans des clauses if/else différentes
1. Ajoutez `coreos.autologin=ttyS0` à la fin des deux lignes `linux$`
1. Appuyez sur Ctrl + X pour redémarrer avec ces paramètres, puis entrez en mode mono-utilisateur

## <a name="access-for-suse-sles"></a>Accès SUSE SLES
Les images plus récentes de SLES 12 SP3+ autorisent l’accès par le biais de la console en série dans les situations où le système démarre en mode d’urgence. 

### <a name="grub-access-in-suse-sles"></a>Accès au GRUB dans SUSE SLES
L’accès au GRUB dans SLES requiert une configuration de chargeur de démarrage par le biais de YaST. Pour ce faire, procédez comme suit :

1. Connectez-vous avec SSH à votre machine virtuelle SLES et exécutez `sudo yast bootloader`. Utilisez la touche `tab`, la touche `enter` et les touches de direction pour naviguer dans le menu. 
1. Accédez à `Kernel Parameters`, puis sélectionnez `Use serial console`. 
1. Ajouter `serial --unit=0 --speed=9600 --parity=no` aux arguments de la console

1. Appuyez sur F10 pour enregistrer vos paramètres et quitter l’écran
1. Pour entrer dans le GRUB, redémarrez votre machine virtuelle et appuyez sur n’importe quelle touche pendant la séquence de démarrage afin que le GRUB reste à l’écran
    - Le délai d’expiration par défaut pour le GRUB est de 1 s. Vous pouvez le modifier en changeant la variable `GRUB_TIMEOUT` dans `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Mode mono-utilisateur dans SUSE SLES
Vous êtes automatiquement amené dans l’interpréteur de commandes d’urgence si SLES ne peut pas démarrer normalement. Pour entrer manuellement dans l’interpréteur de commandes d’urgence, appliquez les instructions suivantes :

1. À partir du GRUB, appuyez sur « e » pour modifier votre entrée de démarrage (entrée SLES)
1. Recherchez la ligne du noyau qui commence par `linux`
1. Ajoutez `systemd.unit=emergency.target` à la fin de la ligne
1. Appuyez sur Ctrl + X pour redémarrer avec ces paramètres, puis entrez dans l’interpréteur de commandes d’urgence
   > Vous êtes amené dans l’interpréteur de commandes d’urgence avec un système de fichiers _en lecture seule_. Pour apporter des modifications à l’un des fichiers, vous devez remonter le système de fichiers avec des autorisations en lecture-écriture. Pour ce faire, entrez `mount -o remount,rw /` dans l’interpréteur de commandes

## <a name="access-for-oracle-linux"></a>Accès Oracle Linux
Un peu comme Red Hat Enterprise Linux, le mode mono-utilisateur dans Oracle Linux nécessite l’activation du GRUB et de l’utilisateur racine. 

### <a name="grub-access-in-oracle-linux"></a>Accès au GRUB dans Oracle Linux
Oracle Linux est fourni avec le GRUB activé par défaut. Pour entrer dans le GRUB, redémarrez votre machine virtuelle avec `sudo reboot` et appuyez sur « Échap ». Vous verrez apparaître l’écran GRUB.

### <a name="single-user-mode-in-oracle-linux"></a>Mode mono-utilisateur dans Oracle Linux
Suivez les instructions pour RHEL ci-dessus afin d’activer le mode mono-utilisateur dans Oracle Linux.

## <a name="next-steps"></a>Étapes suivantes
* La page principale de documentation de la console série Linux se trouve [ici](serial-console.md).
* Utiliser la console série pour [les appels SysRq et NMI](serial-console-nmi-sysrq.md)
* La console série est également disponible pour les machines virtuelles [Windows](../windows/serial-console.md)
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md)
