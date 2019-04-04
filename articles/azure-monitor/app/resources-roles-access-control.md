---
title: Ressources, rôles et contrôle d’accès dans Azure Application Insights | Microsoft Docs
description: Propriétaires, collaborateurs et lecteurs des perspectives de votre organisation.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: mbullwin
ms.openlocfilehash: 213f4313e96638e4d94455be5f16aa3221d35b73
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905679"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Contrôle d’accès, rôles et ressources dans Application Insights

Vous pouvez contrôler qui a lu et mis à jour l’accès à vos données dans Azure [Application Insights][start], à l’aide du [Contrôle d’accès basé sur les rôles dans Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Accordez l’accès aux utilisateurs dans le **groupe de ressources ou l’abonnement** auquel appartient votre ressource d’application et non dans la ressource elle-même. Affectez le rôle de **collaborateur de composants Application Insights** . Cela garantit un contrôle d’accès uniforme aux tests et aux alertes Web, ainsi qu’aux ressources de votre application. [En savoir plus](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Ressources, groupes et abonnements

Quelques définitions pour commencer :

* **Ressource** : une instance d’un service Microsoft Azure. Votre ressource Application Insights collecte, analyse et affiche les données de télémétrie envoyées par votre application.  Les autres types de ressources Azure incluent des applications Web, des bases de données et des machines virtuelles.
  
    Pour voir vos ressources, ouvrez le [portail Azure][portal], connectez-vous, puis cliquez sur Toutes les ressources. Pour trouver une ressource, tapez une partie de son nom dans le champ filtre.
  
    ![Liste des ressources Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Groupe de ressources**][group] : chaque ressource appartient à un groupe. Un groupe est un moyen pratique de gérer les ressources apparentées, en particulier pour le contrôle d’accès. Par exemple, vous pouvez placer dans un groupe de ressources une application Web, une ressource Application Insights pour surveiller l’application et une ressource de stockage pour conserver les données exportées.

* [**Abonnement**](https://portal.azure.com) : pour utiliser Application Insights ou d’autres ressources Azure, vous vous connectez à un abonnement Azure. Chaque groupe de ressources appartient à un abonnement Azure, où vous choisissez votre package de prix et, s’il s’agit d’un abonnement d’organisation, sélectionnez les membres et leurs autorisations d’accès.
* [**Compte Microsoft**][account] : le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter aux abonnements Microsoft Azure, XBox Live, Outlook.com et autres services Microsoft.

## <a name="access"></a> Contrôle de l’accès dans le groupe de ressources

Il est important de comprendre qu’en plus de la ressource que vous avez créée pour votre application, il existe également des ressources distinctes masquées pour les alertes et les tests Web. Elles sont associées au même [groupe de ressources](#resource-group) que votre application. Vous pouvez également placer d’autres services Azure ici, comme des sites Web ou du stockage.

Pour contrôler l’accès à ces ressources, il est donc recommandé de :

* contrôler l’accès au niveau du **groupe de ressources ou de l’abonnement** .
* affecter le rôle de **collaborateur de composants Application Insights** . Cela leur permet de modifier les tests Web, les alertes et les ressources d’Application Insights, sans donner accès aux autres services dans le groupe.

## <a name="to-provide-access-to-another-user"></a>Pour fournir l’accès à un autre utilisateur

Vous devez disposer des droits du propriétaire de l’abonnement ou du groupe de ressources.

L’utilisateur doit avoir un [compte Microsoft][account] ou disposer d’un accès à son [compte professionnel Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Vous pouvez fournir l’accès aux personnes et aux groupes d’utilisateurs définis dans Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Accéder au groupe de ressources ou directement à la ressource

Choisissez **Contrôle d’accès (IAM)** dans le menu de gauche.

![Capture d’écran du bouton Contrôle d'accès dans le portail Azure](./media/resources-roles-access-control/0001-access-control.png)

Sélectionnez **Ajouter une attribution de rôle**.

![Capture d’écran du menu Contrôle d’accès avec le bouton Ajouter encadré en rouge](./media/resources-roles-access-control/0002-add.png)

La vue **Ajouter des autorisations** ci-dessous concerne principalement les ressources Application Insights. Si vous consultiez les autorisations de contrôle d’accès à partir d’un niveau supérieur tel que les groupes de ressources, vous verriez des rôles supplémentaires non centrés sur Application Insights.

Pour afficher des informations sur tous les rôles intégrés de contrôle d'accès en fonction du rôle Azure, utilisez le [contenu de référence officielle](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Capture d’écran de la liste des rôles d’utilisateur de contrôle d’accès](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Sélectionnez un rôle

Le cas échéant, le lien est établi vers la documentation de référence officielle associée.

| Rôle | Dans le groupe de ressources |
| --- | --- |
| [Propriétaire](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Peut tout modifier, y compris l’accès utilisateur. |
| [Contributeur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Peut tout modifier, y compris l’ensemble des ressources. |
| [collaborateur de composants Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Peut modifier les ressources, les tests web et les alertes Application Insights. |
| [Lecteur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Peut afficher les éléments, mais ne peut rien modifier. |
| [Débogueur de capture instantanée d’Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Autorise l’utilisateur à utiliser les fonctionnalités du débogueur de capture instantanée d’Application Insights. Remarquez que ce rôle n’est pas inclus dans les rôles de propriétaire et de contributeur. |
| Contributeur de gestion des mises en production de déploiement Azure Service | Rôle de contributeur pour le déploiement de services via le déploiement Azure Service. |
| [Videur de données](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Rôle spécial pour le vidage des données personnelles. Consultez notre [guide pour les données personnelles](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) pour plus d’informations.   |
| Administrateur ExpressRoute | Peut créer, supprimer et gérer des itinéraires express.|
| [Contributeur Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de surveillance inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure.  |
| [Lecteur Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. |
| masterreader | Permet à un utilisateur d’afficher tous les éléments, mais sans apporter de modifications. |
| [Contributeur d’analyse](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Peut lire toutes les données de surveillance et mettre à jour les paramètres de surveillance. |
| [Publication des métriques de surveillance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Permet de publier les métriques relatives aux ressources Azure. |
| [Lecteur d’analyse](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Peut lire toutes les données de surveillance. |
| Contributeur de stratégie de ressource (préversion) | Utilisateurs renvoyés de EA, avec des droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire une ressource/hiérarchie.  |
| [Administrateur de l'accès utilisateur](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Permet à un utilisateur de gérer l’accès d’autres utilisateurs à des ressources Azure.|
| [Contributeur de site web](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Vous permet de gérer des sites web (pas des plans web), mais pas d’y accéder.|

Une « modification » inclut la création, la suppression et la mise à jour des éléments suivants :

* Ressources
* Tests web
* Alertes
* Exportation continue

#### <a name="select-the-user"></a>Sélectionnez l’utilisateur

Si l’utilisateur n’est pas dans le répertoire, vous pouvez inviter toute personne disposant d’un compte Microsoft.
(Si elle utilise des services comme Outlook.com, OneDrive, Windows Phone ou XBox Live, elle dispose d’un compte Microsoft.)

## <a name="related-content"></a>Contenu connexe

* [Contrôle d’accès dans Azure en fonction du rôle](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Requête PowerShell permettant de déterminer l’appartenance au rôle

Dans la mesure où certains rôles peuvent être liés aux notifications et aux alertes de courrier électronique, il peut être utile de pouvoir générer une liste des utilisateurs qui appartiennent à un rôle donné. Pour faciliter la génération de ces types de listes, nous proposons les exemples de requêtes suivants qui peuvent être adaptés pour répondre à vos besoins spécifiques :

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Interroger l’ensemble de l’abonnement pour les rôles d’administrateur + des rôles de contributeur

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Requête dans le contexte d’une ressource Application Insights spécifique pour les propriétaires et les collaborateurs

```powershell
$resourceGroup = “RGNAME”
$resourceName = “AppInsightsName”
$resourceType = “microsoft.insights/components”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Requête dans le contexte d’un groupe de ressources spécifique pour les propriétaires et les collaborateurs

```powershell
$resourceGroup = “RGNAME”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
