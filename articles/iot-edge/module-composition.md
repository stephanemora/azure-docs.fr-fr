---
title: Déployer des modules et des routes avec des manifestes de déploiement - Azure IoT Edge
description: Découvrez comment un manifeste de déploiement déclare les modules à déployer, comment les déployer et comment créer des itinéraires de messages entre eux.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 31a83d3edb1bc297fc53b089384ab940482e5b28
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665829"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Découvrez comment déployer des modules et établir des itinéraires dans IoT Edge.

Chaque appareil IoT Edge exécute au moins deux modules : $edgeAgent et $edgeHub, qui font partie du runtime IoT Edge. Un appareil IoT Edge peut exécuter des modules supplémentaires pour autant de processus que nécessaire. Utilisez un manifeste de déploiement pour indiquer à un appareil les modules à installer et comment les configurer pour qu'ils fonctionnent ensemble. 

Le *manifeste de déploiement* est un document JSON qui décrit :

* Le jumeau de module de l'**agent IoT Edge**, comprend trois composants. 
  * L’image conteneur pour chaque module qui s’exécute sur l’appareil.
  * Les informations d’identification pour accéder aux registres de conteneurs privés contenant des images de module.
  * Des instructions sur la manière de créer et de gérer chaque module.
* le jumeau de module du **hub IoT Edge**, qui inclut le mode de flux des messages entre les modules et finalement vers IoT Hub ;
* le cas échéant, les propriétés souhaitées de tout jumeau de module supplémentaire.

Tous les appareils IoT Edge doivent être configurés avec un manifeste de déploiement. Un runtime IoT Edge nouvellement installé renvoie un code d’erreur tant qu’il n’est pas configuré avec un manifeste valide. 

