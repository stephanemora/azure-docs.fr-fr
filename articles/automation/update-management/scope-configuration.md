---
title: Limiter l’étendue du déploiement Update Management d’Azure Automation
description: Cet article explique comment utiliser les configurations d’étendue pour limiter l’étendue d’un déploiement Update Management.
services: automation
ms.date: 06/03/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 230be9fcdb30e51802a2e288cde86fd8047f70f0
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854951"
---
# <a name="limit-update-management-deployment-scope"></a>Limiter l’étendue du déploiement Update Management

Cet article explique comment travailler avec des configurations d’étendue lors de l’utilisation de la fonctionnalité [Update Management](overview.md) pour déployer des mises à jour et des patchs sur vos machines. Pour plus d’informations, consultez [Ciblage des solutions de supervision dans Azure Monitor (préversion)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>À propos des configurations d’étendues

La configuration d’étendue est un groupe d’une ou plusieurs recherches enregistrées (requêtes) utilisé pour limiter l’étendue d’Update Management à des ordinateurs spécifiques. La configuration d’étendue est utilisée au sein de l’espace de travail Log Analytics pour cibler les ordinateurs qui activent la fonctionnalité. Lorsque vous ajoutez un ordinateur pour recevoir des mises à jour de Update Management, l’ordinateur est également ajouté à une recherche enregistrée dans l’espace de travail. 

Par défaut, Update Management crée un groupe d’ordinateurs nommé **Updates__MicrosoftDefaultComputerGroup** selon la façon dont vous avez activé les ordinateurs avec Update Management :

* Dans le compte Automation, vous avez sélectionné **+ Ajouter des machines virtuelles Azure**.
* Dans le compte Automation, vous avez sélectionné **Gérer les machines**, puis vous avez sélectionné l’option **Activer sur tous les ordinateurs disponibles** ou vous avez sélectionné **Activer sur les ordinateurs sélectionnés**.

Si l’une des méthodes ci-dessus est sélectionnée, ce groupe d’ordinateurs est ajouté à la configuration d’étendue **MicrosoftDefaultScopeConfig-Updates**. Vous pouvez également ajouter un ou plusieurs groupes d’ordinateurs personnalisés à cette étendue pour répondre à vos besoins de gestion et contrôler la façon dont des ordinateurs spécifiques sont activés pour la gestion avec Update Management.

Pour supprimer un ou plusieurs ordinateurs du groupe d’ordinateurs **Updates__MicrosoftDefaultComputerGroup** afin d’arrêter de les gérer avec Update Management, consultez [Supprimer des machines virtuelles d’Update Management](remove-vms.md).

## <a name="set-the-scope-limit"></a>Définir la limite de l’étendue

Pour limiter l’étendue de votre déploiement Update Management :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le portail Azure, accédez à **Espaces de travail Log Analytics**. Sélectionnez votre espace de travail dans la liste.

3. Dans votre espace de travail Log Analytics, sélectionnez **Configurations d’étendue (préversion)** dans le menu de gauche.

4. Sélectionnez le bouton de sélection à droite de la configuration d’étendue **MicrosoftDefaultScopeConfig-Updates**, puis sélectionnez **Modifier**.

5. Dans le volet de modification, développez **Sélectionner des groupes d’ordinateurs**. Le volet **Groupes d’ordinateurs** affiche les recherches enregistrées qui sont ajoutées à la configuration d’étendue. La recherche enregistrée utilisée par Update Management est :

    |Nom     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Mises à jour        | Updates__MicrosoftDefaultComputerGroup         |

6. Si vous avez ajouté un groupe personnalisé, il est affiché dans la liste. Pour le désélectionner, décochez la case à gauche de l’élément. Pour ajouter un groupe personnalisé à l’étendue, sélectionnez-le puis, lorsque vous avez terminé vos modifications, cliquez sur **Sélectionner**.

7. Sur la page **Modifier la configuration d’étendue**, cliquez sur **OK** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez [interroger des journaux d’Azure Monitor](query-logs.md) pour analyser les évaluations de mise à jour, les déploiements et autres tâches de gestion associées. Vous trouverez des requêtes prédéfinies pour vous aider à commencer.
