---
title: Utiliser des groupes dynamiques avec Azure Update Management
description: Cet article décrit le fonctionnement des groupes dynamiques avec Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420395"
---
# <a name="use-dynamic-groups-with-update-management"></a>Utiliser des groupes dynamiques avec Update Management

Update Management permet de cibler un groupe dynamique de machines virtuelles Azure ou non-Azure pour les déploiements de mises à jour. Ces groupes sont évalués au moment du déploiement, donc vous n’êtes pas obligé de modifier votre déploiement pour ajouter des machines.

## <a name="azure-machines"></a>Machines Azure

Ces groupes sont définis par une requête et, au démarrage d’un déploiement de mise à jour, les membres de ce groupe sont évalués. Les groupes dynamiques ne fonctionnent pas avec des machines virtuelles classiques. Quand vous définissez votre requête, les éléments suivants peuvent être utilisés ensemble pour remplir le groupe dynamique :

* Subscription
* Groupes de ressources
* Emplacements
* Balises

![Sélection de groupes](./media/automation-update-management/select-groups.png)

Pour prévisualiser les résultats d’un groupe dynamique, cliquez sur le bouton **Aperçu**. Cet aperçu montre l’appartenance au groupe à ce moment-là. Dans cet exemple, nous recherchons les machines dont la balise **Role** (Rôle) est définie à **BackendServer**. Si plusieurs machines ont cette balise, elles seront ajoutées à tous les déploiements ultérieurs effectués pour ce groupe.

![aperçu des groupes](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Machines non-Azure

Pour les machines non-Azure, les recherches enregistrées, également appelées groupes d’ordinateurs, sont utilisées pour créer le groupe dynamique. Pour apprendre à créer une recherche enregistrée, consultez [Création d’un groupe d’ordinateurs](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Une fois que votre groupe est créé, vous pouvez le sélectionner dans la liste des recherches enregistrées. Cliquez sur **Aperçu** pour afficher un aperçu des ordinateurs figurant dans la recherche enregistrée à ce moment-là.

![Sélection de groupes](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un groupe dynamique, vous pouvez [créer un déploiement de mise à jour](automation-tutorial-update-management.md)
