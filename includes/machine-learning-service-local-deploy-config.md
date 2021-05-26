---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 04/21/2021
ms.author: larryfr
ms.openlocfilehash: b960b19f67dee802b9058896d5924f883d7463a9
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846062"
---
Les entrées dans le document `deploymentconfig.json` correspondent aux paramètres pour [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). Le tableau suivant décrit le mappage entre les entités dans le document JSON et les paramètres de la méthode :

| Entité JSON | Paramètre de méthode | Description |
| ----- | ----- | ----- |
| `computeType` | N/D | La cible de calcul. Pour les cibles locales, la valeur doit être `local`. |
| `port` | `port` | Port local sur lequel exposer le point de terminaison HTTP du service. |

Ce code JSON suivant est un exemple de configuration de déploiement à utiliser avec l’interface CLI :


:::code language="json" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/deploymentconfig.json":::

Enregistrez ce code JSON dans un fichier nommé `deploymentconfig.json`.