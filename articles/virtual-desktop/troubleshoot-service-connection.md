---
title: Détecter un problème de connexion au service dans Windows Virtual Desktop – Azure
description: Comment résoudre des problèmes lorsque vous configurez des connexions à un client dans un environnement de locataire Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 56a31caeefb3589527fdbbac118fa3a544a0d1a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208891"
---
# <a name="windows-virtual-desktop-service-connections"></a>Connexions au service Windows Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Appuyez-vous sur cet article pour résoudre les problèmes liés aux connexions à un client Windows Virtual Desktop.

## <a name="provide-feedback"></a>Fournir des commentaires

Vous pouvez nous fournir des commentaires et discuter du service Windows Virtual Desktop avec l’équipe de produit et d’autres membres actifs de la communauté dans [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux)

Si l’utilisateur peut démarrer les clients Bureau à distance et s’authentifier, il ne voit cependant aucune icône dans le flux de découverte web.

1. Vérifiez que l’utilisateur signalant les problèmes a été affecté aux groupes d’applications à l’aide de cette ligne de commande :

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Vérifiez que l’utilisateur se connecte avec les informations d’identification correctes.

3. Si le client web est utilisé, vérifiez qu’il n’y a aucun problème d’informations d’identification mises en cache.

4. Si l’utilisateur fait partie d’un groupe d’utilisateurs Azure Active Directory (AD), assurez-vous que le groupe d’utilisateurs est un groupe de sécurité au lieu d’un groupe de distribution. Le bureau virtuel Windows ne prend pas en charge les groupes de distribution Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une vue d’ensemble de la résolution des problèmes Windows Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre des problèmes lors de la création d’un environnement Windows Virtual Desktop et d’un pool d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un environnement et d’un pool d’hôtes](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
