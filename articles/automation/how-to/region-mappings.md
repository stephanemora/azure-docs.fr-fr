---
title: Régions prises en charge pour l’espace de travail Log Analytics lié
description: Cet article décrit les mappages des régions prises en charge entre un compte Automation et un espace de travail Log Analytics en relation avec certaines fonctionnalités d’Azure Automation.
ms.date: 02/17/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0599dcb57b46d1e48b4035acac8b64edbbe06912
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720169"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Régions prises en charge pour l’espace de travail Log Analytics lié

Dans Azure Automation, vous pouvez activer les fonctionnalités Update Management, Change Tracking and Inventory et Start/Stop VMs during off-hours pour vos serveurs et machines virtuelles. Ces fonctionnalités dépendent d’un espace de travail Log Analytics et nécessitent donc de lier l’espace de travail à un compte Automation. Toutefois, seules certaines régions sont prises en charge pour les lier ensemble. En général, le mappage n’est *pas* applicable si vous envisagez de lier un compte Automation à un espace de travail pour lequel ces fonctionnalités ne sont pas activées.

Les mappages abordés ici s’appliquent uniquement à la liaison de l’espace de travail Log Analytics à un compte Automation. Ils ne s’appliquent pas aux machines virtuelles qui sont connectées à l’espace de travail lié au compte Automation. Les machines virtuelles ne sont pas limitées aux régions prises en charge par l’espace de travail Log Analytics donné. Elles peuvent se situer dans n’importe quelle région. N’oubliez pas que si les machines virtuelles se trouvent dans une autre région, cela peut affecter les exigences réglementaires locales et nationales, ou les exigences de conformité de votre entreprise. Avoir des machines virtuelles dans une autre région peut également introduire des frais de bande passante des données.

Avant de connecter des machines virtuelles à un espace de travail dans une autre région, vous devez passer en revue les exigences et les coûts potentiels pour confirmer et comprendre les implications juridiques et financières.

Cet article fournit les mappages pris en charge afin d’activer et d’utiliser correctement ces fonctionnalités dans votre compte Automation.

Pour plus d’informations, consultez [Espace de travail Log Analytics et compte Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mappages pris en charge

> [!NOTE]
> Comme indiqué dans le tableau suivant, un seul mappage peut exister entre Log Analytics et Azure Automation.

Le tableau suivant renseigne sur les mappages pris en charge :

|**Région de l’espace de travail Log Analytics**|**Région Azure Automation**|
|---|---|
|**États-Unis**||
|EastUS<sup>1</sup>|USAEst2|
|USAEst2<sup>2</sup>|USAEst|
|WestUS|WestUS|
|WestUS2|WestUS2|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|USACentreOuest|USACentreOuest|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Asie-Pacifique**||
|AustraliaEast|AustraliaEast|
|AustralieSudEst|AustralieSudEst|
|EastAsia|EastAsia|
|AsieSudEst|AsieSudEst|
|CentralIndia|CentralIndia|
|ChineEst2<sup>3</sup>|ChinaEast2|
|JaponEst|JaponEst|
|**Europe**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|RoyaumeUniSud|RoyaumeUniSud|
|WestEurope|WestEurope|
|SwitzerlandNorth|SwitzerlandNorth|
|**Gouvernement des États-Unis**||
|USGovVirginie|USGovVirginie|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> Le mappage EastUS entre des espaces de travail Log Analytics et des comptes Automation n’est pas un mappage exact de région à région, mais c’est le mappage correct.

<sup>2</sup> Le mappage USAEst2 entre des espaces de travail Log Analytics et des comptes Automation n'est pas un mappage exact de région à région, mais il s'agit du mappage qui convient.

<sup>3</sup> Dans cette région, seul Update Management est pris en charge ; d'autres fonctionnalités comme Suivi des modifications et inventaire ne sont pas disponibles pour l'instant.

## <a name="unlink-a-workspace"></a>Supprimer la liaison d’un espace de travail

Si vous ne souhaitez plus intégrer votre compte Automation à un espace de travail Log Analytics, vous pouvez dissocier votre compte directement dans le Portail Azure. Avant de continuer, vous devez [supprimer](move-account.md#remove-features) les fonctionnalités Update Management, Change Tracking and Inventory ainsi que Start/Stop VMs during off-hours, si vous les utilisez. Si vous ne les supprimez pas, vous ne pouvez pas terminer l’opération de dissociation.

Une fois les fonctionnalités supprimées, vous pouvez suivre les étapes ci-dessous pour supprimer la liaison de votre compte Automation.

> [!NOTE]
> Certaines fonctionnalités, dont les versions antérieures d’Azure SQL Monitoring, ont peut-être créé des ressources Automation qui doivent également être supprimées avant de dissocier l’espace de travail.

1. À partir du Portail Azure, ouvrez votre compte Automation. Sur la page de votre compte Automation, sélectionnez **Espace de travail lié** sous **Ressources connexes**.

2. Dans la page Dissocier l’espace de travail, sélectionnez **Dissocier l’espace de travail**. Vous recevez une invite de confirmation de la poursuite de l’opération.

3. Pour suivre la progression de la suppression de la liaison du compte depuis votre espace de travail Log Analytics par Azure Automation, sélectionnez **Notifications** dans le menu.

4. Si vous avez utilisé Update Management, vous pouvez le cas échéant supprimer les éléments suivants qui ne sont plus nécessaires :

    * Planifications des mises à jour : chacune a un nom qui correspond à un déploiement de mise à jour que vous avez créé.
    * Groupes de Workers hybrides créés pour la fonctionnalité : chacun porte un nom similaire à `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`.

5. Si vous avez utilisé Start/Stop VMs during off-hours, vous pouvez le cas échéant supprimer les éléments suivants qui ne sont plus nécessaires :

    * Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
    * Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
    * Variables

Vous pouvez également dissocier votre espace de travail de votre compte Automation dans l’espace de travail.

1. Dans l’espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**.
2. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez Update Management dans [Vue d’ensemble de Update Management](../update-management/overview.md).
* Découvrez Change Tracking and Inventory dans [Vue d’ensemble de Change Tracking and Inventory](../change-tracking/overview.md).
* Découvrez Start/Stop VMs during off-hours dans [Vue d’ensemble de Start/Stop VMs during off-hours](../automation-solution-vm-management.md).