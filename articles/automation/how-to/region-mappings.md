---
title: Mappages d’espaces de travail Azure Automation et Log Analytics
description: Cet article décrit les mappages autorisés entre un compte Automation et un espace de travail Log Analytics pour la prise en charge d’une solution
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849511"
---
# <a name="workspace-mappings"></a>Mappages d’espaces de travail

Lorsque vous activez des solutions comme Update Management, Change Tracking et Inventory, ou Start/Stop VMs during off-hours, seules certaines régions sont prises en charge pour la liaison entre un espace de travail Log Analytics et un compte Automation. Ce mappage s’applique uniquement au compte Automation et à l’espace de travail Log Analytics. Les ressources créant des rapports pour votre espace de travail Log Analytics ou votre compte Automation peuvent résider dans d’autres régions.

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

<sup>1</sup> Le mappage EastUS entre des espaces de travail Log Analytics et des comptes Automation n’est pas un mappage précis entre deux région, mais le mappage correct.

<sup>2</sup> En raison de restrictions de capacité, la région n’est pas disponible lors de la création de ressources. Cela inclut les espaces de travail Log Analytics et les comptes Automation. Toutefois, les ressources liées préexistantes dans la région doivent continuer à fonctionner.

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

Si vous ne souhaitez plus intégrer votre compte Automation dans un espace de travail Log Analytics, vous pouvez supprimer son lien directement dans le portail Microsoft Azure. Avant de continuer, vous devez d’abord supprimer les solutions Update Management, Change Tracking et Inventory, et Start/Stop VMs during off-hours, si vous les utilisez. Si vous ne les supprimez pas, cette procédure ne pourra pas aboutir. Consultez l’article relatif à la solution que vous avez importée pour comprendre sa procédure de suppression.

Après avoir supprimé ces solutions, vous pouvez effectuer les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, y compris les versions antérieures de la solution de supervision de SQL Azure ont peut-être créé des ressources d’automatisation et doivent également être supprimées avant d’être de l’espace de travail.

1. Dans le portail Azure, ouvrez votre compte Automation puis, dans la page de ce dernier, sélectionnez **Espace de travail lié** dans la section **Ressources associées** sur la gauche.

2. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**. Vous recevez une invite de confirmation de la poursuite de l’opération.

3. Pour suivre la progression de la suppression du lien de votre espace de travail Log Analytics dans Azure Automation, sélectionnez **Notifications** dans le menu.

Si vous avez utilisé la solution de gestion de la mise à jour, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Planifications de mise à jour : chacune aura un nom correspondant aux déploiements de mise à jour que vous avez créés.

* Les groupes Worker hybrides créés pour la solution : chacun d’entre eux sera nommé de la même façon que `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Si vous avez utilisé la solution Démarrer/arrêter des machines virtuelles pendant les heures creuses, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
* Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
* Variables

Vous pouvez également dissocier votre espace de travail de votre compte Automation à partir de votre espace de travail Log Analytics. Dans votre espace de travail, sélectionnez **Compte Automation** sous **Ressources connexes**. Sur la page du compte Automation, sélectionnez **Dissocier le compte**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment intégrer les solutions suivantes :

Update Management, Change Tracking et Inventory :

* Depuis une [machine virtuelle](../automation-onboard-solutions-from-vm.md)
* Depuis votre [compte Automation](../automation-onboard-solutions-from-automation-account.md)
* Lorsque vous [parcourez plusieurs machines](../automation-onboard-solutions-from-browse.md)
* Depuis un [runbook](../automation-onboard-solutions.md)

Démarrer/arrêter des machines virtuelles pendant les heures creuses

* [Déployer Start/Stop VMs during off-hours](../automation-solution-vm-management.md)
