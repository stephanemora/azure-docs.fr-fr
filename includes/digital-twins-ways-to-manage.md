---
author: baanders
description: Fichier include pour Azure Digital Twins - Modes de gestion de l’instance
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303754"
---
Cet article montre comment effectuer différentes opérations de gestion à l’aide du [**kit SDK** .NET (C#) Azure Digital Twins](/dotnet/api/overview/azure/digitaltwins/management). Vous pouvez également créer les mêmes appels de gestion à l’aide des autres kits SDK de langage décrits dans [*Guide pratique : Utiliser les kits SDK et les API Azure Digital Twins*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> N’oubliez pas que toutes les méthodes du SDK sont disponibles dans les versions synchrones et asynchrones. Pour les appels de pagination, les méthodes asynchrones retournent `AsyncPageable<T>`, tandis que les versions synchrones retournent `Pageable<T>`.

Une autre option de gestion consiste à appeler les [**API REST**](/rest/api/azure-digitaltwins/) Azure Digital Twins directement pour ce sujet via un client REST tel que Postman. Pour obtenir des instructions sur la façon de procéder, consultez [*Procédure : Faire des demandes avec Postman*](../articles/digital-twins/how-to-use-postman.md).

Enfin, vous pouvez effectuer les mêmes opérations de gestion à l’aide de l’**interface CLI** Azure Digital Twins. Pour en savoir plus sur l’utilisation de l’interface CLI, consultez [*Guide pratique : utiliser l’interface CLI Azure Digital Twins*](../articles/digital-twins/how-to-use-cli.md).