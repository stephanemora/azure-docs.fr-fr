---
title: Superviser les rubriques et les abonnements aux événements - Azure Event Grid IoT Edge | Microsoft Docs
description: Superviser les rubriques et les abonnements aux événements
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 79b223de7a0a0cfdaf799b1f80e585a2a55f7e82
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76851320"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Superviser les rubriques et les abonnements aux événements

Event Grid sur Edge expose un certain nombre de métriques pour les rubriques et abonnements aux événements au format [d'exposition Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Cet article décrit les métriques disponibles et comment les activer.

## <a name="enable-metrics"></a>Activer les mesures

Configurez le module pour émettre des métriques en définissant la variable d’environnement `metrics__reporterType` sur `prometheus` dans les options de création du conteneur :

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Les métriques seront disponibles au niveau `5888/metrics` du module pour http et `4438/metrics` pour HTTPS. Par exemple, `http://<modulename>:4438/metrics?api-version=2019-01-01-preview` pour http. À ce stade, un module de métriques peut interroger le point de terminaison pour collecter les métriques comme dans cet [exemple d'architecture](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métriques disponibles

Les rubriques et les abonnements aux événements émettent des métriques pour vous donner des insights en termes de remise des événements et de performances des modules.

### <a name="topic-metrics"></a>Métriques de rubrique

| Métrique | Description |
| ------ | ----------- |
| EventsReceived | Nombre d’événements publiés dans la rubrique
| UnmatchedEvents | Nombre d’événements publiés dans la rubrique ne correspondant pas à un abonnement à un événement et supprimés
| SuccessRequests | Nombre de requêtes de publication entrantes reçues par la rubrique
| SystemErrorRequests | Nombre de requêtes de publication entrantes ayant échoué en raison d’une erreur système interne
| UserErrorRequests | Nombre de requêtes de publication entrantes ayant échoué en raison d'une erreur de l’utilisateur, par exemple un JSON mal formé
| SuccessRequestLatencyMs | Latence de la réponse de requête publication en millisecondes


### <a name="event-subscription-metrics"></a>Métriques des abonnements aux événements

| Métrique | Description |
| ------ | ----------- |
| deliverySuccessCounts | Nombre d’événements correctement remis au point de terminaison configuré
| deliveryFailureCounts | Nombre de tentatives de remise d’événement au point de terminaison configuré ayant échoué
| deliverySuccessLatencyMs | Latence des événements correctement remis en millisecondes
| deliveryFailureLatencyMs | Latence des échecs de remise d'événement en millisecondes
| systemDelayForFirstAttemptMs | Délai système des événements avant la première tentative de remise en millisecondes
| deliveryAttemptsCount | Nombre de tentatives de remise d’événement - Ayant abouti ou échoué
| expiredCounts | Nombre d’événements dont la remise est impossible 