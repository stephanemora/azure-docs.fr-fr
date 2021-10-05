---
author: baanders
description: include pour la note sur la latence des API de requête
ms.service: digital-twins
ms.topic: include
ms.date: 09/21/2021
ms.author: baanders
ms.openlocfilehash: 2e538b6f0197f408be649bd76cb4a15fbe22b4bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557653"
---
>[!NOTE]
>Vous pouvez constater une latence jusqu’à 10 secondes pour que les changements que vous apportez aux données de votre graphe prennent effet dans les requêtes. 
>
>L’[API DigitalTwins](../articles/digital-twins/concepts-apis-sdks.md#overview-data-plane-apis) reflète les changements tout de suite, donc si vous avez besoin d’une réponse instantanée, utilisez une demande d’API [DigitalTwins GetById](/rest/api/digital-twins/dataplane/twins/digitaltwins_getbyid) ou un appel de SDK ([GetDigitalTwin](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.getdigitaltwin?view=azure-dotnet&preserve-view=true)) pour obtenir des données de jumeau au lieu d’une requête.