---
title: Mappages d’espace de travail Azure Automation et d’Analytique de journal
description: Cet article décrit les mappages autorisés entre un compte Automation et un espace de travail Log Analytique pour prendre en charge de la solution
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8afe8fed8912dd365071f1c4c5560c9f5578dcd8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133097"
---
# <a name="workspace-mappings"></a>Mappages d’espace de travail

Lorsque vous activez des solutions telles que Update Management, Change Tracking et d’inventaire ou le démarrer/arrêter des machines virtuelles au cours de la solution d’heures creuses, uniquement dans certaines régions sont prises en charge pour la liaison d’un espace de travail Analytique de journal et un compte Automation. Ce mappage s’applique uniquement au compte Automation et l’espace de travail Analytique de journal. Les ressources de création de rapports à votre espace de travail de compte Automation ou Log Analytique peuvent résider dans d’autres régions.

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
|**US Gov**||
|USGovVirginie|USGovVirginie|

<sup>1</sup> mappage EastUS pour les espaces de travail Analytique de journal aux comptes Automation n’est pas un mappage précis de la région à une autre, mais est le mappage correct.

<sup>2</sup> en raison des restrictions de capacité la région n’est pas disponible lors de la création de nouvelles ressources. Cela inclut les espaces de travail comptes Automation et d’Analytique de journal. Toutefois, les ressources liées préexistants dans la région doivent continuer à fonctionner.

## <a name="unlink-workspace"></a>Supprimer le lien de votre espace de travail

Si vous décidez que vous ne souhaitez plus intégrer votre compte Automation à un espace de travail Analytique de journal, vous pouvez dissocier votre compte directement depuis le portail Azure. Avant de continuer, vous devez d’abord supprimer la gestion de la mise à jour, le suivi des modifications et d’inventaire ou le démarrer/arrêter des machines virtuelles pendant les solutions les heures creuses si vous utilisez. Si vous ne les supprimez pas, cette procédure ne pourra aboutir. Consultez l’article relatif à la solution que vous avez importée pour comprendre sa procédure de suppression.

Après avoir supprimé ces solutions, vous pouvez effectuer les étapes suivantes pour supprimer le lien de votre compte Automation.

> [!NOTE]
> Certaines solutions, y compris les versions antérieures de la solution de supervision de SQL Azure ont peut-être créé des ressources d’automatisation et doivent également être supprimées avant d’être de l’espace de travail.

1. Dans le portail Azure, ouvrez votre compte Automation puis, dans la page de ce dernier, sélectionnez **Espace de travail lié** dans la section **Ressources associées** sur la gauche.

2. Dans la page Dissocier l’espace de travail, cliquez sur **Dissocier l’espace de travail**. Vous recevrez une invite de confirmation pour continuer.

3. Pour suivre la progression de la suppression du lien de votre espace de travail Log Analytics dans Azure Automation, sélectionnez **Notifications** dans le menu.

Si vous avez utilisé la solution de gestion de la mise à jour, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Planifications de mise à jour : chacune aura un nom correspondant aux déploiements de mise à jour que vous avez créés.

* Groupes de travail hybrides créés pour la solution : chacun a un nom similaire à machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Si vous avez utilisé la solution Démarrer/arrêter des machines virtuelles pendant les heures creuses, vous pouvez (si vous le souhaitez) supprimer les éléments suivants qui ne sont plus nécessaires après la suppression de la solution.

* Start and stop VM runbook schedules (Démarrer et arrêter les planifications de Runbook de machine virtuelle)
* Start and stop VM runbooks (Démarrer et arrêter les Runbooks de machine virtuelle)
* variables

Ou bien, vous pouvez également dissocier votre espace de travail à partir de votre compte Automation à partir de votre espace de travail Analytique de journal. Dans votre espace de travail, sélectionnez **compte Automation** sous **les ressources associées**. Dans la page compte Automation, sélectionnez **dissocier le compte**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment intégrer les solutions suivantes :

Mettre à jour de la gestion et suivi des modifications et inventaire :

* À partir d’un [machine virtuelle](../automation-onboard-solutions-from-vm.md)
* À partir de votre [compte Automation](../automation-onboard-solutions-from-automation-account.md)
* Lorsque [navigation sur plusieurs ordinateurs](../automation-onboard-solutions-from-browse.md)
* À partir d’un [runbook](../automation-onboard-solutions.md)

Démarrer/arrêter des machines virtuelles pendant les heures creuses

* [Déployer Start/Stop VMs during off-](../automation-solution-vm-management.md)