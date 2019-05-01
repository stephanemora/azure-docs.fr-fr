---
title: Résolution des problèmes de vue d’ensemble, les commentaires et prise en charge - Azure un bureau virtuel Windows
description: Vue d’ensemble de résolution des problèmes lors de la configuration d’un environnement de client de bureau virtuel Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 8e344d6908ba19f8e2294c7777b9c1016eafaf52
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927641"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Résolution des problèmes de vue d’ensemble, commentaires et support

Cet article fournit une vue d’ensemble des problèmes que vous pouvez rencontrer lorsque vous configurez un environnement de client de bureau virtuel Windows et fournit les moyens de résoudre les problèmes.

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="escalation-tracks"></a>Effectue le suivi de l’escalade de verrous

Utilisez le tableau suivant pour identifier et résoudre les problèmes que vous pouvez rencontrer lorsque vous configurez un environnement de client à l’aide du client Bureau à distance.

>[!NOTE]
>Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Chaque fois que nous faisons référence à la prise en charge du bureau virtuel de Windows, accédez à notre forum de communauté technique pour l’instant. Visitez le [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour évoquer les problèmes rencontrés avec l’équipe de produit et les membres de la Communauté active. Si vous avez besoin résoudre un problème de support, inclure l’ID d’activité et le laps de temps approximatif pour lorsque le problème s’est produite.

| **Problème**                                                            | **Solution suggérée**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Création d’un client                                                    | Si une panne d’Azure, contactez [Support Azure](https://azure.microsoft.com/support/options/); sinon contact **prise en charge à distance Bureau virtuel Services/Windows de bureau**.|
| Accès aux modèles de la place de marché dans le portail Azure       | Si une panne d’Azure, contactez [prise en charge Azure](https://azure.microsoft.com/support/options/). <br> <br> Les modèles de bureau virtuel de la place de marché Windows Azure sont disponibles gratuitement.|
| L’accès à des modèles Azure Resource Manager à partir de GitHub                                  | Consultez la section « Hôte de session Bureau de création Windows Virtual machines virtuelles » de [locataire et hôte de la création du pool](troubleshoot-set-up-issues.md). Si le problème est toujours pas résolu, contactez le [GitHub l’équipe de support](https://github.com/contact). <br> <br> Si l’erreur se produit après avoir accédé à du modèle dans GitHub, contactez [prise en charge Azure](https://azure.microsoft.com/support/options/).|
| Session hôte réseau virtuel Azure (VNET) et Express Route paramètres du pool               | Contact **prise en charge Azure (mise en réseau)**. |
| Pool d’hôte de session création de Machine virtuelle (VM) lorsque les modèles Azure Resource Manager fournis avec le bureau virtuel Windows ne sont pas utilisées | Contact **prise en charge Azure (calcul)**. <br> <br> Pour les problèmes avec les modèles Azure Resource Manager qui sont fournis avec Windows de bureau virtuel, consultez la section sur les clients de création d’un bureau virtuel Windows [locataire et hôte de la création du pool](troubleshoot-set-up-issues.md). |
| La gestion des environnement hôte de session Bureau virtuel Windows à partir du portail Azure    | Contact **prise en charge Azure**. <br> <br> Pour les problèmes de gestion lors de l’utilisation de Remote Desktop Services/Windows Virtual Desktop PowerShell, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou contactez le **équipe de support à distance Bureau virtuel Services/Windows de bureau** . |
| Gestion de la configuration de bureau virtuel Windows liées à des pools d’hôte et application groupes (application)      | Consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), ou contactez le **l’équipe de support à distance Bureau virtuel Services/Windows de bureau**. <br> <br> Si les problèmes sont liés à l’exemple d’interface utilisateur graphique (GUI), contacter la Communauté de Yammer.|
| Dysfonctionnement des clients Bureau à distance sur Démarrer                                                 | Consultez [les connexions Bureau à distance client](troubleshoot-client-connection.md) et, si cela ne résout pas le problème, contactez **l’équipe de support à distance Bureau virtuel Services/Windows de bureau**.  <br> <br> S’il s’agit d’un problème réseau, vos utilisateurs doivent contacter leur administrateur réseau. |
| Connecté mais aucun flux                                                                 | Résoudre les problèmes à l’aide de le « utilisateur se connecte, mais rien ne s’affiche (aucun flux) « section de [connexions du client Bureau à distance](troubleshoot-client-connection.md). <br> <br> Si vos utilisateurs ont été attribués à un groupe de l’application, faire remonter à la **l’équipe de support à distance Bureau virtuel Services/Windows de bureau**. |
| Problèmes de découverte en raison du réseau d’alimentation                                            | Vos utilisateurs doivent contacter leur administrateur réseau. |
| Connexion des clients                                                                    | Consultez [les connexions Bureau à distance client](troubleshoot-client-connection.md) et si cela ne résout pas votre problème, consultez [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md). |
| Réactivité des applications à distance ou de bureau                                      | Si les problèmes sont liés à une application spécifique ou d’un produit, contactez l’équipe responsable de ce produit. |
| Messages de gestion des licences ou des erreurs                                                          | Si les problèmes sont liés à une application spécifique ou d’un produit, contactez l’équipe responsable de ce produit. |

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes lors de la création d’un pool de client et de l’hôte dans un environnement de bureau virtuel de Windows, consultez [locataire et hôte de la création du pool](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes lors de la configuration d’une machine virtuelle (VM) dans un bureau virtuel Windows, consultez [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion de client de bureau virtuel de Windows, consultez [les connexions Bureau à distance client](troubleshoot-client-connection.md).
- Pour résoudre les problèmes lors de l’utilisation de PowerShell avec le bureau virtuel de Windows, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour en savoir plus sur le service en version préliminaire, consultez [environnement en version préliminaire de Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Suivez le [Didacticiel : Résoudre les problèmes de déploiements de modèle Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).