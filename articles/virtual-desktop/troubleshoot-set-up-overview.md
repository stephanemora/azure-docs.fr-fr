---
title: 'Vue d’ensemble du dépannage de Windows Virtual Desktop : Azure'
description: Vue d’ensemble de la résolution des problèmes lors de la configuration d’un environnement de locataire Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 196c4f692b425d2fc6592888ba89f2fa6dafd3de
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612501"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Vue d’ensemble du dépannage, commentaires et support

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, et nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article fournit une vue d’ensemble des problèmes que vous pouvez rencontrer lors de la configuration d’un environnement de locataire Windows Virtual Desktop et fournit des moyens de résoudre les problèmes.

## <a name="report-issues-during-public-preview"></a>Signaler des problèmes pendant la phase de préversion publique

Pour signaler des problèmes ou suggérer des fonctionnalités lors de la phase de préversion publique de la version Printemps 2020, visitez le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Vous pouvez utiliser la Communauté technique pour discuter des bonnes pratiques ou bien pour suggérer et soutenir de nouvelles fonctionnalités. Quand vous signalez un problème lié à la préversion publique, veillez à étiqueter le type de problème en tant que **Spring 2020 Update (Preview)** (Mise à jour Printemps 2020 (préversion)).

Quand vous demandez de l’aide ou que vous proposez une nouvelle fonctionnalité, veillez à décrire votre sujet aussi précisément que possible. Des informations détaillées peuvent aider les autres utilisateurs à répondre à votre question ou à comprendre la fonctionnalité pour laquelle vous proposez un vote.

## <a name="escalation-tracks"></a>Chemins de réaffectation

Avant de passer à autre chose, veillez à consulter la [page des états Azure](https://status.azure.com/status) et [Azure Service Health](https://azure.microsoft.com/features/service-health/) pour vérifier que votre service Azure fonctionne correctement.

Utilisez le tableau suivant pour identifier et résoudre les problèmes que vous pouvez rencontrer quand vous configurez un environnement de locataire avec le client Bureau à distance. Une fois votre locataire configuré, vous pouvez utiliser notre nouveau [service Diagnostics](diagnostics-role-service.md) pour identifier les problèmes pour les scénarios courants.

| **Problème**                                                            | **Solution suggérée**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Accès aux modèles de la Place de marché dans le portail Azure       | Les modèles Windows Virtual Desktop de la Place de marché Azure sont disponibles gratuitement.|
| Paramètres de Réseau virtuel Azure et d’ExpressRoute des pools d’hôtes de session               | [Ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), puis sélectionnez le service approprié (sous la catégorie Réseaux). |
| Création d’une machine virtuelle de pool d’hôtes quand les modèles Azure Resource Manager fournis avec Windows Virtual Desktop ne sont pas utilisés | [Ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), puis sélectionnez **Machine virtuelle exécutant Windows** pour le service. <br> <br> Pour les problèmes avec les modèles Azure Resource Manager qui sont fournis avec Windows Virtual Desktop, consultez la section « Création d’un locataire Windows Virtual Desktop » de [Création d’un pool d’hôtes dans un environnement](troubleshoot-set-up-issues.md). |
| Gestion des environnements d’hôtes de session Windows Virtual Desktop à partir du portail Azure    | [Ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Pour les problèmes de gestion survenant quand vous utilisez les services Bureau à distance/Windows Virtual Desktop PowerShell, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Windows Virtual Desktop** pour le service, sélectionnez **Configuration et gestion** pour le type de problème, puis sélectionnez **Problèmes avec la configuration du locataire à l’aide de PowerShell** pour le sous-type de problème. |
| Gestion de la configuration de Windows Virtual Desktop liée à des pools d’hôtes et à des groupes d’applications      | Consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou [ouvrez une de demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Windows Virtual Desktop** pour le service, puis sélectionnez le type de problème approprié.|
| Déploiement et gestion de conteneurs de profils FSLogix | Consultez le [Guide de résolution des problèmes relatifs aux produits FSLogix](/fslogix/fslogix-trouble-shooting-ht/). Si ce guide ne vous permet pas de résoudre le problème, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Windows Virtual Desktop** pour le service, sélectionnez **FSLogix** pour le type de problème, puis sélectionnez le sous-type de problème approprié. |
| Dysfonctionnement des clients Bureau à distance au démarrage                                                 | Consultez [Résoudre des problèmes du client Bureau à distance](troubleshoot-client.md). Si vous ne parvenez toujours pas à résoudre le problème, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Windows Virtual Desktop** pour le service, puis sélectionnez **Clients Bureau à distance** pour le type de problème.  <br> <br> S’il s’agit d’un problème réseau, vos utilisateurs doivent contacter leur administrateur réseau. |
| Connecté mais pas de flux                                                                 | Résolvez les problèmes en suivant la section [Lorsqu’un utilisateur se connecte, rien ne s’affiche (aucun flux)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) de l’article [Connexions au service Windows Virtual Desktop](troubleshoot-service-connection.md). <br> <br> Si vos utilisateurs ont été attribués à un groupe d’applications, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/), sélectionnez **Windows Virtual Desktop** pour le service, puis sélectionnez **Clients Bureau à distance** pour le type de problème. |
| Problèmes de découverte de flux en raison du réseau                                            | Vos utilisateurs doivent contacter leur administrateur réseau. |
| Connexion des clients                                                                    | Consultez [Connexions au service Windows Virtual Desktop](troubleshoot-service-connection.md) et, si cela ne résout pas votre problème, consultez [Configuration d’une machine virtuelle hôte de sessions](troubleshoot-vm-configuration.md). |
| Réactivité des applications ou du Bureau à distance                                      | Si les problèmes sont liés à une application ou un produit spécifique, contactez l’équipe responsable de ce produit. |
| Messages ou erreurs concernant les licences                                                          | Si les problèmes sont liés à une application ou un produit spécifique, contactez l’équipe responsable de ce produit. |
| Problèmes liés aux méthodes d’authentification tierces | Vérifiez que votre fournisseur tiers prend en charge les scénarios Windows Virtual Desktop en tenant compte des problèmes connus. |
| Problèmes lors de l’utilisation de Log Analytics pour Windows Virtual Desktop | Pour les problèmes liés au schéma de diagnostics, [ouvrez une demande de support Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Pour les requêtes, la visualisation ou d’autres problèmes dans Log Analytics, sélectionnez le type de problème approprié sous Log Analytics. |

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes de création d’un pool d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un pool d’hôtes dans un environnement](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion au client Windows Virtual Desktop, consultez [Connexions au service Windows Virtual Desktop](troubleshoot-service-connection.md).
- Pour résoudre les problèmes liés aux clients Bureau à distance, consultez [Résoudre des problèmes du client Bureau à distance](troubleshoot-client.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour plus d’informations sur le service, consultez [Environnement Windows Virtual Desktop](environment-setup.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](../azure-resource-manager/templates/deployment-history.md).
