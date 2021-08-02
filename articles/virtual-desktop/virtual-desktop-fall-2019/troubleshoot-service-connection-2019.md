---
title: Détecter un problème de connexion au service dans Azure Virtual Desktop (classique) – Azure
description: Comment résoudre des problèmes quand vous configurez des connexions à un client dans un environnement de locataire Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 856d6e65273b15c843bffe40c818b07c60f00fb0
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753932"
---
# <a name="azure-virtual-desktop-classic-service-connections"></a>Connexions au service Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../troubleshoot-service-connection.md).

Appuyez-vous sur cet article pour résoudre les problèmes liés aux connexions à un client Azure Virtual Desktop.

## <a name="provide-feedback"></a>Fournir des commentaires

Vous pouvez nous fournir des commentaires et discuter du service Azure Virtual Desktop avec l’équipe de produit et d’autres membres actifs de la communauté dans [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux)

Si l’utilisateur peut démarrer les clients Bureau à distance et s’authentifier, il ne voit cependant aucune icône dans le flux de découverte web.

Vérifiez que l’utilisateur signalant les problèmes a été affecté aux groupes d’applications à l’aide de cette ligne de commande :

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Vérifiez que l’utilisateur se connecte avec les informations d’identification correctes.

Si le client web est utilisé, vérifiez qu’il n’y a aucun problème d’informations d’identification mises en cache.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une vue d’ensemble de la résolution des problèmes Azure Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview-2019.md).
- Pour résoudre les problèmes de création d’un pool de locataires et d’hôtes dans un environnement Azure Virtual Desktop, consultez [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues-2019.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Azure Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration-2019.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Azure Virtual Desktop, consultez [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
