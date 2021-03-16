---
title: 'Informations de référence : Problèmes connus et dépannage'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenez la liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656854"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problèmes connus et dépannage d’Azure Data Science Virtual Machine

Cet article vous aide à rechercher et corriger les erreurs ou les défaillances que vous pouvez rencontrer lors de l’utilisation d’Azure Data Science Virtual Machine.

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