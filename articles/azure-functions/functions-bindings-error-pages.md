---
title: Conseils de gestion des erreurs Azure Functions
description: Apprenez à gérer les erreurs dans Azure Functions avec des liens vers des erreurs de liaison spécifiques.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586187"
---
# <a name="azure-functions-error-handling"></a>Gestion d’erreurs d’Azure Functions

La gestion des erreurs dans Azure Functions est importante pour éviter des pertes de données, des événements manqués et pour superviser l’intégrité de votre application.

Cet article décrit les stratégies générales de gestion des erreurs, ainsi que des liens vers des erreurs propres à la liaison.

## <a name="handling-errors"></a>Gestion des erreurs

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Codes d’erreur de liaison

Lors de l’intégration aux services Azure, vous pouvez rencontrer des erreurs provenant des API des services sous-jacents. Les informations relatives aux erreurs propres à la liaison sont disponibles dans la section **Exceptions et codes de retour** des articles suivants :

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Stockage Blob](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Stockage de files d’attente](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Stockage Table](functions-bindings-storage-table.md#exceptions-and-return-codes)
