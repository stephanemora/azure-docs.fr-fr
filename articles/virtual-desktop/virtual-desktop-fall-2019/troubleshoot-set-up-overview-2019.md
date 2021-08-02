---
title: Vue d’ensemble du dépannage d’Azure Virtual Desktop (classique) - Azure
description: Vue d’ensemble de la résolution des problèmes lors de la configuration d’un environnement de locataire Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 14449af17a39be884ae023ea23910e617048d5c4
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753896"
---
#  <a name="azure-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Vue d’ensemble du dépannage d’Azure Virtual Desktop (classique), feedback et support

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../troubleshoot-set-up-overview.md).

Cet article fournit une vue d’ensemble des problèmes que vous pouvez rencontrer lors de la configuration d’un environnement de locataire Azure Virtual Desktop et fournit des moyens de résoudre les problèmes.

## <a name="provide-feedback"></a>Fournir des commentaires

Rendez-vous sur le site [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Azure Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="escalation-tracks"></a>Chemins de réaffectation

Utilisez le tableau suivant pour identifier et résoudre les problèmes que vous pouvez rencontrer quand vous configurez un environnement de locataire avec le client Bureau à distance. Une fois votre locataire configuré, vous pouvez utiliser notre nouveau [service Diagnostics](diagnostics-role-service-2019.md) pour identifier les problèmes pour les scénarios courants.

>[!NOTE]
> Nous disposons d'un forum Tech Community auquel vous pouvez accéder pour discuter de vos problèmes avec l'équipe produit et avec les membres actifs de la communauté. Consultez le site [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour commencer une discussion.

| **Problème**                                                            | **Solution suggérée**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Création d’un locataire Azure Virtual Desktop                                                    | En cas de panne d’Azure, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/). Sinon, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Azure Virtual Desktop** pour le service, sélectionnez **Déploiement** pour le type de problème, puis sélectionnez **Problèmes avec la création d’un locataire Azure Virtual Desktop** pour le sous-type de problème.|
| Accès aux modèles de la Place de marché dans le portail Azure       | En cas de panne d’Azure, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Les modèles Azure Virtual Desktop de la Place de marché Azure sont disponibles gratuitement.|
| Accès aux modèles Azure Resource Manager à partir de GitHub                                  | Consultez la section [Création de machines virtuelles hôtes de la session Azure Virtual Desktop](troubleshoot-set-up-issues-2019.md#creating-azure-virtual-desktop-session-host-vms) de [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues-2019.md). Si le problème n’est toujours pas résolu, contactez l’[équipe de support GitHub](https://github.com/contact). <br> <br> Si l’erreur se produit après avoir accédé au modèle dans GitHub, contactez [Support Azure](https://azure.microsoft.com/support/create-ticket/).|
| Paramètres de Réseau virtuel Azure et d’ExpressRoute des pools d’hôtes de session               | [Ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), puis sélectionnez le service approprié (sous la catégorie Réseaux). |
| Création d’une machine virtuelle de pool d’hôtes quand les modèles Azure Resource Manager fournis avec Azure Virtual Desktop ne sont pas utilisés | [Ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), puis sélectionnez **Machine virtuelle exécutant Windows** pour le service. <br> <br> Pour les problèmes avec les modèles Azure Resource Manager qui sont fournis avec Azure Virtual Desktop, consultez la section « Création d’un locataire Azure Virtual Desktop » de [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues-2019.md). |
| Gestion des environnements d’hôtes de session Azure Virtual Desktop à partir du portail Azure    | [Ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Pour les problèmes de gestion survenant quand vous utilisez les services Bureau à distance/Azure Virtual Desktop PowerShell, consultez [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md) ou [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Azure Virtual Desktop** pour le service, sélectionnez **Configuration et gestion** pour le type de problème, puis sélectionnez **Problèmes avec la configuration du locataire à l’aide de PowerShell** pour le sous-type de problème. |
| Gestion de la configuration d’Azure Virtual Desktop liée à des pools d’hôtes et à des groupes d’applications      | Consultez [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md) ou [ouvrez une de demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Azure Virtual Desktop** pour le service, puis sélectionnez le type de problème approprié.|
| Déploiement et gestion de conteneurs de profils FSLogix | Consultez le [Guide de résolution des problèmes relatifs aux produits FSLogix](/fslogix/fslogix-trouble-shooting-ht/). Si ce guide ne vous permet pas de résoudre le problème, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Azure Virtual Desktop** pour le service, sélectionnez **FSLogix** pour le type de problème, puis sélectionnez le sous-type de problème approprié. |
| Dysfonctionnement des clients Bureau à distance au démarrage                                                 | Consultez [Résoudre des problèmes du client Bureau à distance](../troubleshoot-client.md). Si vous ne parvenez toujours pas à résoudre le problème, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Azure Virtual Desktop** pour le service, puis sélectionnez **Clients Bureau à distance** pour le type de problème.  <br> <br> S’il s’agit d’un problème réseau, vos utilisateurs doivent contacter leur administrateur réseau. |
| Connecté mais pas de flux                                                                 | Résolvez les problèmes en suivant la section [Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) de l’article [Connexions au service Azure Virtual Desktop](troubleshoot-service-connection-2019.md). <br> <br> Si vos utilisateurs ont été attribués à un groupe d’applications, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Azure Virtual Desktop** pour le service, puis sélectionnez **Clients Bureau à distance** pour le type de problème. |
| Problèmes de découverte de flux en raison du réseau                                            | Vos utilisateurs doivent contacter leur administrateur réseau. |
| Connexion des clients                                                                    | Consultez [Connexions au service Azure Virtual Desktop](troubleshoot-service-connection-2019.md) et, si cela ne résout pas votre problème, consultez [Configuration d’une machine virtuelle hôte de sessions](troubleshoot-vm-configuration-2019.md). |
| Réactivité des applications ou du Bureau à distance                                      | Si les problèmes sont liés à une application ou un produit spécifique, contactez l’équipe responsable de ce produit. |
| Messages ou erreurs concernant les licences                                                          | Si les problèmes sont liés à une application ou un produit spécifique, contactez l’équipe responsable de ce produit. |
| Problèmes liés à l’utilisation des outils Azure Virtual Desktop sur GitHub (modèles Azure Resource Manager, outil de diagnostic, outil de gestion) | Pour signaler des problèmes, consultez [Modèles Azure Resource Manager pour Services Bureau à distance](https://github.com/Azure/RDS-Templates/blob/master/README.md). |

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes de création d’un pool de locataires et d’hôtes dans un environnement Azure Virtual Desktop, consultez [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues-2019.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Azure Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration-2019.md).
- Pour résoudre les problèmes de connexion au client Azure Virtual Desktop, consultez [Connexions au service Azure Virtual Desktop](troubleshoot-service-connection-2019.md).
- Pour résoudre les problèmes liés aux clients Bureau à distance, consultez [Résoudre des problèmes du client Bureau à distance](../troubleshoot-client.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Azure Virtual Desktop, consultez [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Pour plus d’informations sur le service, consultez [Environnement Azure Virtual Desktop](environment-setup-2019.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](../../azure-resource-manager/templates/deployment-history.md).
