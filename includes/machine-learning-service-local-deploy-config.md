---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508093"
---
Les entrées dans le document `deploymentconfig.json` correspondent aux paramètres pour [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `computeType` | N/D | La cible de calcul. Pour les cibles locales, la valeur doit être `local`. |
| `port` | `port` | Port local sur lequel exposer le point de terminaison HTTP du service. |

Ce code JSON suivant est un exemple de configuration de déploiement à utiliser avec l’interface CLI :

```json
{
    "computeType": "local",
    "port": 32267
}
```