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
ms.openlocfilehash: d0fbdcb877e91a703306f15fdc7507fd19d534f4
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204838"
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
| Demandes  | Nombre de requêtes d’API au cours d’une période donnée |
| Durée des requêtes de passerelle | Nombre de millisecondes entre le moment où la passerelle a reçu la requête et celui où la réponse complète a été envoyée |
| Durée des requêtes back-end | Nombre de millisecondes consacrées à l’ensemble des E/S du serveur principal (connexion, envoi et réception d’octets)  |

## <a name="logs"></a>Journaux d’activité

La passerelle auto-hébergée n’envoie pas de [journaux de diagnostic](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) vers le cloud. Toutefois, il est possible de [configurer et de conserver des journaux localement](how-to-configure-local-metrics-logs.md), à l’endroit où la passerelle auto-hébergée est déployée. 

Si une passerelle est déployée dans [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/), vous pouvez activer [Azure Monitor pour les conteneurs](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) afin de collecter des journaux à partir de vos conteneurs et les afficher dans Log Analytics. 


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md)
* En savoir plus sur la [configuration et la conservation locale des journaux](how-to-configure-local-metrics-logs.md)


