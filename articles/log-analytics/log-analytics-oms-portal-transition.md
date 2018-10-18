---
title: Migration du portail OMS vers Azure | Microsoft Docs
description: Le portail OMS devient caduque et toutes ses fonctionnalités migrent vers le portail Azure. Cet article fournit des détails sur cette transition.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 6e3026ea3aca897ab63f4ad7d72eac438fdb7199
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389109"
---
# <a name="oms-portal-moving-to-azure"></a>Migration du portail OMS vers Azure

> [!NOTE]
> Sauf indication contraire, cet article s’applique au cloud du secteur public et au cloud public Azure.

Le portail Azure est le hub de tous les services Azure qui propose une expérience de gestion complète avec des fonctionnalités telles que des tableaux de bord pour épingler les ressources, la recherche intelligente pour rechercher des ressources et le balisage pour gérer les ressources. Pour consolider et rationaliser le flux de travail de monitoring et de gestion, nous avons commencé à ajouter les fonctionnalités du portail OMS dans le portail Azure. Toutes les fonctionnalités du portail OMS font désormais partie du portail Azure. En fait, certaines des nouvelles fonctionnalités, comme Traffic Analytics, ne sont disponibles que sur le Portail Azure. Vous pouvez faire tout ce que vous faisiez dans le portail OMS avec le portail Azure, et bien plus encore. Si ce n’est déjà fait, vous devriez commencer à utiliser le portail Azure dès aujourd’hui !

**Le portail OMS sera officiellement mis hors service le 15 janvier 2019.** Nous sommes heureux de migrer vers le portail Azure et pensons que la transition sera facile. Mais nous comprenons que les changements sont difficiles et peuvent entraîner des perturbations. Envoyez vos questions, vos commentaires ou vos problèmes à **LAUpgradeFeedback@microsoft.com**. Le reste de cet article passe en revue les scénarios clés et la feuille de route pour cette transition.

## <a name="what-is-changing"></a>Qu’est-ce qui change ? 
Les modifications suivantes sont annoncées avec l’abandon du portail OMS. Chacun de ces changements est décrit en détail dans les sections ci-dessous.

