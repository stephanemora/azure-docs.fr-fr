---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 4c16fc8a6f497a05294a9b7357b8ffc4dab285f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801265"
---
#### <a name="built-in-log-streaming"></a>Streaming des journaux intégré

Utilisez la [commande `func azure functionapp logstream`](../articles/azure-functions/functions-core-tools-reference.md#func-azure-functionapp-list-functions) pour commencer à recevoir les journaux de streaming d’une application de fonction s’exécutant dans Azure, comme dans l’exemple suivant :

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Le streaming de journaux intégré n’est pas encore activé dans Core Tools pour les applications de fonction s’exécutant sur Linux dans le cadre d’un plan Consommation. Pour ces plans d’hébergement, vous devez utiliser Flux de métriques temps réel pour voir les journaux en quasi-temps réel.

#### <a name="live-metrics-stream"></a>Live Metrics Stream (Flux continu de mesures)

Vous pouvez voir le [Flux de métriques temps réel](../articles/azure-monitor/app/live-stream.md) de votre application de fonction dans une nouvelle fenêtre de navigateur en incluant l’option `--browser`, comme dans l’exemple suivant :

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