Dans les didacticiels Azure IoT Edge, vous générez un manifeste de déploiement via un Assistant dans le portail Azure IoT Edge. Vous pouvez également appliquer un manifeste de déploiement par programmation à l’aide du Kit de développement logiciel (SDK) REST ou IoT Hub Service. Pour plus d’informations, consultez [Comprendre les déploiements IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Créer un manifeste de déploiement

À un niveau élevé, un manifeste de déploiement est une liste des jumeaux de module configurés avec leurs propriétés souhaitées. Un manifeste de déploiement indique à un appareil IoT Edge (ou à un groupe d’appareils) les modules à installer et comment les configurer. Les manifestes de déploiement incluent les *propriétés souhaitées* pour chaque jumeau de module. Les appareils IoT Edge rapportent les *propriétés signalées* pour chaque module. 

Deux modules sont nécessaires dans chaque manifeste de déploiement : `$edgeAgent` et `$edgeHub`. Ces modules font partie du runtime IoT Edge qui gère l’appareil IoT Edge et les modules en cours d’exécution sur ce dernier. Pour plus d’informations sur ces modules, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Outre les deux modules de runtime, vous pouvez ajouter jusqu'à 20 modules de votre choix pour s’exécuter sur un appareil IoT Edge. 

Un manifeste de déploiement ne contenant que le runtime IoT Edge (edgeAgent et edgeHub) est valide.

Les manifestes de déploiement suivent la structure suivante :

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configurer des modules

Définir comment le runtime IoT Edge installe les modules dans votre déploiement. L’agent IoT Edge est le composant de runtime qui gère l’installation, les mises à jour et les rapports d’état d’un appareil IoT Edge. Pour cette raison, le jumeau du module $edgeAgent a besoin des informations de configuration et de gestion de tous les modules. Ces informations incluent les paramètres de configuration pour l’agent IoT Edge proprement dit. 

Pour obtenir une liste complète des propriétés qui peuvent ou doivent être incluses, consultez [Propriétés de l’agent IoT Edge et du hub IoT Edge](module-edgeagent-edgehub.md).

Les propriétés $edgeAgent suivent cette structure :

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Déclarer des itinéraires

Le hub IoT Edge gère la communication entre les modules, IoT Hub et tous les appareils de nœuds terminaux. Par conséquent, le jumeau de module $edgeHub contient une propriété souhaitée appelée *itinéraires* qui indique la façon dont les messages sont transmis au sein d’un déploiement. Vous pouvez avoir plusieurs itinéraires dans le même déploiement.

Les itinéraires sont déclarés dans les propriétés souhaitées **$edgeHub** avec la syntaxe suivante :

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Chaque itinéraire requiert une source et un récepteur, mais la condition est facultative et peut être utilisée pour filtrer les messages. 


### <a name="source"></a>Source

La source spécifie d'où proviennent les messages. IoT Edge peut acheminer les messages d’appareils de modules ou appareils de nœud terminal. 

À l’aide des kits de développement logiciel (SDK) IoT, les modules peuvent déclarer des files d’attente de sortie spécifiques pour leurs messages à l’aide de la classe ModuleClient. Les files d’attente de sortie ne sont pas nécessaires, mais utiles pour gérer plusieurs itinéraires. Les appareils de nœud terminal peuvent utiliser la classe DeviceClient des kits de développement logiciel (SDK) IoT pour envoyer des messages aux appareils de passerelle IoT Edge de la même manière qu'ils enverraient des messages à IoT Hub. Pour plus d'informations, consultez [Comprendre et utiliser les kits de développement logiciel (SDK) Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).

La propriété source peut être l’une des valeurs suivantes :

| Source | Description |
| ------ | ----------- |
| `/*` | Tous les messages appareil-à-cloud ou les notifications de changement de jumeau à partir de n’importe quel module ou appareil de nœud terminal |
| `/twinChangeNotifications` | Tout changement de jumeau (propriétés signalées) en provenance de n’importe quel module ou appareil de nœud terminal |
| `/messages/*` | Tout message appareil-à-cloud envoyé par un module via une sortie ou non, pour par un appareil de nœud terminal |
| `/messages/modules/*` | Tout message appareil-à-cloud envoyé par un module via une sortie ou aucune |
| `/messages/modules/<moduleId>/*` | Tout message appareil-à-cloud envoyé par un module spécifique, via une sortie ou non |
| `/messages/modules/<moduleId>/outputs/*` | Tout message appareil-à-cloud envoyé par un module spécifique, via une sortie |
| `/messages/modules/<moduleId>/outputs/<output>` | Tout message appareil-à-cloud envoyé par un module spécifique via une sortie spécifique |

### <a name="condition"></a>Condition
La condition est facultative dans une déclaration d’itinéraire. Si vous souhaitez transmettre tous les messages de la source au récepteur, il suffit d’omettre la clause **WHERE** entièrement. Vous pouvez également utiliser le [langage de requête IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) pour filtrer certains messages ou types de messages qui répondent à la condition. Les itinéraires IoT Edge ne prennent pas en charge les messages de filtrage basés sur les propriétés ou balises de jumeaux. 

Les messages qui transitent entre les modules dans IoT Edge sont mis en forme de la même manière que les messages qui passent entre les appareils et Azure IoT Hub. Tous les messages sont au format JSON et ont les paramètres **systemProperties**, **appProperties** et **corps**. 

Vous pouvez générer des requêtes autour de chacun des trois paramètres avec la syntaxe suivante : 

* Propriétés du système : `$<propertyName>` ou `{$<propertyName>}`
* Propriétés de l’application : `<propertyName>`
* Propriétés du corps : `$body.<propertyName>` 

Pour obtenir des exemples sur la façon de créer des requêtes pour les propriétés de message, consultez [Expressions de requête des itinéraires des messages appareil-à-cloud](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Par exemple, sur IoT Edge, si vous voulez filtrer des messages reçus sur un appareil de passerelle à partir d’un appareil de nœud terminal. Les messages provenant des modules incluent une propriété système appelée **connectionModuleId**. Par conséquent, si vous souhaitez router des messages à partir d’appareils de nœud terminal directement vers IoT Hub, utilisez l’itinéraire suivant pour exclure les messages de modules :

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Récepteur
Le récepteur définit où les messages sont envoyés. Seuls les modules et IoT Hub peuvent recevoir des messages. Les messages ne peut pas être acheminés vers d’autres appareils. Il n’existe aucune option de caractère générique dans la propriété de récepteur. 

La propriété de récepteur peut être l’une des valeurs suivantes :

| Récepteur | Description |
| ---- | ----------- |
| `$upstream` | Envoyer le message à IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Envoyer le message à une entrée spécifique d’un module spécifique |

IoT Edge offre la garantie « Au moins une remise ». Le hub IoT Edge stocke les messages localement dans le cas où un itinéraire ne peut pas remettre le message à son récepteur. C’est le cas, par exemple, si le hub IoT Edge ne peut pas se connecter à IoT Hub ou que le module cible n’est pas connecté.

Le hub IoT Edge stocke les messages jusqu’à l’heure spécifiée dans la propriété `storeAndForwardConfiguration.timeToLiveSecs` des [propriétés souhaitées du hub IoT Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Définir ou mettre à jour les propriétés souhaitées 

Le manifeste de déploiement spécifie les propriétés souhaitées pour chaque module déployé sur l’appareil IoT Edge. Les propriétés souhaitées dans le manifeste de déploiement remplacent les propriétés souhaitées actuellement spécifiées dans le jumeau de module.

Si vous ne spécifiez pas de propriétés souhaitées d’un jumeau de module dans le manifeste de déploiement, IoT Hub ne modifiera d’aucune façon le jumeau de module. Au lieu de cela, vous pouvez définir programmatiquement les propriétés souhaitées.

Les mêmes mécanismes que ceux qui vous permettent de modifier des jumeaux d’appareils sont utilisés pour modifier des jumeaux de modules. Pour plus d’informations, consultez le [guide du développeur des jumeaux de module](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Exemple de manifeste de déploiement

L’exemple suivant montre à quoi peut ressembler un document de manifeste de déploiement valide.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir la liste complète des propriétés qui peuvent ou doivent être incluses dans $edgeAgent et $edgeHub, consultez [Propriétés de l’agent IoT Edge et du hub IoT Edge](module-edgeagent-edgehub.md).

* Maintenant que vous savez comment les modules IoT Edge sont utilisés, vous pouvez lire [Comprendre les exigences et les outils de développement de modules IoT Edge](module-development.md).
