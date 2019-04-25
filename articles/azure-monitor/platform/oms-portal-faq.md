---
title: Questions courantes pour la transition depuis le portail OMS vers le portail Azure pour les utilisateurs de Log Analytics | Microsoft Docs
description: Réponses aux questions courantes pour les utilisateurs de Log Analytics en cours de transition depuis le portail OMS vers le portail Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: 8947d235894ffc021243a64244b4904c5ca7f128
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453118"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Questions courantes pour la transition depuis le portail OMS vers le portail Azure pour les utilisateurs de Log Analytics
Log Analytics utilisait initialement son propre portail appelé portail OMS pour gérer sa configuration et analyser les données collectées.  Toutes les fonctionnalités accessibles depuis ce portail ont été déplacées vers le portail Azure où elles continueront d’être développées.

Cet article répond aux questions courantes pour les utilisateurs effectuant cette transition.  Si vous avez utilisé Log Analytics dans le portail OMS, vous pouvez trouver ici des réponses sur la façon dont vous pouvez effectuer les mêmes tâches dans le portail Azure.

## <a name="do-i-need-to-migrate-anything"></a>Dois-je migrer quoi que ce soit ?
Non. Aucune modification n’est apportée à Log Analytics lui-même, donc rien ne doit être migré. La seule chose qui change est l’interface que vous utilisez pour y accéder. En fait, vous pouvez à présent utiliser le portail Azure pour accéder aux mêmes espaces de travail, solutions, affichages et recherches dans les journaux que vous utilisez aujourd'hui dans le portail OMS.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Où puis-je trouver Log Analytics dans Azure ?
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).  Cliquez sur **Plus de services** et, dans la liste des ressources, tapez **Log Analytics**. Sélectionnez **Log Analytics** puis sélectionnez votre espace de travail. La page de résumé pour l’espace de travail s’affiche.

