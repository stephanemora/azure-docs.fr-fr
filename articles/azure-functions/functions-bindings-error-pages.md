---
title: Conseils de gestion des erreurs d’Azure Functions | Microsoft Docs
description: Fournit des conseils généraux pour la gestion des erreurs qui se produisent dans lors de l’exécution de vos fonctions, ainsi que des liens vers des rubriques d’erreurs spécifiques d’une liaison.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: ef8f2d5a63f7924097362f6aa0ebc78cc0f6455f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480704"
---
# <a name="azure-functions-error-handling"></a>Gestion d’erreurs d’Azure Functions

Cette rubrique fournit des conseils généraux pour la gestion des erreurs qui se produisent dans lors de l’exécution de vos fonctions. Elle fournit également des liens vers des rubriques décrivant des erreurs spécifiques d’une liaison qui peuvent se produire. 

## <a name="handing-errors-in-functions"></a>Gestion des erreurs dans Azure Functions
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Codes d’erreur de liaison

Lors de l’intégration avec les services Azure, vous pouvez rencontrer des erreurs provenant des API des services sous-jacents. Vous pouvez trouver des liens vers de la documentation relative aux codes d’erreur pour ces services dans la section **Exceptions et codes de retour** du déclencheur et des rubriques de référence de liaison suivants :

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Stockage Blob](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hubs d'événements](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Stockage de files d’attente](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Stockage Table](functions-bindings-storage-table.md#exceptions-and-return-codes)
