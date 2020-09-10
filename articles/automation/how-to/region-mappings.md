---
title: Régions prises en charge pour l’espace de travail Log Analytics lié
description: Cet article décrit les mappages de régions prises en charge entre un compte Automation et un espace de travail Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/12/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: 879c6d247a915def7b4b8d53c74bde7861e47f1d
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279803"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Régions prises en charge pour l’espace de travail Log Analytics lié

Dans Azure Automation, vous pouvez activer les fonctionnalités Update Management, Change Tracking and Inventory et Start/Stop VMs during off-hours pour vos machines virtuelles. Toutefois, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation dans votre abonnement. Les mappages de régions s’applique uniquement au compte Automation et à l’espace de travail Log Analytics. L’espace de travail Log Analytics et le compte Automation doivent se trouver dans le même abonnement, mais ils peuvent se trouver dans des groupes de ressources différents déployés dans la même région. Pour plus d’informations, consultez [Espace de travail Log Analytics et compte Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mappages pris en charge

> [!NOTE]
> Comme indiqué dans le tableau suivant, un seul mappage peut exister entre Log Analytics et Azure Automation.

Le tableau suivant renseigne sur les mappages pris en charge :

|**Région de l’espace de travail Log Analytics**|**Région Azure Automation**|
|---|---|
|**États-Unis**||
|EastUS<sup>1</sup>|USAEst2|
|WestUS2|WestUS2|
|USACentreOuest|USACentreOuest|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Asie-Pacifique**||
|AustralieSudEst|AustralieSudEst|
|AsieSudEst|AsieSudEst|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>2</sup>|ChinaEast2|
|JaponEst|JaponEst|
|**Europe**||
|RoyaumeUniSud|RoyaumeUniSud|
|WestEurope|WestEurope|
|**Gouvernement des États-Unis**||
|USGovVirginie|USGovVirginie|
|USGovArizona<sup>2</sup>|USGovArizona|

<sup>1</sup> Le mappage EastUS entre des espaces de travail Log Analytics et des comptes Automation n’est pas un mappage exact de région à région, mais c’est le mappage correct.

<sup>2</sup> Dans cette région, seul Update Management est pris en charge, d’autres fonctionnalités comme Change Tracking et Inventory ne sont pas disponibles pour l’instant.

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

* Découvrez Update Management dans [Vue d’ensemble de Update Management](../update-management/update-mgmt-overview.md).
* Découvrez Change Tracking and Inventory dans [Vue d’ensemble de Change Tracking and Inventory](../change-tracking.md).
* Découvrez Start/Stop VMs during off-hours dans [Vue d’ensemble de Start/Stop VMs during off-hours](../automation-solution-vm-management.md).
