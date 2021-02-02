---
title: Hébergement du plan Consommation d’Azure Functions
description: Découvrez comment l’hébergement du plan Consommation d’Azure Functions vous permet d’exécuter votre code dans un environnement qui évolue dynamiquement, mais de ne payer que les ressources utilisées pendant l’exécution.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760546"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Hébergement du plan Consommation d’Azure Functions

Quand vous utilisez le plan Consommation, les instances de l’hôte Azure Functions sont ajoutées et supprimées de façon dynamique en fonction du nombre d’événements entrants. Le plan Consommation est l’option d’hébergement <em>serverless</em> pour Azure Functions.

## <a name="benefits"></a>Avantages

Le plan Consommation se met automatiquement à l’échelle, même pendant les périodes de charge élevée. Lorsque vous exécutez des fonctions dans un plan Consommation, les ressources de calcul vous sont facturées uniquement lorsque vos fonctions sont en cours d’exécution. Dans un plan Consommation, le délai d’attente de l’exécution d’une fonction arrive à expiration après une période configurable.

Pour une comparaison entre le plan Consommation et les autres types de plans et d’hébergements, consultez [Options d’échelle et d’hébergement des fonctions](functions-scale.md).

## <a name="billing"></a>Facturation

La facturation est basée sur le nombre d’exécutions, la durée d’exécution et la mémoire utilisée. L’utilisation est agrégée pour toutes les fonctions d’une même application de fonction. Pour plus d’informations, consultez la page [Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Pour en savoir plus sur la façon d’estimer les coûts pour une exécution dans le cadre d’un plan Consommation, consultez [Compréhension des coûts d’un plan Consommation](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Créer une application de fonction dans un plan Consommation

Lorsque vous créez une application de fonction dans le portail Azure, le plan Consommation est sélectionné par défaut. Lorsque vous utilisez des API pour créer votre application de fonction, vous n’avez pas besoin de créer d’abord un plan App Service comme vous le feriez pour les plans Premium et Dedicated.

Utilisez les liens suivants pour apprendre à créer une application de fonction serverless dans un plan Consommation, soit par programmation, soit dans le portail Azure :

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure portal](./functions-get-started.md)
+ [Modèle Azure Resource Manager](functions-create-first-function-resource-manager.md)

Vous pouvez également créer des applications de fonction dans un plan Consommation lorsque vous publiez un projet Functions à partir de [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) ou [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Plusieurs applications dans le même plan

Les Function App d’une même région peuvent être affectées au même plan Consommation. Avoir plusieurs applications qui s’exécutent dans le même plan Consommation n’entraîne aucun inconvénient ou impact. L’attribution de plusieurs applications au même plan Consommation n’affecte pas la résilience, la scalabilité ni la fiabilité de chaque application.

## <a name="next-steps"></a>Étapes suivantes

+ [Options d’hébergement Azure Functions](functions-scale.md)
+ [Mise à l’échelle basée sur les événements dans Azure Functions](event-driven-scaling.md)
