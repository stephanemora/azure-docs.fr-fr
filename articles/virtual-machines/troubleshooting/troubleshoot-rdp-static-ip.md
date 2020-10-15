---
title: Impossible de connecter un bureau à distance à des machines virtuelles Azure en raison de l’adresse IP statique | Microsoft Docs
description: Découvrez comment résoudre un problème de protocole RDP (Remote Desktop Protocol) causé par une adresse IP statique dans Microsoft Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 49f3f44c7de8c700d0093c5eb6f166a1dffb34a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087246"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Impossible de connecter un bureau à distance à des machines virtuelles Azure en raison de l’adresse IP statique

Cet article décrit un problème dans lequel vous ne pouvez pas connecter un bureau distant à des machines virtuelles (VM) Windows Azure après qu’une IP statique a été configurée sur la machine virtuelle.


## <a name="symptoms"></a>Symptômes

Au moment d’établir une connexion RDP à une machine virtuelle dans Azure, vous obtenez le message d’erreur général suivant :

**Le Bureau à distance ne peut pas se connecter à l’ordinateur distant pour l’une des raisons suivantes :**

1. **L’accès à distance au serveur n’est pas activé**

2. **L’ordinateur distant est éteint**

3. **L’ordinateur distant n’est pas disponible sur le réseau**

**Vérifiez que l’ordinateur distant est allumé et connecté au réseau et que l’accès à distance est activé.**

Lorsque vous vérifiez la capture d’écran dans les [diagnostics de démarrage](../troubleshooting/boot-diagnostics.md) du portail Azure, vous voyez la machine virtuelle démarrer normalement et attendre les identifiants dans l’écran de connexion.

## <a name="cause"></a>Cause :

La machine virtuelle a une adresse IP statique qui est définie sur l’interface réseau dans Windows. Cette adresse IP est différente de l’adresse définie dans le portail Azure.

## <a name="solution"></a>Solution

Avant de suivre cette procédure, faites en sauvegarde en prenant un instantané du disque du système d’exploitation de la machine virtuelle affectée. Pour plus d’informations, consultez [Créer un instantané](../windows/snapshot-copy-managed-disk.md).

Pour résoudre ce problème, utilisez la console série pour activer le protocole DHCP ou [réinitialisez l’interface réseau](reset-network-interface.md) de la machine virtuelle.

### <a name="use-serial-control"></a>Utiliser le contrôle série

1. Connectez-vous à la [console série et ouvrez une instance CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Si la console série n’est pas activée sur votre machine virtuelle, consultez [Réinitialiser l’interface réseau](reset-network-interface.md).
2. Vérifiez si le protocole DHCP est désactivé sur l’interface réseau :

    ```console
    netsh interface ip show config
    ```

3. Si le protocole DHCP est désactivé, rétablissez la configuration de votre interface réseau pour utiliser DHCP :

    ```console
    netsh interface ip set address name="<NIC Name>" source=dhc
    ```

    Par exemple, si l’interface réseau s’appelle « Ethernet 2 », exécutez la commande suivante :

    ```console
    netsh interface ip set address name="Ethernet 2" source=dhc
    ```

4. Interrogez à nouveau la configuration IP pour vous assurer que l’interface réseau est correctement configurée. La nouvelle adresse IP doit correspondre à celle fournie par Azure.

    ```console
    netsh interface ip show config
    ```

    À ce stade, vous n’êtes pas obligé de redémarrer la machine virtuelle. La machine virtuelle sera de nouveau accessible.

Ensuite, si vous voulez configurer l’adresse IP statique pour la machine virtuelle, consultez [Configurer les adresses IP statiques d’une machine virtuelle](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).