---
title: Problèmes avec un disque de système d’exploitation plein sur une machine virtuelle Linux
description: Résolution des problèmes liés à un disque de système d’exploitation plein sur une machine virtuelle Linux
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523274"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problèmes avec un disque de système d’exploitation plein sur une machine virtuelle Linux

La saturation du disque du système d’exploitation d’une machine virtuelle Linux peut perturber le fonctionnement de la machine virtuelle.

## <a name="symptom"></a>Symptôme

Lorsque vous essayez de créer un fichier, le message suivant s’affiche :

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Plusieurs démons indiquent ensuite qu’ils ne peuvent pas créer de fichiers temporaires pendant la session de démarrage.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Cause

Ce message d'erreur peut s’afficher pour différentes raisons :

1. Le disque est plein.
1. Le système de fichiers manque d’inodes.
1. Un disque de données est monté sur un répertoire existant, ce qui entraîne le masquage des fichiers.
1. Des fichiers ont été ouverts par un processus, puis supprimés.

## <a name="solution"></a>Solution

### <a name="process-overview"></a>Vue d’ensemble du processus

1. Créez une machine virtuelle de réparation et accédez-y.
1. Libérer de l’espace sur le disque.
1. Regénérez la machine virtuelle.

> [!NOTE]
> Lorsque vous rencontrez cette erreur, cela signifie que le système d’exploitation invité n’est pas opérationnel. Résolvez ce problème en mode hors connexion.

### <a name="create-and-access-a-repair-vm"></a>Créer une machine virtuelle de réparation et y accéder

1. Effectuez les étapes 1 à 3 des [commandes de réparation de machine virtuelle](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) pour préparer une machine virtuelle de réparation.
1. À l’aide de SSH, connectez-vous à la machine virtuelle de réparation.

### <a name="free-up-space-on-the-disk"></a>Libérer de l’espace sur le disque

Pour résoudre ce problème :

- Redimensionnez le disque jusqu’à 1 To s’il n’est pas déjà à la taille maximale (1 To).
- Libérez de l’espace sur le disque.

1. Vérifiez si le disque est plein. Si la taille du disque est inférieure à 1 To, augmentez-la jusqu’à un maximum de 1 To [à l’aide de l’interface de ligne de commande Azure](../linux/expand-disks.md).
1. Si le disque a déjà une taille de 1 To, vous devrez libérer de l’espace.
1. Une fois le redimensionnement et le nettoyage terminés, passez à la régénération de la machine virtuelle.

### <a name="rebuild-the-vm"></a>Régénérez la machine virtuelle.

Utilisez [l’étape 5 des commandes de réparation de machine virtuelle](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) pour régénérer la machine virtuelle.
