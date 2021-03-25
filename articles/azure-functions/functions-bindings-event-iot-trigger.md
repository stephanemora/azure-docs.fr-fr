---
title: Déclencheur Azure IoT Hub pour Azure Functions
description: Découvrez comment répondre aux événements envoyés à un flux d’événements IoT Hub dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612284"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Déclencheur Azure IoT Hub pour Azure Functions

Cet article explique comment utiliser des liaisons Azure Functions pour IoT Hub. La prise en charge d'IoT Hub repose sur la [liaison Azure Event Hubs](functions-bindings-event-hubs.md).

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Si les exemples de code suivants utilisent l'API Event Hub, la syntaxe donnée s'applique aux fonctions IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>Propriétés host.json

Le fichier [host.json](functions-host-json.md#eventhub) contient les paramètres qui contrôlent le comportement du déclencheur Event Hub. Consultez la section [Paramètres host.json](functions-bindings-event-iot.md#hostjson-settings) pour plus d’informations concernant les paramètres disponibles.

## <a name="next-steps"></a>Étapes suivantes

- [Écrire des événements dans un flux d’événements (liaison de sortie)](./functions-bindings-event-iot-output.md)
