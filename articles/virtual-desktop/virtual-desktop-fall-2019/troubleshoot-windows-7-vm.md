---
title: Machines virtuelles Windows 7 dans Azure Virtual Desktop (classique) – Azure
description: Guide pratique pour résoudre les problèmes de machines virtuelles Windows 7 dans un environnement Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2d0ea39dc4a0b217c35322e0aefc477996402a0f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753860"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-azure-virtual-desktop-classic"></a>Résolution des problèmes de machines virtuelles Windows 7 dans Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager.

Utilisez cet article pour résoudre les problèmes rencontrés lors de la configuration des machines virtuelles hôtes de session Azure Virtual Desktop.

## <a name="known-issues"></a>Problèmes connus

Windows 7 sur Azure Virtual Desktop ne prend pas en charge les fonctionnalités suivantes :

- Applications virtualisées (RemoteApps)
- Redirection de fuseau horaire
- Mise à l'échelle PPP automatique

Sur Windows 7, Azure Virtual Desktop ne peut virtualiser les bureaux que dans leur intégralité.

Bien que la mise à l'échelle PPP automatique ne soit pas prise en charge, vous pouvez manuellement modifier la résolution sur votre machine virtuelle en cliquant avec le bouton droit de la souris sur l'icône du client Bureau à distance et en sélectionnant **Résolution**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Erreur : Impossible d'accéder au groupe d'utilisateurs du Bureau à distance

Si Azure Virtual Desktop ne parvient pas à vous trouver ou à trouver les informations d’identification de vos utilisateurs dans le groupe d’utilisateurs du Bureau à distance, l’un des messages d’erreur suivants peut s’afficher :

- « Cet utilisateur n'est pas membre du groupe d'utilisateurs du Bureau à distance »
- « Vous devez disposer des autorisations nécessaires pour vous connecter via les services Bureau à distance »

Pour y remédier, ajoutez l'utilisateur au groupe d'utilisateurs du Bureau à distance :

1. Ouvrez le portail Azure.
2. Sélectionnez la machine virtuelle sur laquelle le message d'erreur est apparu.
3. Sélectionnez **Exécuter une commande**.
4. Exécutez la commande suivante en remplaçant `<username>` par le nom de l'utilisateur que vous souhaitez ajouter :

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```