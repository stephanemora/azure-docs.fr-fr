---
title: Azure IoT Edge et Azure IoT Central | Microsoft Docs
description: Découvrez comment utiliser Azure IoT Edge avec une application IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 2233e6f40c1023f1b02543f4e234b00422f6f77f
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077191"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Connecter des appareils Azure IoT Edge à une application Azure IoT Central

Azure IoT Edge déplace l’analytique et la logique métier personnalisée du cloud vers des appareils, afin que votre organisation puisse se concentrer sur les insights métier plutôt que sur la gestion des données. Effectuez un scale-out de votre solution IoT en plaçant votre logique métier dans des conteneurs standard, déployez ces conteneurs sur vos appareils, puis surveillez-les à partir du cloud.

Cet article aborde les points suivants :

* Comment des appareils IoT Edge se connectent à une application IoT Central.
* Comment utiliser IoT Central pour gérer vos appareils IoT Edge.

Pour en savoir plus sur les IoT Edge, consultez [Qu’est-ce qu’Azure IOT Edge ?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge est constitué de trois composants :

* Les *modules IoT Edge* sont des conteneurs qui exécutent les services Azure, les services partenaire ou votre propre code. Ils sont déployés sur des appareils IoT Edge et s’exécutent localement sur ces appareils. Pour en savoir plus, consultez [Présentation des modules Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
* Le *runtime IoT Edge* s’exécute sur chaque appareil IoT Edge et gère les modules déployés sur chaque appareil. Le runtime se compose de deux modules IoT Edge : *agent IoT Edge* et *hub IoT Edge*. Pour en savoir plus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](../../iot-edge/iot-edge-runtime.md).
* Une *interface basée sur le cloud* permet de superviser et de gérer des appareils IoT Edge à distance. IoT Central est un exemple d’interface cloud.

Un appareil IoT Edge peut être :

* un appareil autonome composé de modules ;
* un *appareil de passerelle* avec des appareils en aval qui s’y connectent.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge en tant que passerelle

Un appareil IoT Edge peut fonctionner en tant que passerelle fournissant une connexion entre d’autres appareils en aval sur le réseau et votre application IoT Central.

Il existe deux modèles de passerelle :

* Dans le modèle de *passerelle transparente* , le module IOT Edge Hub se comporte comme une solution IoT Central et gère les connexions à partir d’appareils inscrits dans celle-ci. Les messages passent des appareils en aval à IoT Central comme s’il n’y avait pas de passerelle entre eux.

* Dans le modèle de *passerelle de traduction*, les appareils qui ne peuvent pas se connecter à IoT Central par eux-mêmes se connectent à un module IoT Edge personnalisé à la place. Le module dans l’appareil IoT Edge traite les messages entrants d’appareils en aval, puis les transfère à IoT Central.

Les modèles de passerelle transparente et de traduction ne s’excluent pas mutuellement. Un seul appareil IoT Edge peut fonctionner à la fois en tant que passerelle transparente et passerelle de traduction.

Pour en savoir plus sur les modèles de passerelle IoT Edge, consultez le [Guide pratique pour utiliser un appareil IoT Edge en tant que passerelle](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relations des appareils en aval avec une passerelle et des modules

Les appareils en aval peuvent se connecter à un appareil de passerelle IoT Edge via le module *hub IoT Edge*. Dans ce scénario, l’appareil IoT Edge est une passerelle transparente :

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagramme de la passerelle transparente" border="false":::

Les appareils en aval peuvent également se connecter à un appareil de passerelle IoT Edge via un module personnalisé. Dans le scénario suivant, les appareils en aval se connectent via un module personnalisé *Modbus*. Dans ce scénario, l’appareil IoT Edge est une passerelle de traduction :

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagramme de la connexion via un module personnalisé" border="false":::

Le diagramme suivant illustre les connexions à un appareil de passerelle IoT Edge via les deux types de modules. Dans ce scénario, l’appareil IoT Edge est à la fois une passerelle transparente et une passerelle de traduction :

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagramme de connexion via les deux modules de connexion" border="false":::

Les appareils en aval peuvent se connecter à un appareil de passerelle IoT Edge via plusieurs modules personnalisés. Le diagramme suivant montre des appareils en aval qui se connectent via un module personnalisé Modbus, un module personnalisé BLE et le module *hub IoT Edge*.

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagramme de connexion à l’aide de plusieurs modules personnalisés" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>Appareils IoT Edge dans IoT Central

Les appareils IoT Edge peuvent utiliser des jetons de *signature d’accès partagé* ou des certificats X.509 pour s’authentifier auprès d’IoT Central. Vous pouvez inscrire manuellement vos appareils IoT Edge dans IoT Central avant qu’ils ne se connectent pour la première fois, ou utiliser le service Device Provisioning pour gérer l’inscription. Pour plus d’informations, consultez [Se connecter à Azure IoT Central](concepts-get-connected.md).

IoT Central utilise des [modèles d’appareil](concepts-device-templates.md) pour définir la manière dont IOT central interagit avec un appareil. Par exemple, un modèle d’appareil spécifie ce qui suit :

* les types de données de télémétrie et les propriétés qu’un appareil envoie afin qu’IoT Central puisse les interpréter et créer des visualisations ;
* les commandes auxquelles un appareil répond afin qu’IoT Central puisse afficher une interface utilisateur qu’un opérateur peut utiliser pour appeler les commandes.

Un appareil IoT Edge peut envoyer des données de télémétrie, synchroniser des valeurs de propriété et répondre à des commandes de la même façon qu’un appareil standard. Ainsi, un appareil IoT Edge a besoin d’un modèle d’appareil dans IoT Central.

### <a name="iot-edge-device-templates"></a>Modèles d’appareils IoT Edge

Les modèles d’appareils IoT Central utilisent des modèles pour décrire les fonctionnalités des appareils. Le diagramme suivant illustre la structure du modèle pour un appareil IoT Edge :

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Structure du modèle pour un appareil IoT Edge connecté à IoT Central" border="false":::

IoT Central modélise ainsi un appareil IoT Edge :

* Chaque modèle d’appareil IoT Edge comprend un modèle de capacité.
* Un modèle de capacité de module est généré pour chaque module personnalisé répertorié dans le manifeste de déploiement.
* Une relation est établie entre chaque modèle de capacité de module et un modèle d’appareil.
* Un modèle de capacité de module implémente une ou plusieurs interfaces de module.
* Chaque interface de module contient des données de télémétrie, des propriétés et des commandes.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>Manifestes de déploiement IoT Edge et modèles d’appareils IoT Central

Dans IoT Edge, vous pouvez déployer et gérer la logique métier sous forme de modules. Les modules IoT Edge sont la plus petite unité de calcul gérée par IoT Edge. Ils peuvent contenir des services Azure (par exemple, Azure Stream Analytics) ou du code spécifique de votre solution.

Un *manifeste de déploiement* IoT Edge répertorie les modules IoT Edge à déployer sur l’appareil et comment les configurer. Pour plus d’informations, consultez [Découvrir comment déployer des modules et établir des routes dans IoT Edge](../../iot-edge/module-composition.md).

Dans Azure IoT Central, vous importez un manifeste de déploiement afin de créer un modèle d’appareil pour l’appareil IoT Edge.

L’extrait de code suivant montre un exemple de manifeste de déploiement IoT Edge :

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
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

Dans l’extrait de code précédent, vous pouvez voir ce qui suit :

* Il existe trois modules. Les modules système *agent IoT Edge* et *hub IoT Edge* qui sont présents dans chaque manifeste de déploiement. Le module **SimulatedTemperatureSensor** personnalisé.
* Les images de modules publics sont extraites d’un référentiel Azure Container Registry qui ne requiert pas d’informations d’identification pour se connecter. Pour les images de modules privés, définissez les informations d’identification du registre de conteneurs à utiliser dans le paramètre `registryCredentials` pour le module *agent IoT Edge*.
* Le module **SimulatedTemperatureSensor** personnalisé a deux propriétés, `"SendData": true` et `"SendInterval": 10`.

Lorsque vous importez ce manifeste de déploiement dans une application IoT Central, il génère le modèle d’appareil suivant :

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Capture d’écran montrant le modèle d’appareil créé à partir du manifeste de déploiement.":::

Dans la capture d’écran précédente, vous pouvez voir ce qui suit :

* Un module appelé **SimulatedTemperatureSensor**. Les modules système *agent IoT Edge* et *hub IoT Edge* n’apparaissent pas dans le modèle.
* Interface appelée **gestion** qui comprend deux propriétés accessibles en écriture, appelées **SendData** et **SendInterval**.

Le manifeste de déploiement n’inclut pas d’informations sur la télémétrie que le module **SimulatedTemperatureSensor** envoie ou les commandes auxquelles il répond. Ajoutez ces définitions au modèle d’appareil manuellement avant de le publier.

Pour en savoir plus, consultez [Tutoriel : Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Mettre à jour un manifeste de déploiement

Lorsque vous remplacez le manifeste de déploiement, tous les appareils IoT Edge connectés téléchargent le nouveau manifeste et mettent à jour leurs modules. Toutefois, IoT Central ne met pas à jour les interfaces dans le modèle d’appareil avec des modifications apportées à la configuration du module. Par exemple, si vous remplacez le manifeste présenté dans l’extrait de code précédent par le manifeste suivant, vous ne voyez pas automatiquement la propriété **SendUnits** dans l’interface **gestion** dans le modèle d’appareil. Ajoutez manuellement la nouvelle propriété à l’interface **gestion** afin qu’IoT Central la reconnaisse :

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
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Mettre à jour le runtime IoT Edge

Pour savoir où vous pouvez exécuter le runtime IoT Edge, consultez [Systèmes pris en charge par Azure IoT Edge](../../iot-edge/support.md).

Vous pouvez également installer le runtime IoT Edge dans les environnements suivants :

* [Installer ou désinstaller Azure IoT Edge pour Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Installer et approvisionner Azure IoT Edge pour Linux sur un appareil Windows (version préliminaire)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Exécuter Azure IoT Edge sur des machines virtuelles Ubuntu dans Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md).

## <a name="iot-edge-gateway-devices"></a>Appareils de passerelle IoT Edge

Si vous avez sélectionné un appareil IoT Edge comme appareil de passerelle, vous pouvez ajouter des relations en aval aux modèles d’appareil pour les appareils que vous souhaitez connecter à l’appareil de passerelle.

Pour en savoir plus, consultez [Guide pratique pour connecter des appareils via une passerelle transparente IoT Edge](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante suggérée est d’apprendre à [Développer vos propres modules IoT Edge](../../iot-edge/module-development.md).
