---
title: 'Informations de référence : Problèmes connus et dépannage'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenez la liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/27/2021
ms.openlocfilehash: 8e3393f236152e591b054ee2d7c4c2753c2ff670
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408746"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problèmes connus et dépannage d’Azure Data Science Virtual Machine

Cet article vous aide à rechercher et corriger les erreurs ou les défaillances que vous pouvez rencontrer lors de l’utilisation d’Azure Data Science Virtual Machine.

## <a name="prompted-for-password-when-running-sudo-command-ubuntu"></a>Vous êtes invité à entrer un mot de passe lors de l’exécution de la commande sudo (Ubuntu)

Lors de l’exécution d’une commande `sudo` sur une machine Ubuntu, il se peut que vous soyez être invité à entrer à nouveau votre mot de passe pour confirmer que vous êtes bien l’utilisateur connecté. Il s’agit du comportement par défaut certains systèmes Linux tels qu’Ubuntu.
Toutefois, dans certains cas, une authentification à répétition n’est pas nécessaire et plutôt ennuyeuse.

Afin de désactiver la réauthentification dans la plupart des cas, vous pouvez exécuter la commande suivante dans un terminal.

`echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

Après redémarrage du terminal, sudo ne demande plus d’ID de connexion, considérant que l’authentification effectuée lors de l’ouverture de la session suffit.

## <a name="accessing-sql-server-windows"></a>Accès à SQL Server (Windows)

Lorsque vous tentez de vous connecter à l’instance SQL Server préinstallée, il se peut que vous rencontriez une erreur d’échec d’ouverture de session. Pour vous connecter avec succès à l’instance SQL Server, vous devez exécuter le programme avec lequel vous vous connectez, par exemple SQL Server Management Studio (SSMS), en mode administrateur. Le mode administrateur est requis parce, en raison du paramétrage par défaut de l’environnement DSVM, seuls des administrateurs sont autorisés à se connecter.

## <a name="python-package-installation-issues"></a>Problèmes d’installation des packages Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>L’installation de packages avec PIP rompt les dépendances sur Linux

Utilisez `sudo pip install` à la place de `pip install` lors de l’installation des packages.

## <a name="disk-encryption-issues"></a>Problèmes de chiffrement de disque

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Le chiffrement de disque échoue sur la machine virtuelle DSVM Ubuntu

Azure Disk Encryption (ADE) n’est actuellement pas pris en charge sur la machine virtuelle DSVM Ubuntu. Pour contourner ce problème, envisagez de configurer le [chiffrement côté serveur des disques managés Azure](../../virtual-machines/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Un outil apparaît désactivé

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V ne fonctionne pas sur la machine virtuelle DSVM Windows

Pour le moment, Hyper-V ne fonctionne pas sur Windows. Pour garantir les performances au démarrage, nous avons désactivé certains services. Pour activer Hyper-V :

1. Ouvrez la barre de recherche sur votre DSVM Windows.
1. Saisissez « Services ».
1. Définissez tous les services Hyper-V sur « Manual ».
1. Définissez « Hyper-V Virtual Machine Management » sur « Automatic ».

Votre écran final doit ressembler à ceci :

   ![Activer Hyper-V](./media/workaround/hyperv-enable-dsvm.png)