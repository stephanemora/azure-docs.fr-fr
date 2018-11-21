---
title: Impossible de connecter un bureau à distance à des machines virtuelles Azure car la carte réseau est désactivée | Microsoft Docs
description: Apprendre à résoudre un échec de connexion RDP dû à la désactivation de la carte réseau sur des machines virtuelles Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 6b14530bd6b4c1b6617cb1d5c88d710a32e5372c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634819"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Impossible de connecter un bureau à distance à une machine virtuelle car l'interface réseau est désactivée

Cet article explique comment résoudre un problème de connexion de votre bureau à distance à des machines virtuelles Windows Azure dû à la désactivation de l'interface réseau.

> [!NOTE] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement Resource Manager, que nous recommandons pour les nouveaux déploiements plutôt que le modèle de déploiement Classic. 

## <a name="symptoms"></a>Symptômes 

Vous ne pouvez pas établir de connexion RDP ni aucun autre type de connexion à d'autres ports d'une machine virtuelle Azure car l'interface réseau de la machine virtuelle est désactivée.

## <a name="solution"></a>Solution 

Avant de suivre cette procédure, faites en sauvegarde en prenant un instantané du disque du système d’exploitation de la machine virtuelle affectée. Pour plus d’informations, consultez [Créer un instantané](../windows/snapshot-copy-managed-disk.md).

Pour activer l'interface de la machine virtuelle, utilisez la console série ou [réinitialisez l'interface réseau](##reset-network-interface) de la machine virtuelle.

### <a name="use-serial-control"></a>Utiliser le contrôle série

1. Connectez-vous à la [console série et ouvrez une instance CMD](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Si la console série n’est pas activée sur votre machine virtuelle, consultez [Réinitialiser l'interface réseau](#reset-network-interface).
2. Vérifiez l’état de l’interface réseau :

        netsh interface show interface

    Notez le nom de l’interface réseau désactivée. 

3. Activez l’interface réseau :

        netsh interface set interface name="interface Name" admin=enabled

    Par exemple, si l'interface réseau s'appelle « Ethernet 2 », exécutez la commande suivante :

        netsh interface set interface name=""Ethernet 2" admin=enabled
    

4.  Vérifiez à nouveau l’état de l’interface réseau pour vous assurer qu'elle est activée.

        netsh interface show interface

    À ce stade, vous n’êtes pas obligé de redémarrer la machine virtuelle. La machine virtuelle sera de nouveau accessible.
        
5.  Connectez-vous à la machine virtuelle et assurez-vous que le problème est résolu.

## <a name="reset-network-interface"></a>Réinitialiser l’interface réseau

Pour réinitialiser l’interface réseau, remplacez l’adresse IP par une autre adresse IP disponible sur le sous-réseau en utilisant le portail Azure ou Azure PowerShell. Pour plus d’informations, consultez [Réinitialiser l'interface réseau](reset-network-interface.md). 