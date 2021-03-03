---
title: Configurer les métriques et les journaux cloud pour la passerelle auto-hébergée de la Gestion des API Azure | Microsoft Docs
description: Découvrez comment configurer les journaux et les métriques cloud pour la passerelle auto-hébergée de la Gestion des API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: c420c62e6f8f09a2b29398590cdb4ad410e5d296
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574053"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configurer les journaux et les métriques cloud pour la passerelle auto-hébergée de la Gestion des API Azure

Cet article fournit des informations détaillées sur la configuration des métriques et des journaux cloud pour la [passerelle auto-hébergée](./self-hosted-gateway-overview.md).

La passerelle auto-hébergée doit être associée à un service de gestion des API et nécessite une connectivité TCP/IP sortante vers Azure via le port 443. La passerelle utilise la connexion sortante pour envoyer des données de télémétrie à Azure, si elle est configurée à cet effet. 

## <a name="metrics"></a>Mesures
Par défaut, la passerelle auto-hébergée émet un certain nombre de métriques via [Azure Monitor](https://azure.microsoft.com/services/monitor/), comme le fait la passerelle gérée [dans le cloud](api-management-howto-use-azure-monitor.md). 

Cette fonctionnalité peut être activée ou désactivée à l’aide de la clé `telemetry.metrics.cloud` située dans le ConfigMap du déploiement de la passerelle. Voici une description des configurations disponibles :

| Champ  | Default | Description |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Permet la journalisation via Azure Monitor. La valeur peut être `true` ou `false`. |


Voici un exemple de configuration :

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

La passerelle auto-hébergée émet les métriques suivantes via Azure Monitor :

| Métrique  | Description |
| ------------- | ------------- |
| Demandes  | Nombre de demandes API au cours de la période |
| Durée des requêtes de passerelle | Nombre de millisecondes entre le moment où la passerelle a reçu la requête et celui où la réponse complète a été envoyée |
| Durée des requêtes back-end | Nombre de millisecondes consacrées à l’ensemble des E/S du serveur principal (connexion, envoi et réception d’octets)  |

## <a name="logs"></a>Journaux d’activité

La passerelle auto-hébergée n’envoie pas de [journaux de diagnostic](./api-management-howto-use-azure-monitor.md#activity-logs) vers le cloud. Toutefois, il est possible de [configurer et de conserver des journaux localement](how-to-configure-local-metrics-logs.md), à l’endroit où la passerelle auto-hébergée est déployée. 

Si une passerelle est déployée dans [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/), vous pouvez activer [Azure Monitor pour les conteneurs](../azure-monitor/containers/container-insights-overview.md) afin de collecter des journaux à partir de vos conteneurs et les afficher dans Log Analytics. 


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md)
* En savoir plus sur la [configuration et la conservation locale des journaux](how-to-configure-local-metrics-logs.md)
