---
title: Utilisation de groupes dynamiques avec Azure Automation Update Management
description: Cet article décrit le fonctionnement des groupes dynamiques avec Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830937"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utiliser des groupes dynamiques avec Update Management

Update Management permet de cibler un groupe dynamique de machines virtuelles Azure ou autres dans le cadre d’un déploiement de mises à jour. L’utilisation d’un groupe dynamique vous évite d’avoir à modifier votre déploiement pour mettre à jour les machines.

> [!NOTE]
> Les groupes dynamiques ne fonctionnent pas avec des machines virtuelles classiques.

Vous pouvez définir des groupes dynamiques pour des machines Azure ou non-Azure à partir de **Gestion des mises à jour** dans le portail Azure. Consultez [Gérer les mises à jour pour plusieurs machines virtuelles Azure](manage-update-multi.md).

Un groupe dynamique est défini par une requête qu’Azure Automation évalue au moment du déploiement. Même si une requête de groupe dynamique récupère un grand nombre de machines, Azure Automation ne peut traiter qu’un maximum de 1 000 machines à la fois. Consultez [Abonnement Azure et limites, quotas et contraintes de service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Si vous envisagez de mettre à jour plus de 1 000 machines, nous vous recommandons de répartir les mises à jour entre plusieurs planifications de mise à jour. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Définir des groupes dynamiques pour les machines Azure

Lorsque vous définissez une requête de groupe dynamique pour des machines Azure, vous pouvez utiliser les éléments suivants qui permettent de renseigner le groupe dynamique :

* Abonnement
* Groupes de ressources
* Emplacements
* Balises

![Sélection de groupes](./media/automation-update-management/select-groups.png)

Pour afficher un aperçu des résultats de votre requête de groupe dynamique, cliquez sur **Aperçu**. L’aperçu montre l’appartenance aux groupes à l’instant t. Dans l’exemple, nous recherchons les machines comportant la balise `Role` pour le groupe **BackendServer**. Si plusieurs machines présentent cette balise, elles sont ajoutées à tous les déploiements ultérieurs effectués pour ce groupe.

![aperçu des groupes](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Définir des groupes dynamiques pour les machines non-Azure

Un groupe dynamique pour les machines non-Azure utilise des recherches enregistrées, également appelées groupes d’ordinateurs. Pour apprendre à créer une recherche enregistrée, consultez [Création d’un groupe d’ordinateurs](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Une fois votre recherche enregistrée créée, vous pouvez la sélectionner dans la liste des recherches enregistrées au niveau de **Gestion des mises à jour** dans le portail Azure. Cliquez sur **Aperçu** pour afficher un aperçu des ordinateurs figurant dans la recherche enregistrée.

![Sélection de groupes](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser Update Management, consultez [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
