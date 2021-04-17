---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020345"
---
#### <a name="built-in-log-streaming"></a>Streaming des journaux intégré

Utilisez l’option `logstream` pour commencer à recevoir les journaux de streaming d’une application de fonction s’exécutant dans Azure, comme dans l’exemple suivant :

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
