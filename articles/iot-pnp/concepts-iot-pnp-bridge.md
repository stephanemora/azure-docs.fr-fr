---
title: Pont IoT Plug-and-Play | Microsoft Docs
description: Découvrez le pont IoT Plug-and-Play et comment l’utiliser pour connecter des appareils existants attachés à une passerelle Windows ou Linux en tant qu’appareils IoT Plug-and-Play.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a45efd90043ecb4d457db7ed39651f1a9b5bbd4d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890605"
---
# <a name="iot-plug-and-play-bridge"></a>Pont IoT Plug-and-Play

Le pont IoT Plug-and-Play est une application open source permettant de connecter des appareils existants attachés à une passerelle Windows ou Linux en tant qu’appareils IoT Plug-and-Play. Après l’installation et la configuration de l’application sur votre machine Windows ou Linux, vous pouvez l’utiliser pour connecter des appareils attachés à un hub IoT. Vous pouvez utiliser le pont pour mapper les interfaces IoT Plug-and-Play aux télémétries envoyées par les appareils attachés, utiliser les propriétés des appareils et appeler des commandes.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Sur le côté gauche, deux capteurs existants sont attachés (les deux filaires et sans fil) à un PC Windows ou Linux contenant un pont IoT Plug-and-Play. Le pont Plug-and-Play IoT se connecte ensuite à un hub IoT sur le côté droit":::

Le pont IoT Plug-and-Play peut être déployé comme un exécutable autonome sur tout appareil IoT, PC, serveur ou toute passerelle exécutant Windows 10 ou Linux. Il peut également être compilé dans le code de votre application. Un fichier JSON de configuration simple indique au pont IoT Plug-and-Play que les appareils/périphériques attachés doivent être exposés à Azure.

## <a name="supported-protocols-and-sensors"></a>Protocoles et capteurs pris en charge

Le pont IoT Plug-and-Play prend en charge les types suivants de périphériques par défaut, avec des liens vers la documentation de l’adaptateur :

|Périphérique|Windows|Linux|
|---------|---------|---------|
|L’[adaptateur de capteur Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) connecte les capteurs compatibles Bluetooth Low Energy (BLE) activés.       |Oui|Non|
|La [carte de la caméra](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) connecte les caméras sur un appareil Windows 10.               |Oui|Non|
|L'[adaptateur Modbus](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) connecte les capteurs sur un appareil Modbus.              |Oui|Oui|
|L’[adaptateur MQTT](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) connecte des appareils qui utilisent un répartiteur MQTT.                  |Oui|Oui|
|L'[adaptateur SerialPnP](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) connecte les appareils qui communiquent via une connexion série.               |Oui|Oui|
|Les [périphériques USB Windows](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) utilisent une liste de classes d’interfaces d’appareils prises en charge par les adaptateurs pour connecter des appareils qui ont un ID matériel spécifique.  |Oui|Non applicable|

Pour savoir comment étendre le pont IoT Plug-and-Play afin de prendre en charge des protocoles d’appareils supplémentaires, consultez [Étendre le pont IoT Plug-and-Play](howto-author-pnp-bridge-adapter.md). Pour savoir comment créer et déployer le pont IoT Plug-and-Play, consultez [Créer et déployer le pont IoT Plug-and-Play](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Architecture de pont IoT Plug-and-Play

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Sur le côté gauche, vous verrez plusieurs cases indiquant les différents périphériques reliés à un PC Windows ou Linux contenant le pont IoT Plug-and-Play. Du haut, une zone intitulée Configuration pointe vers le pont. Le pont se connecte ensuite à un hub IoT sur le côté droit du diagramme.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Adaptateurs de pont IoT Plug-and-Play

Le pont IoT Plug-and-Play prend en charge un ensemble de cartes du pont IoT Plug-and-Play pour différents types d’appareils. Un *manifeste d’adaptateur* définit statiquement les adaptateurs sur un pont.

Le gestionnaire de cartes de pont utilise le manifeste pour identifier et appeler les fonctions de l’adaptateur. Le gestionnaire d’adaptateur appelle uniquement la fonction de création sur les adaptateurs de pont requis par les composants d’interface répertoriés dans le fichier de configuration. Une instance d’adaptateur est créée pour chaque composant IoT Plug-and-Play.

Un adaptateur de pont crée et acquiert un descripteur d’interface numérique. L’adaptateur utilise ce handle pour lier la fonctionnalité de l’appareil à la représentation numérique.

À l’aide des informations contenues dans le fichier de configuration, l’adaptateur de pont utilise les techniques suivantes pour activer la communication complète de l’appareil sur les représentations numériques via le pont :

- Établit un canal de communication directement.
- Crée un observateur d’appareils pour attendre qu’un canal de communication soit disponible.

### <a name="configuration-file"></a>Fichier de configuration

Le pont d’IoT Plug-and-Play utilise un fichier de configuration JSON qui spécifie les éléments suivants :

- Comment se connecter à un hub IoT ou une application IoT Central : Les options incluent les chaînes de connexion, les paramètres d’authentification ou le service de provisionnement des appareils (DPS).
- Emplacement des modèles de capacité IoT Plug-and-Play utilisés par le pont. Le modèle définit les fonctionnalités d’un appareil IoT Plug-and-Play et est statique et immuable.
- Une liste de composants d’interface IoT Plug-and-Play et les informations suivantes pour chaque composant :
- ID d’interface et nom du composant.
- Adaptateur de pont requis pour interagir avec le composant.
- Informations sur l’appareil dont l’adaptateur de pont a besoin pour établir la communication avec l’appareil. Par exemple, l’ID matériel ou les informations spécifiques d’un adaptateur, d’une interface ou d’un protocole.
- Un sous-type d’adaptateur de pont facultatif ou une configuration d’interface si l’adaptateur prend en charge plusieurs types de communication avec des appareils similaires. L’exemple montre comment configurer un composant de capteur Bluetooth :

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Liste facultative de paramètres d’adaptateur de pont globaux. Par exemple, l’adaptateur de pont de capteur Bluetooth possède un dictionnaire des configurations prises en charge. Un composant d’interface qui requiert l’adaptateur de capteur Bluetooth peut choisir l’une de ces configurations comme `blesensor_identity`:

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Télécharger le pont IoT Plug-and-Play

Vous pouvez télécharger une version pré-générée du pont avec des adaptateurs pris en charge parmi les [versions du pont IoT Plug-and-Play](https://github.com/Azure/iot-plug-and-play-bridge/releases) et étendre la liste des ressources pour la version la plus récente. Téléchargez la version la plus récente de l’application pour votre système d’exploitation.

Vous pouvez également télécharger et afficher le code source du [pont IoT Plug-and-Play sur GitHub](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une vue d’ensemble de l’architecture d’un pont IoT Plug-and-Play, les étapes suivantes permettent d’en savoir plus :

- [Exemple de connexion d’un pont IoT Plug-and-Play s’exécutant sur Linux ou Windows à IoT Hub](./howto-use-iot-pnp-bridge.md)
- [Créer et déployer un pont IoT Plug-and-Play](howto-build-deploy-extend-pnp-bridge.md)
- [Étendre un pont IoT Plug-and-Play](howto-author-pnp-bridge-adapter.md)
- [Pont IoT Plug-and-Play sur GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
