---
title: Déployer des modules à grande échelle à l’aide d’Azure CLI – Azure IoT Edge
description: Utilisez l’extension IoT pour Azure CLI afin de créer des déploiements automatiques pour les groupes d’appareils IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 29aab4437b7d77b9a00b5745d68dcb5c44a4efe6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434221"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Déployer et surveiller des modules IoT Edge à grande échelle à l’aide d’Azure CLI

Créez un **déploiement automatique IoT Edge** à l’aide de l’interface de ligne de commande Azure pour gérer les déploiements en cours de plusieurs appareils à la fois. Les déploiements automatiques IoT Edge font partie de la fonctionnalité [Gestion automatique des appareils](/azure/iot-hub/iot-hub-automatic-device-management) d’IoT Hub. Les déploiements sont des processus dynamiques qui vous permettent de déployer plusieurs modules sur plusieurs appareils, de suivre l’état et l’intégrité des modules, et d’apporter des modifications si nécessaire. 

Pour plus d’informations, consultez [Comprendre les déploiements automatiques IoT Edge pour un seul ou de nombreux appareils](module-deployment-monitoring.md).

Dans cet article, vous configurez Azure CLI et l’extension IoT. Vous apprendrez ensuite à déployer des modules sur un ensemble d’appareils IoT Edge et à superviser la progression à l’aide des commandes CLI disponibles.

## <a name="cli-prerequisites"></a>Prérequis pour l’interface CLI

