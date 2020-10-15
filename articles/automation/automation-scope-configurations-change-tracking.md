---
title: Limiter l’étendue du déploiement de Change Tracking and Inventory d’Azure Automation
description: Cet article explique comment utiliser les configurations d’étendue pour limiter l’étendue du déploiement de Change Tracking and Inventory.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185906"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limiter l’étendue du déploiement de Change Tracking and Inventory

Cet article explique comment vous pouvez utiliser les configurations d’étendue lors de l’utilisation de la fonctionnalité [Change Tracking and Inventory](change-tracking.md) pour déployer des modifications sur vos machines virtuelles. Pour plus d’informations, consultez [Ciblage des solutions de supervision dans Azure Monitor (préversion)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>À propos des configurations d’étendues

La configuration d’étendue est un groupe d’une ou plusieurs recherches enregistrées (requêtes) utilisé pour limiter l’étendue de Change Tracking and Inventory à des ordinateurs spécifiques. La configuration d’étendue est utilisée au sein de l’espace de travail Log Analytics pour cibler les ordinateurs à activer. Lorsque vous ajoutez un ordinateur pour recevoir des changements de la fonctionnalité, l’ordinateur est également ajouté à une recherche enregistrée dans l’espace de travail.

## <a name="set-the-scope-limit"></a>Définir la limite d’étendue

Pour limiter l’étendue du déploiement de Change Tracking and Inventory :

1. Dans votre compte Automation, sélectionnez **Espace de travail lié** sous **Ressources connexes**.

2. Cliquez sur **Accéder à l’espace de travail**.

3. Sélectionnez **Configurations d’étendue (préversion)** , sous **Sources de données de l’espace de travail**.

4. Sélectionnez les points de suspension sur la droite de la configuration d’étendue `MicrosoftDefaultScopeConfig-ChangeTracking`, puis cliquez sur **Modifier**. 

5. Dans le volet de modification, choisissez **Sélectionner des groupes d’ordinateurs**. Le volet Groupes d’ordinateurs affiche les recherches enregistrées et utilisées pour créer la configuration d’étendue. La recherche enregistrée utilisée par Change Tracking and Inventory est :

    |Nom     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Sélectionnez la recherche enregistrée pour afficher et modifier la requête utilisée pour remplir le groupe. L’image suivante montre la requête et ses résultats :

    ![Recherches enregistrées](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser la fonctionnalité Suivi des modifications et inventaire, consultez [Gérer Suivi des modifications et inventaire](change-tracking-file-contents.md).
* Pour résoudre les problèmes généraux liés à la fonctionnalité, consultez [Résoudre les problèmes liés à Suivi des modifications et inventaire](troubleshoot/change-tracking.md).
