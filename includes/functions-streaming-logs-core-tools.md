---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881325"
---
#### <a name="built-in-log-streaming"></a>Streaming des journaux intégré

Utilisez l’option `logstream` pour commencer à recevoir les journaux de streaming d’une application de fonction s’exécutant dans Azure, comme dans l’exemple suivant :

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Live Metrics Stream (Flux continu de mesures)

Vous pouvez également afficher le [Flux de métriques temps réel](../articles/azure-monitor/app/live-stream.md) de votre application de fonction dans une nouvelle fenêtre de navigateur en incluant l’option `--browser`, comme dans l’exemple suivant :

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