* Un [hub IoT](../iot-hub/iot-hub-create-using-cli.md) dans votre abonnement Azure. 
* [Appareils IoT Edge](how-to-register-device.md#prerequisites-for-the-azure-cli) avec le runtime IoT Edge installé.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) dans votre environnement. Vous devez utiliser Azure CLI version 2.0.24 ou ultérieure. Utilisez `az --version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. 
* [Extension IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations, consultez [Déployer des modules et établir des routes dans IoT Edge](module-composition.md).

Pour déployer des modules avec Azure CLI, enregistrez localement le manifeste de déploiement dans un fichier .txt. Vous utiliserez le chemin du fichier dans la section suivante au moment d’exécuter la commande permettant d’appliquer la configuration à votre appareil. 

Par exemple, voici un manifeste de déploiement de base comportant un seul module :

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Déploiement en couches

Les déploiements en couches sont des déploiements automatiques qui peuvent être empilé les uns sur les autres. Pour plus d’informations sur les déploiements en couches, consultez [Comprendre les déploiements automatiques IoT Edge pour un seul ou de nombreux appareils](module-deployment-monitoring.md). 

Les déploiements en couches peuvent être créés et gérés à l'aide d'Azure CLI comme n’importe quel déploiement automatique, à quelques différences près. Lorsqu'un déploiement en couches est créé, Azure CLI fonctionne de la même manière pour les déploiements en couches que tout autre déploiement. Pour créer un déploiement en couches, ajoutez la balise `--layered` à la commande create. 

La deuxième différence réside dans la construction du manifeste de déploiement. Alors que les déploiements automatiques standard doivent contenir les modules d’exécution du système en plus des modules utilisateur, les déploiements en couches, quant à eux, peuvent uniquement contenir les modules utilisateur. En effet, les déploiements en couches impliquent également un déploiement automatique standard sur un appareil afin de fournir les composants requis de chaque appareil IoT Edge, comme les modules d’exécution du système. 

Par exemple, voici un manifeste de déploiement en couches de base comportant un seul module : 

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

L’exemple précédent illustrait un déploiement en couches définissant `properties.desired` pour un module. Si ce déploiement en couches venait à cibler un appareil sur lequel le même module est déjà appliqué, il remplacerait les propriétés souhaitées existantes. Pour procéder à une mise à jour plutôt qu'à un remplacement des propriétés souhaitées, vous pouvez définir une nouvelle sous-section. Par exemple : 

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Pour plus d’informations sur la configuration de jumeaux de module au sein des déploiements en couches, consultez [Déploiement en couches.](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>Identifier les appareils à l’aide de balises

Avant de pouvoir créer un déploiement, vous devez être en mesure de spécifier les appareils concernés. Azure IoT Edge identifie les appareils à l’aide de **balises** dans le jumeau d’appareil. Chaque appareil peut avoir plusieurs balises que vous pouvez définir comme bon vous semble pour votre solution. Par exemple, si vous gérez un campus de bâtiments intelligents, vous pouvez ajouter les balises suivantes à un appareil :

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Pour plus d’informations sur les étiquettes et les jumeaux d’appareils, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Créer un déploiement

Vous déployez les modules sur vos appareils cibles en créant un déploiement comprenant le manifeste de déploiement ainsi que d’autres paramètres. 

Utilisez la commande [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) pour créer un déploiement :

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Utilisez la même commande avec la balise `--layered` pour créer un déploiement en couches.

La commande « deployment create » utilise les paramètres suivants : 

* **--layered** : indicateur facultatif permettant d’identifier le déploiement en tant que déploiement en couches.
* **--deployment-id** : nom du déploiement à créer dans le hub IoT. Donnez à votre déploiement un nom unique comportant au plus 128 lettres minuscules. Évitez les espaces et les caractères non valides suivants : `& ^ [ ] { } \ | " < > /`. Paramètre obligatoire. 
* **--content** : chemin du fichier JSON du manifeste de déploiement. Paramètre obligatoire. 
* **--hub-name** : nom du hub IoT dans lequel le déploiement sera créé. Le hub doit être dans l’abonnement actuel. Modifiez votre abonnement actuel avec la commande `az account set -s [subscription name]`.
* **--labels** : ajoutez des étiquettes pour faciliter le suivi de vos déploiements. Les étiquettes sont des paires Nom, Valeur qui décrivent votre déploiement. Les étiquettes utilisent le format JSON pour les noms et les valeurs. Par exemple : `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** : entrez une condition cible pour déterminer quels sont les appareils ciblés par ce déploiement. La condition est basée sur les balises de jumeau d’appareil ou sur les propriétés signalées du jumeau d’appareil et doit correspondre au format de l’expression. Par exemple : `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** : entier positif. Si deux ou plusieurs déploiements sont ciblés sur le même appareil, le déploiement ayant la valeur numérique la plus élevée pour Priority s’applique.
* **--metrics** : créez des métriques qui interrogent les propriétés signalées par edgeHub afin de suivre l’état d’un déploiement. Les métriques utilisent une entrée JSON ou un chemin d'accès. Par exemple : `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`. 

## <a name="monitor-a-deployment"></a>Surveiller un déploiement

Utilisez la commande [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) pour afficher les détails d’un déploiement unique :

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

La commande « deployment show » utilise les paramètres suivants :
* **--deployment-id** : nom du déploiement qui existe dans le hub IoT. Paramètre obligatoire. 
* **--hub-name** : nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

Inspectez le déploiement dans la fenêtre de commande. La propriété **metrics** répertorie un nombre pour chaque métrique évaluée par chaque hub :

* **targetedCount** : métrique système qui spécifie le nombre de jumeaux d’appareil dans le hub IoT qui correspondent à la condition de ciblage.
* **appliedCount** : métrique système qui spécifie le nombre d’appareils dont les jumeaux de module se sont vu appliquer le contenu de déploiement dans IoT Hub.
* **reportedSuccessfulCount** : métrique d’appareil qui spécifie le nombre d’appareils IoT Edge dans le déploiement indiquant une réussite de l’exécution du client IoT Edge.
* **reportedFailedCount** : métrique d’appareil qui spécifie le nombre d’appareils IoT Edge dans le déploiement indiquant un échec de l’exécution du client IoT Edge.

Vous pouvez afficher une liste d’ID d’appareil ou d’objets pour chacune des métriques à l’aide de la commande [az iot edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

La commande « deployment show-metric » utilise les paramètres suivants : 
* **--deployment-id** : nom du déploiement qui existe dans le hub IoT.
* **--metric-id** : nom de la métrique pour laquelle vous souhaitez voir la liste des ID d’appareil, par exemple `reportedFailedCount`.
* **--hub-name** : nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Modifier un déploiement

Quand vous modifiez un déploiement, les modifications sont répliquées immédiatement sur tous les appareils ciblés. 

Si vous mettez à jour la condition cible, les mises à jour suivantes se produisent :

* Si un appareil ne remplissait pas l’ancienne condition cible, mais qu’il remplit la nouvelle condition cible et que ce déploiement a la priorité la plus élevée pour cet appareil, ce déploiement est appliqué à l’appareil. 
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible, il désinstalle ce déploiement et prend le déploiement suivant dans l’ordre de priorité. 
* Si un appareil exécutant actuellement ce déploiement ne remplit plus la condition cible et ne remplit la condition cible d’aucun autre déploiement, aucun changement ne se produit sur l’appareil. L’appareil continue à exécuter ses modules actuels dans leur état actuel, mais il n’est plus géré dans le cadre de ce déploiement. Dès qu’il remplit la condition cible d’un autre déploiement, il désinstalle ce déploiement et prend le nouveau. 

Vous ne pouvez pas mettre à jour le contenu d’un déploiement, ce qui comprend les modules et itinéraires définis dans le manifeste de déploiement. Pour mettre à jour le contenu d’un déploiement, vous devez créer un déploiement ciblant les mêmes appareils avec une priorité plus élevée. Vous pouvez modifier certaines propriétés d’un module existant, y compris la condition, les étiquettes, les métriques et la priorité de la cible. 

Utilisez la commande [az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) pour mettre à jour un déploiement :

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

La commande « deployment update » utilise les paramètres suivants :
* **--deployment-id** : nom du déploiement qui existe dans le hub IoT.
* **--hub-name** : nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.
* **--set** : met à jour une propriété dans le déploiement. Vous pouvez mettre à jour les propriétés suivantes :
  * targetCondition : par exemple, `targetCondition=tags.location.state='Oregon'`
  * étiquettes 
  * priority
* **--add** : ajoutez une nouvelle propriété au déploiement, y compris des conditions cibles ou étiquettes. 
* **--remove** : supprimez une propriété existante, y compris des conditions cibles ou étiquettes. 


## <a name="delete-a-deployment"></a>Supprimer un déploiement

Quand vous supprimez un déploiement, tous les appareils prennent leur déploiement suivant dans l’ordre de priorité. Si vos appareils ne remplissent la condition cible d’aucun autre déploiement, les modules ne sont pas supprimés lors de la suppression du déploiement. 

Utilisez la commande [az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) pour supprimer un déploiement :

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

La commande « deployment delete » utilise les paramètres suivants : 
* **--deployment-id** : nom du déploiement qui existe dans le hub IoT.
* **--hub-name** : nom du hub IoT dans lequel le déploiement existe. Le hub doit être dans l’abonnement actuel. Basculez vers l’abonnement souhaité avec la commande `az account set -s [subscription name]`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le [déploiement des modules sur des appareils IoT Edge](module-deployment-monitoring.md).
