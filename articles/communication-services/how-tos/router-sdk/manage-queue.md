---
title: Gérer une file d’attente dans le routeur de travaux
titleSuffix: An Azure Communication Services how-to guide
description: Utiliser les kits SDK Azure Communication Services pour gérer le comportement d’une file d’attente
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f7aaf45cea9ced8721d9b13da4d8ab34b249f2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075080"
---
# <a name="manage-a-queue"></a>Gérer une file d’attente

Ce guide décrit les étapes pour créer et gérer une file d’attente du routeur de travaux.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Facultatif : suivez le Guide de démarrage rapide pour [commencer à utiliser le routeur de travaux](../../quickstarts/router/get-started-router.md)

## <a name="create-a-queue"></a>Créer une file d’attente

Pour créer une file d’attente simple dans le routeur de travaux, utilisez le kit SDK pour spécifier **l’ID de file d’attente**, le **nom** et un **ID de stratégie de distribution**. La stratégie de distribution doit être créée à l’avance, car le routeur de travaux valide son existence lors de la création de la file d’attente. Dans l’exemple suivant, une stratégie de distribution est créée pour contrôler la façon dont les offres de travail sont générées pour les Workers.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);

var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```
## <a name="update-a-queue"></a>Mettre à jour une file d’attente

Le kit SDK du routeur de travaux crée une nouvelle file d’attente ou met à jour une file d’attente existante quand la méthode `SetQueue` ou `SetQueueAsync` est appelée.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```

## <a name="delete-a-queue"></a>Suppression d'une file d'attente

Pour supprimer une file d’attente à l’aide du kit SDK du routeur de travaux, appelez la méthode `DeleteQueue` ou `DeleteQueueAsync` qui transmet **l’ID de file d’attente**.

```csharp
var result = await client.DeleteQueueAsync("XBOX_DEFAULT_QUEUE");
```

> [!NOTE]
> Pour supprimer une file d’attente, vous devez vérifier qu’aucun travail actif ne lui est attribué. En outre, assurez-vous qu’il n’existe aucune référence à la file d’attente dans les stratégies ou les règles de classification qui utilisent une expression pour sélectionner la file d’attente par ID à l’aide d’une valeur de chaîne.