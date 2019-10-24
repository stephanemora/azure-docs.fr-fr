---
title: Problèmes connus et résolutions
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenez la liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72302198"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problèmes connus et dépannage d’Azure Data Science Virtual Machine

Cet article vous aide à rechercher et corriger les erreurs ou les défaillances rencontrées lors de l’utilisation d’Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problèmes d’installation des packages Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>L’installation de packages avec PIP rompt les dépendances sur Linux

Utilisez `sudo pip install` à la place de `pip install` lors de l’installation des packages.

## <a name="disk-encryption-issues"></a>Problèmes de chiffrement de disque

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Le chiffrement de disque échoue sur la machine virtuelle DSVM Ubuntu

Azure Disk Encryption (ADE) n’est actuellement pas pris en charge sur la machine virtuelle DSVM Ubuntu. Pour contourner ce problème, envisagez de configurer le [chiffrement de stockage Azure à l’aide de clés gérées par le client](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Un outil apparaît désactivé

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V ne fonctionne pas sur la machine virtuelle DSVM Windows

Il s’agit du comportement attendu ; comme pour les performances de démarrage, nous avons désactivé certains services. Pour réactiver, ouvrez la barre de recherche sur votre machine virtuelle DSVM Windows, tapez « Services », puis définissez tous les services Hyper-V sur « Manuel » et définissez « Gestion des machines virtuelles Hyper-V » sur « Automatique ».

Votre écran final doit ressembler à ceci :

   ![Activer Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

