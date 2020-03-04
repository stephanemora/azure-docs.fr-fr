---
title: Liaisons Azure IoT Hub pour Azure Functions
description: Découvrez comment répondre aux événements envoyés à un flux d’événements IoT Hub dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589604"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Déclencheur Azure IoT Hub pour Azure Functions

Cet article explique comment utiliser des liaisons Azure Functions pour IoT Hub. La prise en charge d'IoT Hub repose sur la [liaison Azure Event Hubs](functions-bindings-event-hubs.md).

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Si les exemples de code suivants utilisent l'API Event Hub, la syntaxe donnée s'applique aux fonctions IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Écrire des événements dans un flux d’événements (liaison de sortie)](./functions-bindings-event-iot-output.md)
