---
title: Régions prises en charge pour l’espace de travail Log Analytics lié
description: Cet article décrit les mappages de régions prises en charge entre un compte Automation et un espace de travail Log Analytics.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: d594d5a9dd263817023d2b5987c03aa50828191c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191860"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Régions prises en charge pour l’espace de travail Log Analytics lié

Dans Azure Automation, vous pouvez activer les fonctionnalités Update Management, Change Tracking and Inventory et Start/Stop VMs during off-hours pour vos machines virtuelles. Toutefois, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation dans votre abonnement. Les mappages de régions s’applique uniquement au compte Automation et à l’espace de travail Log Analytics. L’espace de travail Log Analytics et le compte Automation doivent se trouver dans le même abonnement, mais ils peuvent se trouver dans des groupes de ressources différents déployés dans la même région. Pour plus d’informations, consultez [Espace de travail Log Analytics et compte Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mappages pris en charge

Le tableau suivant renseigne sur les mappages pris en charge :

|**Région de l’espace de travail Log Analytics**|**Région Azure Automation**|
|---|---|
|**États-Unis**||
|EastUS<sup>1</sup>|USAEst2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Asie-Pacifique**||
|AustralieSudEst|AustralieSudEst|
|AsieSudEst|AsieSudEst|
|CentralIndia|CentralIndia|
|JaponEst|JaponEst|
|**Europe**||
|RoyaumeUniSud|RoyaumeUniSud|
|WestEurope|WestEurope|
|**Gouvernement des États-Unis**||
|USGovVirginie|USGovVirginie|

<sup>1</sup> Le mappage EastUS entre des espaces de travail Log Analytics et des comptes Automation n’est pas un mappage exact de région à région, mais c’est le mappage correct.

<sup>2</sup> En raison de restrictions de capacité, la région n’est pas disponible lorsque vous créez des ressources. Cela inclut les espaces de travail Log Analytics et les comptes Automation. Toutefois, les ressources liées préexistantes dans la région doivent continuer à fonctionner.

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

* Découvrez Update Management dans [Vue d’ensemble de Update Management](../automation-update-management.md).
* Découvrez Change Tracking and Inventory dans [Vue d’ensemble de Change Tracking and Inventory](../change-tracking.md).
* Découvrez Start/Stop VMs during off-hours dans [Vue d’ensemble de Start/Stop VMs during off-hours](../automation-solution-vm-management.md).
