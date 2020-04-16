---
title: Installer des conteneurs Speech
titleSuffix: Azure Cognitive Services
description: Détaille les options de configuration de graphique Helm pour la reconnaissance vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422074"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Reconnaissance vocale (sous-graphique : charts/speechToText)

Pour remplacer le graphique en parapluie, ajoutez le préfixe `speechToText.` à n’importe quel paramètre pour le rendre plus spécifique. Il remplacera le paramètre correspondant. Par exemple, `speechToText.numberOfConcurrentRequest` remplace `numberOfConcurrentRequest`.

|Paramètre|Description|Default|
| -- | -- | -- |
| `enabled` | Indique si la **reconnaissance vocale** est activée ou non. | `false` |
| `numberOfConcurrentRequest` | Le nombre de requêtes simultanées pour le service de **reconnaissance vocale**. Ce graphique calcule automatiquement les ressources processeur et mémoire en fonction de cette valeur. | `2` |
| `optimizeForAudioFile`| Indique si le service doit optimiser pour l’entrée audio via des fichiers audio. Si `true`, ce graphique alloue plus de ressources processeur au service. | `false` |
| `image.registry`| Le registre d’image docker pour la **reconnaissance vocale**. | `containerpreview.azurecr.io` |
| `image.repository` | Le référentiel d’image docker pour la **reconnaissance vocale**. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | La balise d’image docker pour la **reconnaissance vocale**. | `latest` |
| `image.pullSecrets` | Les secrets de l’image pour l’extraction de l’image docker de **reconnaissance vocale**. | |
| `image.pullByHash`| Indique si l’image docker est extraite par hachage. Si `true`, `image.hash` est requis. | `false` |
| `image.hash`| Le hachage d’image docker pour la **reconnaissance vocale**. Utilisé uniquement si `image.pullByHash: true`.  | |
| `image.args.eula` (requis) | Indique que vous avez accepté la licence. La seule valeur valide est `accept` | |
| `image.args.billing` (requis) | La valeur de l’URI de point de terminaison de facturation est disponible dans la page Vue d’ensemble de Speech du portail Azure. | |
| `image.args.apikey` (requis) | Utilisé pour le suivi des informations de facturation. ||
| `service.type` | Le type de service Kubernetes du service de **reconnaissance vocale**. Consultez les [Instructions relatives aux types de service Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) pour plus d’informations et pour vérifier la prise en charge des fournisseurs cloud. | `LoadBalancer` |
| `service.port`|  Le port du service de **reconnaissance vocale**. | `80` |
| `service.annotations` | Les annotations de **reconnaissance vocale** pour les métadonnées du service. Les annotations sont des paires clé-valeur. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Indique si [l’Autoscaler de pods élastique](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) est activé. Si `true`, le `speech-to-text-autoscaler` sera déployé dans le cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Indique si le [Budget de perturbation de pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) est activé. Si `true`, le `speech-to-text-poddisruptionbudget` sera déployé dans le cluster Kubernetes. | `true` |