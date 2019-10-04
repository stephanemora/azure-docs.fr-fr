---
title: Vue d’ensemble du dépannage, feedback et support de Windows Virtual Desktop - Azure
description: Vue d’ensemble de la résolution des problèmes lors de la configuration d’un environnement de locataire Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: a31750d888410b9bdaea28bc5153152c426f1915
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877545"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Vue d’ensemble du dépannage, commentaires et support

Cet article fournit une vue d’ensemble des problèmes que vous pouvez rencontrer lors de la configuration d’un environnement de locataire Windows Virtual Desktop et fournit des moyens de résoudre les problèmes.

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="escalation-tracks"></a>Chemins de réaffectation

Utilisez le tableau suivant pour identifier et résoudre les problèmes que vous pouvez rencontrer quand vous configurez un environnement de locataire avec le client Bureau à distance. Une fois votre locataire configuré, vous pouvez utiliser notre nouveau [service Diagnostics](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) pour identifier les problèmes pour les scénarios courants.

>[!NOTE]
>Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Chaque fois que nous faisons référence au support de Windows Virtual Desktop, accédez pour le moment à notre forum Tech Community. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter des problèmes avec l’équipe de produit et les membres actifs de la communauté. Si vous devez résoudre un problème de support, incluez l’ID d’activité et la fenêtre de temps approximative au cours de laquelle le problème s’est produit.

| **Problème**                                                            | **Solution suggérée**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Création d’un locataire                                                    | En cas de panne d’Azure, contactez [Support Azure](https://azure.microsoft.com/support/options/) ; sinon, contactez **Support Services Bureau à distance/Windows Virtual Desktop**.|
| Accès aux modèles de la Place de marché dans le portail Azure       | En cas de panne d’Azure, contactez [Support Azure](https://azure.microsoft.com/support/options/). <br> <br> Les modèles Windows Virtual Desktop de la Place de marché Azure sont disponibles gratuitement.|
| Accès aux modèles Azure Resource Manager à partir de GitHub                                  | Consultez la section « Création de machines virtuelles hôtes de la session Windows Virtual Desktop » de [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues.md). Si le problème n’est toujours pas résolu, contactez l’[équipe de support GitHub](https://github.com/contact). <br> <br> Si l’erreur se produit après avoir accédé au modèle dans GitHub, contactez [Support Azure](https://azure.microsoft.com/support/options/).|
| Paramètres de Réseau virtuel Azure et d’ExpressRoute des pools d’hôtes de session               | Contactez **Support Azure (Réseau)** . |
| Création d’une machine virtuelle de pool d’hôtes quand les modèles Azure Resource Manager fournis avec Windows Virtual Desktop ne sont pas utilisés | Contactez **Support Azure (Calcul)** . <br> <br> Pour les problèmes avec les modèles Azure Resource Manager qui sont fournis avec Windows Virtual Desktop, consultez la section « Création d’un locataire Windows Virtual Desktop » de [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues.md). |
| Gestion des environnements d’hôtes de session Windows Virtual Desktop à partir du portail Azure    | Contactez **Support Azure**. <br> <br> Pour les problèmes de gestion lors de l’utilisation de Services Bureau à distance/Windows Virtual Desktop PowerShell, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou contactez l’**équipe de support Services Bureau à distance/Windows Virtual Desktop**. |
| Gestion de la configuration de Windows Virtual Desktop liée à des pools d’hôtes et à des groupes d’applications      | Consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou contactez l’**équipe de support Services Bureau à distance/Windows Virtual Desktop**. <br> <br> Si les problèmes sont liés à l’exemple d’interface graphique, contactez la communauté Yammer.|
| Dysfonctionnement des clients Bureau à distance au démarrage                                                 | Consultez [Connexions au client Bureau à distance](troubleshoot-client-connection.md) et, si cela ne résout pas le problème, contactez l’**équipe de support Services Bureau à distance/Windows Virtual Desktop**.  <br> <br> S’il s’agit d’un problème réseau, vos utilisateurs doivent contacter leur administrateur réseau. |
| Connecté mais pas de flux                                                                 | Résolvez les problèmes en utilisant la section « Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux) » de [Connexions au client Bureau à distance](troubleshoot-client-connection.md). <br> <br> Si vos utilisateurs ont été affectés à un groupe d’applications, réaffectez le problème à l’**équipe de support Services Bureau à distance/Windows Virtual Desktop**. |
| Problèmes de découverte de flux en raison du réseau                                            | Vos utilisateurs doivent contacter leur administrateur réseau. |
| Connexion des clients                                                                    | Consultez [Connexions au client Bureau à distance](troubleshoot-client-connection.md) et, si cela ne résout pas votre problème, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md). |
| Réactivité des applications ou du Bureau à distance                                      | Si les problèmes sont liés à une application ou un produit spécifique, contactez l’équipe responsable de ce produit. |
| Messages ou erreurs concernant les licences                                                          | Si les problèmes sont liés à une application ou un produit spécifique, contactez l’équipe responsable de ce produit. |

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes de création d’un pool de locataires et d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion au client Windows Virtual Desktop, consultez [Connexions au client Bureau à distance](troubleshoot-client-connection.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour plus d’informations sur le service en préversion, consultez [Environnement Windows Virtual Desktop en préversion](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).