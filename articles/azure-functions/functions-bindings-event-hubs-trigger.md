---
title: Déclencheur Azure Event Hubs pour Azure Functions
description: Apprenez à utiliser un déclencheur Azure Event Hubs dans Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608907"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Déclencheur Azure Event Hubs pour Azure Functions

Cet article explique comment utiliser un déclencheur [Azure Event Hubs](../event-hubs/event-hubs-about.md) pour Azure Functions. Azure Functions prend en charge les liaisons de déclencheur et les [liaisons de sortie](functions-bindings-event-hubs-output.md) relatives à Event Hubs.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>Paramètres host.json

Le fichier [host.json](functions-host-json.md#eventhub) contient les paramètres qui contrôlent le comportement du déclencheur Event Hub. Consultez la section [Paramètres host.json](functions-bindings-event-hubs.md#hostjson-settings) pour plus d’informations concernant les paramètres disponibles.

## <a name="next-steps"></a>Étapes suivantes

- [Écrire des événements dans un flux d’événements (liaison de sortie)](./functions-bindings-event-hubs-output.md)
