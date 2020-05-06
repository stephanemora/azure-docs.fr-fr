---
title: Mappages d’espaces de travail Azure Automation et Log Analytics
description: Cet article décrit les mappages autorisés entre un compte Automation et un espace de travail Log Analytics pour la prise en charge d’une solution
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 607cebca3e6e8ddd95900ecdbd7041e5f7bb50cc
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82165771"
---
# <a name="workspace-mappings"></a>Mappages d’espaces de travail

Lorsque vous activez les solutions Update Management, Change Tracking et Inventory, ou encore Start/Stop VMs during off-hours, seules certaines régions sont prises en charge pour la liaison entre un espace de travail Log Analytics et un compte Automation dans votre abonnement. Ce mappage s’applique uniquement au compte Automation et à l’espace de travail Log Analytics. L’espace de travail Log Analytics et le compte Automation doivent se trouver dans le même abonnement, mais ils peuvent se trouver dans des groupes de ressources différents déployés dans la même région.

Pour plus d’informations, consultez [Espace de travail Log Analytics et compte Automation](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

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

<sup>2</sup> En raison de restrictions de capacité, la région n’est pas disponible lors de la création de ressources. Cela inclut les espaces de travail Log Analytics et les comptes Automation. Toutefois, les ressources liées préexistantes dans la région doivent continuer à fonctionner.

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

Si vous ne souhaitez plus intégrer votre compte Automation à un espace de travail Log Analytics, vous pouvez dissocier votre compte directement dans le Portail Azure. Avant de continuer, vous devez supprimer les solutions Update Management, Change Tracking et Inventory ainsi que Start/Stop VMs during off-hours, si vous les utilisez. Si vous ne les supprimez pas, vous ne pouvez pas terminer l’opération de dissociation. Examinez l’article pour chaque solution que vous activez afin de comprendre les étapes nécessaires à sa suppression.

Après avoir supprimé ces solutions, vous pouvez suivre les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, dont les versions antérieures de la solution de supervision SQL Azure, ont peut-être créé des ressources Automation et doivent également être supprimées avant de dissocier l’espace de travail.

1. À partir du Portail Azure, ouvrez votre compte Automation. Sur la page de votre compte Automation, sélectionnez **Espace de travail lié** sous **Ressources connexes**.

2. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**. Vous recevez une invite de confirmation de la poursuite de l’opération.

3. Pour suivre la progression de la suppression du lien de votre espace de travail Log Analytics dans Azure Automation, sélectionnez **Notifications** dans le menu.

4. Si vous avez utilisé la solution Update Management, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après sa suppression.

    * Planifications des mises à jour : chacune a un nom qui correspond à un déploiement de mise à jour que vous avez créé.
    * Groupes Workers hybrides créés pour la solution : chacun d’eux sera nommé sur le modèle `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`.

5. Si vous avez utilisé la solution Start/Stop VMs during off-hours, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après sa suppression.

    * Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
    * Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
    * Variables

Vous pouvez également dissocier votre espace de travail de votre compte Automation dans l’espace de travail.

1. Dans l’espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**. 
2. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment intégrer les solutions Update Management, Change Tracking et Inventory :

    * Depuis une [machine virtuelle](../automation-onboard-solutions-from-vm.md)
    * Depuis votre [compte Automation](../automation-onboard-solutions-from-automation-account.md)
    * Lorsque vous [parcourez plusieurs machines](../automation-onboard-solutions-from-browse.md)
    * Depuis un [runbook](../automation-onboard-solutions.md)

* Découvrez comment intégrer la solution Start/Stop VMs during off-hours :

    * [Vue d’ensemble de la Solution Start/stop VMs during off-hours](../automation-solution-vm-management.md)