![Espace de travail Log Analytics](media/oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Comment gérer les autorisations ?
Si vous n’avez pas accès à votre espace de travail Log Analytics dans le portail Azure, vous devez configurer vos autorisations à l’aide de [l’accès en fonction du rôle Azure](../../role-based-access-control/role-assignments-portal.md). Pour plus d’informations sur la gestion des autorisations de l’espace de travail, consultez [Gérer les espaces de travail](manage-access.md#manage-accounts-and-users). Pour plus d’informations sur la gestion des autorisations pour les alertes, consultez [Prise en main des rôles, autorisations et de la sécurité avec Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Comment créer un nouvel espace de travail ? 
Dans la liste des espaces de travail au sein du portail Azure, cliquez sur **Ajouter** dans la liste des espaces de travail.  Pour plus de détails, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../learn/quick-create-workspace.md).

![Page Vue d’ensemble](media/oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Où se trouve ma page Vue d’ensemble ?
L’écran principal dans le portail OMS affiche les vignettes pour toutes les solutions de gestion installées dans votre espace de travail ainsi que les vues personnalisées que vous avez créées. Cette même vue est disponible sur le Portail Azure. Dans votre espace de travail, sélectionnez **Résumé de l’espace de travail**.

![Page Vue d’ensemble](media/oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Comment ouvrir la Recherche dans les journaux et le Concepteur de vue ?
La **Recherche dans les journaux** et le **Concepteur de vue** sont disponibles sur la page principale et dans le menu de gauche de votre espace de travail dans le portail Azure, à côté de **Vue d’ensemble**.

## <a name="where-do-i-find-settings"></a>Où trouver les paramètres ?
Un grand nombre de paramètres dans la section **Paramètres** du portail OMS sont disponibles dans le menu **Paramètres avancés** dans le portail Azure pour l’espace de travail.

![Paramètres avancés](media/oms-portal-faq/advanced-settings.png)

Les sections suivantes fournissent une liste complète pour accéder aux paramètres qui étaient précédemment disponibles dans la section **Paramètres** du portail OMS.

### <a name="accounts"></a>Comptes 
Les paramètres des comptes sont gérés dans des emplacements différents dans le portail Azure, comme décrit dans le tableau suivant.

| Réglage dans le portail OMS | Équivalent dans le portail Azure |
|:---|:---|
| Compte Automation | Menu **Compte Automation** pour l’espace de travail. |
| Abonnement Azure & Plan de données | Menu **Niveau tarifaire** pour l’espace de travail. |
| Gestion des utilisateurs | Utilisez l’accès en fonction du rôle Azure pour [gérer les autorisations pour votre espace de travail](#how-do-i-manage-permissions). |
| Informations sur l’espace de travail | Les informations disponibles dans le menu **Espace de travail OMS** pour l’espace de travail. |

### <a name="alerts"></a>Alertes
Les règles d’alerte basées sur des requêtes Log Analytics sont désormais gérées dans [l’expérience d’alerte unifiée](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Groupes d’ordinateurs
Gérez des groupes d’ordinateurs dans le menu **Paramètres avancés** pour l’espace de travail. 

### <a name="connected-sources"></a>Sources connectées
Gérez la plupart des paramètres de source connectée dans le menu **Paramètres avancés** pour l’espace de travail. Le tableau suivant fournit des détails sur chaque section de ce menu.

| Réglage dans le portail OMS | Équivalent dans le portail Azure |
|:---|:---|
| Serveurs Windows   | Menu **Paramètres avancés** de l’espace de travail. |
| Serveurs Linux   | Menu **Paramètres avancés** de l’espace de travail. |
| Stockage Azure     | Menu **Paramètres avancés** de l’espace de travail. |
| System Center     | Menu **Paramètres avancés** de l’espace de travail. |
| Office 365        | Consultez la [documentation pour la solution de gestion Office 365](../insights/solution-office-365.md) pour plus d’informations de configuration. |
| Télémétrie Windows | Menu Paramètres de la solution. Consultez [Analytique de Windows dans le Portail Azure](/windows/deployment/update/windows-analytics-azure-portal) pour plus d’informations sur la configuration. |
| Connecteur ITSM    | Consultez [Connecter des produits/services ITSM à IT Service Management Connector](itsmc-connections.md) pour obtenir des instructions sur la connexion de votre service ITSM avec Log Analytics. |

### <a name="data"></a>Données
Gérez la plupart des paramètres de données dans le menu **Paramètres avancés** pour l’espace de travail. Le tableau suivant fournit des détails sur chaque section de ce menu.

| Réglage dans le portail OMS | Équivalent dans le portail Azure |
|:---|:---|
| Journaux des événements Windows           | Menu **Paramètres avancés** de l’espace de travail. |
| Compteurs de performances Windows | Menu **Paramètres avancés** de l’espace de travail. |
| Compteurs de performances Linux   | Menu **Paramètres avancés** de l’espace de travail. |
| Journaux d’activité IIS                     | Menu **Paramètres avancés** de l’espace de travail. |
| Champs personnalisés                | Menu **Paramètres avancés** de l’espace de travail. |
| Journaux d’activité personnalisés                  | Menu **Paramètres avancés** de l’espace de travail. |
| syslog                       | Menu **Paramètres avancés** de l’espace de travail. |
| Application Insights         | Cette solution est déconseillée maintenant que Log Analytics et Application Insights partagent le même moteur de données.  |
| Suivi des fichiers Windows        | Le menu **Suivi des modifications** dans Azure Automation. Voir [Suivre les modifications apportées à votre environnement grâce à la solution Suivi des modifications](../../automation/automation-change-tracking.md) pour plus de détails. |
| Suivi du Registre Windows        | Le menu **Suivi des modifications** dans Azure Automation. Voir [Suivre les modifications apportées à votre environnement grâce à la solution Suivi des modifications](../../automation/automation-change-tracking.md) pour plus de détails. |
| Suivi des fichiers Linux          | Le menu **Suivi des modifications** dans Azure Automation. Voir [Suivre les modifications apportées à votre environnement grâce à la solution Suivi des modifications](../../automation/automation-change-tracking.md) pour plus de détails. |

### <a name="solutions"></a>solutions
Gérer les solutions dans le menu **Solutions** de l’espace de travail. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Comment installer et supprimer des solutions de gestion ?
Dans le portail OMS, vous installez des solutions de gestion à partir de la galerie de solutions et vous les supprimez des **Paramètres**. Dans le portail Azure, [installez des solutions de gestion](../insights/solutions.md#install-a-monitoring-solution) à partir de la Place de marché Microsoft Azure. [Supprimer des solutions](../insights/solutions.md#remove-a-monitoring-solution) de la liste des solutions installées.

## <a name="how-do-i-create-and-manage-alerts"></a>Comment créer et gérer les alertes ?
Les règles d’alerte basées sur des requêtes Log Analytics sont désormais gérées dans [l’expérience d’alerte unifiée](../../azure-monitor/platform/alerts-metric.md). Consultez [Comment étendre des alertes à partir de Log Analytics dans Azure Alerts](../../azure-monitor/platform/alerts-extend-tool.md) pour plus d’informations sur la configuration et l’utilisation d’alertes dans le portail Azure.

## <a name="how-do-i-access-my-dashboards"></a>Comment accéder à mes tableaux de bord ?
La fonctionnalité **Mon tableau de bord** de Log Analytics est déconseillée. Cette fonctionnalité vous permettait d’avoir une collection privée de composants du Concepteur de vues. Elle a été remplacée par la fonctionnalité de tableau de bord Azure intégrée. Vous pouvez continuer à visualiser les données dans Log Analytics à l’aide du [Concepteur de vues](view-designer.md) pour les vues partagées. Vous pouvez également épingler des visualisations à partir de ces vues, ou pour les [requêtes individuelles](../learn/tutorial-logs-dashboards.md), dans les tableaux de bord Azure.

## <a name="how-do-i-check-my-usage"></a>Comment vérifier mon utilisation ?
Vous pouvez désormais facilement afficher et gérer votre utilisation et coût de Log Analytics en sélectionnant **Utilisation et coûts estimés** dans votre espace de travail.

![Utilisation et estimation des coûts](media/oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Puis-je toujours utiliser le portail Classic ?
Pour une durée limitée, vous pouvez toujours accéder au portail via cette URL, avec votre propre nom d’espace de travail : https://\<nom de votre espace de travail\>. portal.mms.microsoft.com. Cependant, nous vous recommandons d’utiliser le portail Azure et nous vous fournissons des commentaires dans LAUpgradeFeedback@microsoft.com sur les problèmes de blocage.

## <a name="next-steps"></a>Étapes suivantes

- [Rechercher et installer des solutions de gestion](../insights/solutions.md) à l’aide du portail Azure.
- En savoir plus sur la [Recherche dans les journaux au sein du portail Azure](../log-query/portals.md).
