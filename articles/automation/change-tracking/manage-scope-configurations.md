---
title: Limiter l’étendue du déploiement de Change Tracking and Inventory d’Azure Automation
description: Cet article explique comment utiliser les configurations d’étendue pour limiter l’étendue du déploiement de Change Tracking and Inventory.
services: automation
ms.subservice: change-inventory-management
ms.date: 05/27/2021
ms.topic: conceptual
ms.openlocfilehash: 6812a0b0688efdb75d847a36d661ba87017a8b9d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110653192"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limiter l’étendue du déploiement de Change Tracking and Inventory

Cet article explique comment vous pouvez utiliser les configurations d’étendue lors de l’utilisation de la fonctionnalité [Change Tracking and Inventory](overview.md) pour déployer des modifications sur vos machines virtuelles. Pour plus d’informations, consultez [Ciblage des solutions de supervision dans Azure Monitor (préversion)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>À propos des configurations d’étendues

La configuration d’étendue est un groupe d’une ou plusieurs recherches enregistrées (requêtes) utilisé pour limiter l’étendue de Change Tracking and Inventory à des ordinateurs spécifiques. La configuration d’étendue est utilisée au sein de l’espace de travail Log Analytics pour cibler les ordinateurs à activer. Lorsque vous ajoutez un ordinateur pour recevoir des changements de la fonctionnalité, l’ordinateur est également ajouté à une recherche enregistrée dans l’espace de travail.

Par défaut, la fonctionnalité Suivi des modifications et inventaire crée un groupe d’ordinateurs nommé **ChangeTracking__MicrosoftDefaultComputerGroup** en fonction de la façon dont les machines ont été activées :

* Dans le compte Automation, vous avez sélectionné **+ Ajouter des machines virtuelles Azure**.
* Dans le compte Automation, vous avez sélectionné **Gérer les ordinateurs**, puis l’option **Activer sur tous les ordinateurs disponibles** ou **Activer sur les ordinateurs sélectionnés**.

Si l’une des méthodes ci-dessus est sélectionnée, ce groupe d’ordinateurs est ajouté à la configuration d’étendue **MicrosoftDefaultScopeConfig-ChangeTracking**. Vous pouvez également ajouter un ou plusieurs groupes d’ordinateurs personnalisés à cette étendue, en fonction de vos besoins de gestion, pour contrôler la façon dont la gestion de certains ordinateurs est activée avec la fonctionnalité Suivi des modifications et inventaire.

Pour supprimer une ou plusieurs machines de **ChangeTracking__MicrosoftDefaultComputerGroup** afin de ne plus les gérer avec la fonctionnalité Suivi des modifications et inventaire, consultez [Suppression de machines virtuelles de la fonctionnalité Suivi des modifications et inventaire](remove-vms-from-change-tracking.md).

## <a name="set-the-scope-limit"></a>Définir la limite d’étendue

Pour limiter l’étendue du déploiement de Change Tracking and Inventory :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le portail Azure, accédez à **Espaces de travail Log Analytics**. Sélectionnez votre espace de travail dans la liste.

3. Dans votre espace de travail Log Analytics, sélectionnez **Configurations d’étendue (préversion)** dans le menu gauche.

4. Sélectionnez le bouton de sélection à droite de la configuration d’étendue **MicrosoftDefaultScopeConfig-ChangeTracking**, puis **Modifier**.

5. Dans le volet de modification, développez **Sélectionner des groupes d’ordinateurs**. Le volet **Groupes d’ordinateurs** affiche les recherches enregistrées ajoutées à la configuration d’étendue. La recherche enregistrée utilisée par Update Management est :

    |Nom     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | ChangeTracking        | ChangeTracking__MicrosoftDefaultComputerGroup         |

6. Si vous avez ajouté un groupe personnalisé, il apparaît dans la liste. Pour le désélectionner, décochez la case située à gauche de l’élément. Pour ajouter un groupe personnalisé à l’étendue, sélectionnez-le, puis cliquez sur **Sélectionner** lorsque vous avez terminé vos modifications.

7. Sur la page **Modifier la configuration d’étendue**, cliquez sur **OK** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser la fonctionnalité Suivi des modifications et inventaire, consultez [Gérer Suivi des modifications et inventaire](manage-change-tracking.md).
* Pour résoudre les problèmes généraux liés à la fonctionnalité, consultez [Résoudre les problèmes liés à Suivi des modifications et inventaire](../troubleshoot/change-tracking.md).