- Vous pouvez créer des [espaces de travail uniquement](#new-workspaces) dans le portail Azure.
- La nouvelle expérience de gestion des alertes [remplace la solution Alert Management](#changes-to-alerts).
- [La gestion de l’accès utilisateur](#user-access-and-role-migration) s’effectue désormais dans le portail Azure avec le contrôle d’accès en fonction du rôle Azure.
- [Application Insights Connector n’est plus nécessaire](#application-insights-connector-and-solution), dans la mesure où une même fonctionnalité est activée avec des requêtes portant sur plusieurs espaces de travail.
- [L’application mobile OMS](#oms-mobile-app) est dépréciée. 
- La [solution NSG est remplacée](#azure-network-security-group-analytics) par une fonctionnalité améliorée disponible via la solution Traffic Analytics.
- Les nouvelles connexions de System Center Operations Manager à Log Analytics exigent des [packs d’administration à jour](#system-center-operations-manager).
- Pour plus d’informations sur les changements apportés à [Update Management](../automation/automation-update-management.md), consultez [Migrer vos déploiements de mises à jour OMS vers Azure](../automation/migrate-oms-update-deployments.md).


## <a name="what-should-i-do-now"></a>Que dois-je savoir ?
Même si vous pouvez continuer à utiliser la plupart des fonctionnalités sans effectuer de migration, vous devez accomplir les tâches suivantes :

- Vous devez [migrer vos autorisations utilisateur](#user-access-and-role-migration) vers le portail Azure.
- Pour plus d’informations sur la transition de la solution Update Management, consultez [Migrer vos déploiements de mises à jour OMS vers Azure](../automation/migrate-oms-update-deployments.md).

Référez-vous à [Questions courantes sur la transition du portail OMS vers le portail Azure pour les utilisateurs Log Analytics](../log-analytics/log-analytics-oms-portal-faq.md) pour plus d’informations sur la transition vers le portail Azure. Envoyez vos commentaires, questions ou problèmes à **LAUpgradeFeedback@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Accès utilisateur et migration du rôle
La gestion des accès du portail Azure est plus complète et plus puissante que la gestion des accès du portail OMS. Consultez [Gérer les espaces de travail](log-analytics-manage-access.md#manage-accounts-and-users) pour plus d’informations sur la gestion de l’accès dans Log Analytics.

> [!NOTE]
> Les versions précédentes de cet article indiquaient que les autorisations seraient automatiquement converties depuis le portail OMS vers le portail Azure. Cette conversion automatique n’étant plus planifiée, vous devez effectuer la conversion vous-même.

Si vous avez déjà un accès approprié dans le portail Azure, vous n’avez pas besoin de changer quoi que ce soit. Dans les rares cas où vous pouvez ne pas avoir un accès approprié, votre administrateur doit vous attribuer des autorisations.

- Vous disposez d’autorisations utilisateur en lecture seule dans le portail OMS, mais d’aucune autorisation dans le portail Azure. 
- Vous disposez d’autorisations contributeur dans le portail OMS, mais uniquement d’un accès en lecture dans le portail Azure.
 
Dans ces deux cas, votre administrateur doit vous assigner manuellement le rôle approprié à partir du tableau suivant. Nous vous recommandons d’assigner ce rôle au niveau de l’abonnement ou du groupe de ressources.  Nous fournirons sous peu davantage de conseils pour ces deux cas.

| Autorisation dans le portail OMS | Rôle Azure |
|:---|:---|
| Lecture seule | Lecteur Log Analytics |
| Contributeur | Contributeur Log Analytics |
| Administrateur | Propriétaire | 
 

## <a name="new-workspaces"></a>Nouveaux espaces de travail
Vous ne pouvez plus créer d’espaces de travail à l’aide du portail OMS. Pour créer un espace de travail sur le Portail Azure, voir [Créer un espace de travail Log Analytics sur le Portail Azure](log-analytics-quick-create-workspace.md).

## <a name="changes-to-alerts"></a>Changements dans les alertes

### <a name="alert-extension"></a>Extension d’alerte  

> [!NOTE]
> Les alertes ont été entièrement étendues au Portail Azure pour le cloud public. Les règles d’alerte existantes sont consultables sur le portail OMS, mais ne peuvent être gérées que sur le Portail Azure. L’extension des alertes dans le portail Azure pour le cloud du secteur public Azure débute en octobre 2018.

Les alertes ont été [étendues au Portail Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Une fois cette opération terminée, les actions de gestion des alertes seront disponibles uniquement dans le portail Azure. Les alertes existantes continueront à être répertoriées dans le portail OMS. Si vous accédez aux alertes par programme à l’aide de l’API REST Log Analytics Alert ou du modèle Log Analytics Alert Resource, vous devrez utiliser des groupes d’actions à la place des actions dans vos appels d’API, modèles Azure Resource Manager et commandes PowerShell.

### <a name="alert-management-solution"></a>solution de gestion des alertes
Au lieu de la [solution de gestion des alertes](log-analytics-solution-alert-management.md), vous pouvez utiliser [l’interface d’alerte unifiée d’Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) pour visualiser et gérer vos alertes. Cette nouvelle expérience regroupe les alertes à partir de plusieurs sources dans Azure, notamment les alertes de journal de Log Analytics. Vous pouvez voir les distributions de vos alertes, tirer parti du regroupement automatique des alertes associées via des groupes intelligents et afficher les alertes entre plusieurs abonnements lors de l’application de filtres riches. Toutes ces fonctionnalités sont disponibles en préversion à partir du 4 juin 2018. La solution de gestion des alertes ne sera pas disponible dans le portail Azure. 

Les données collectées par la solution Alert Management (enregistrements avec un type d’alerte) se trouvent toujours dans Log Analytics tant que la solution est installée pour l’espace de travail. À partir du mois d’août 2018, le streaming des alertes à partir de la génération d’alertes unifiée dans les espaces de travail sera activée, remplaçant cette fonctionnalité. Certaines modifications du schéma sont attendues et seront annoncées à une date ultérieure.

## <a name="oms-mobile-app"></a>Application mobile OMS
L’application mobile OMS deviendra caduque, ainsi que le portail OMS. Au lieu de l’application mobile OMS, pour accéder aux informations sur votre infrastructure informatique, tableaux de bord et requêtes enregistrées, vous pouvez accéder au portail Azure directement à partir de votre navigateur sur votre appareil mobile. Pour obtenir des alertes, vous devez configurer des [groupes d’actions Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) pour recevoir des notifications sous la forme d’un appel vocal ou d’un SMS

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector et solution
[Application Insights Connector](log-analytics-app-insights-connector.md) permet d’importer des données Application Insights dans un espace de travail Log Analytics. Cette duplication des données était nécessaire pour activer la visibilité sur les données d’infrastructure et d’application.

Avec la prise en charge des [requêtes sur plusieurs ressources](log-analytics-cross-workspace-search.md), il n’est plus nécessaire de dupliquer les données. Par conséquent, la solution Application Insights existante sera déconseillée. À partir du mois d’octobre, vous ne pourrez plus lier de nouvelles ressources Application Insights aux espaces de travail Log Analytics. Les liens et les tableaux de bord existants continueront de fonctionner jusqu’au 15 janvier 2019.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
La [solution Azure Network Security Group Analytics](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) sera remplacée par [Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) récemment lancé qui offre une visibilité sur l’activité des utilisateurs et des applications sur les réseaux cloud. Traffic Analytics vous permet d’auditer l’activité réseau de votre organisation, de sécuriser les applications et les données, d’optimiser les performances de la charge de travail et de rester en conformité. 

Cette solution analyse les journaux de flux NSG et fournit des insights sur les éléments suivants.

- Les flux de trafic sur vos réseaux entre Azure et Internet, les régions de cloud public, les réseaux virtuels et les sous-réseaux.
- Les applications et les protocoles sur votre réseau, sans renifleur ni appliance de collection de flux dédiée.
- Les principaux consommateurs, les applications bavardes, les conversations de machine virtuelle dans le cloud, les zones réactives du trafic.
- Les sources et destinations du trafic entre les réseaux virtuels, les relations entre les services métier critiques et les applications.
- La sécurité, comme le trafic malveillant, les ports ouverts sur Internet, les applications ou machines virtuelles tentant d’accéder à Internet.
- L’utilisation de la capacité, qui vous aide à éliminer les problèmes de sur-provisionnement ou de sous-utilisation.

Vous pouvez continuer à utiliser les Paramètres de diagnostics pour envoyer les journaux NSG à Log Analytics, afin que vos recherches enregistrées, alertes, tableaux de bord existants continuent de fonctionner. Les clients qui ont déjà installé la solution peuvent continuer à l’utiliser jusqu’à nouvel ordre. À partir du 5 septembre, la solution Network Security Group Analytics sera supprimée de la place de marché et mise à disposition via la communauté sous forme de [Modèle de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Si le [groupe d’administration Operations Manager est déjà connecté à Log Analytics](log-analytics-om-agents.md), il continuera de fonctionner sans modification. Pour les nouvelles connexions en revanche, suivez les instructions du [Pack d’administration Microsoft System Center Operations Manager pour configurer Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Étapes suivantes
- Consultez [Questions courantes sur la transition du portail OMS vers le portail Azure pour les utilisateurs Log Analytics](log-analytics-oms-portal-faq.md) pour obtenir des conseils sur la transition depuis le portail OMS vers le portail Azure.