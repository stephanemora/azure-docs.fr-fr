---
title: Passerelle Azure IoT Edge imbriquée à deux niveaux du cache connecté Microsoft avec proxy non authentifié sortant | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Tutoriel pour la passerelle Azure IoT Edge imbriquée à deux niveaux du cache connecté Microsoft avec proxy non authentifié sortant
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811883"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Exemple de scénario de déploiement de la préversion du cache connecté Microsoft : passerelle Azure IoT Edge imbriquée à deux niveaux avec proxy non authentifié sortant

Le diagramme ci-dessous décrit le scénario où une passerelle Azure IoT Edge a un accès direct aux ressources CDN et fait office de parent d’une autre passerelle Azure IoT Edge. La passerelle IoT Edge enfant fait office de parent d’un appareil de nœud terminal Azure IoT, par exemple, un Raspberry Pi. L’enfant Azure IoT Edge et l’appareil Azure IoT sont isolés d’Internet. L’exemple ci-dessous montre la configuration de deux niveaux de passerelles Azure IoT Edge, mais sans limite de profondeur des hôtes en amont qui sont pris en charge par le cache connecté Microsoft. Les options de création de conteneur de cache connecté Microsoft ne sont pas différentes par rapport aux exemples précédents.

Pour plus d’informations sur la configuration des déploiements en couche de passerelles Azure IoT Edge, reportez-vous à la documentation [Connecter des appareils IoT Edge en aval - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11). Notez aussi que quand vous déployez Azure IoT Edge, le cache connecté Microsoft et des modules personnalisés, tous les modules doivent se trouver dans le même registre de conteneurs.

>[!Note]
>Quand vous déployez Azure IoT Edge, le cache connecté Microsoft et des modules personnalisés, tous les modules doivent se trouver dans le même registre de conteneurs.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Cache connecté Microsoft imbriqué" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuration de la passerelle parente
1. Ajoutez le module Cache connecté Microsoft à votre déploiement d’appareil de passerelle Azure IoT Edge dans Azure IoT Hub (consultez [Prise en charge des appareils déconnectés](connected-cache-disconnected-device-update.md) pour plus d’informations sur l’obtention du module).
2. Ajoutez les variables d’environnement pour le déploiement. Voici un exemple des variables d’environnement.

    **Variables d’environnement**

    | Nom                          | Valeur                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Voir les descriptions de la [variable d’environnement](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Voir les descriptions de la [variable d’environnement](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Voir les descriptions de la [variable d’environnement](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Ajoutez les options de création de conteneur pour le déploiement. Il n’existe aucune différence dans les options de création de conteneur MCC par rapport à l’exemple précédent. Voici un exemple des options de création de conteneur.

### <a name="container-create-options"></a>Options de création de conteneur

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Configuration de la passerelle enfant

>[!Note]
>Si vous avez des conteneurs répliqués utilisés dans votre configuration dans votre propre registre privé, vous devez modifier les paramètres de configuration de config.toml et les paramètres d’exécution dans le déploiement de votre module. Pour plus d’informations, consultez [Connecter des appareils IoT Edge en aval - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices).


1. Modifiez le chemin de l’image pour l’agent Edge comme illustré dans l’exemple ci-dessous :

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Modifiez les paramètres d’exécution du hub Edge et de l’agent Edge dans le déploiement Azure IoT Edge, comme illustré dans cet exemple :
    
    * Sous Hub Edge, dans le champ Image, entrez ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```.
    * Sous Agent Edge, dans le champ Image, entrez ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```.

3. Ajoutez le module Cache connecté Microsoft à votre déploiement d’appareil de passerelle Azure IoT Edge dans Azure IoT Hub.

   * Choisissez un nom pour votre module : ```ConnectedCache```
   * Modifiez l’URI de l’image : ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Ajoutez le même ensemble de variables d’environnement et d’options de création de conteneur que celles utilisées dans le déploiement parent.
>[!Note]
>CACHE_NODE_ID doit être unique.  Les valeurs CUSTOMER_ID et CUSTOMER_KEY sont identiques à celles du parent. (voir [Configurer le cache connecté Microsoft](connected-cache-configure.md))

Pour une validation du bon fonctionnement du cache connecté Microsoft, exécutez la commande suivante dans le terminal de l’appareil IoT Edge hébergeant le module ou sur n’importe quel appareil sur le réseau. Remplacez \<Azure IoT Edge Gateway IP\> par l’adresse IP ou le nom d’hôte de votre passerelle IoT Edge. (Consultez les détails de la variable d’environnement pour plus d’informations sur la visibilité de ce rapport.)

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```