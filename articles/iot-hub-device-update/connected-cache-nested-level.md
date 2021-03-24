---
title: Passerelle Azure IoT Edge imbriquée à deux niveaux du cache connecté Microsoft avec proxy non authentifié sortant | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Tutoriel pour la passerelle Azure IoT Edge imbriquée à deux niveaux du cache connecté Microsoft avec proxy non authentifié sortant
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615378"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Exemple de scénario de déploiement de la préversion du cache connecté Microsoft : passerelle Azure IoT Edge imbriquée à deux niveaux avec proxy non authentifié sortant

Étant donné le diagramme ci-dessous, dans ce scénario, il existe une passerelle Azure IoT Edge et un appareil Azure IoT Edge en aval, une passerelle Azure IoT Edge apparentée à une autre passerelle Azure IoT Edge et un serveur proxy sur la zone DMZ du système informatique. Voici un exemple de variables d’environnement de cache connecté Microsoft qui seraient définies dans l’expérience utilisateur du portail Azure pour les deux modules MCC déployés sur les passerelles Azure IoT Edge. L’exemple illustré montre la configuration de deux niveaux de passerelles Azure IoT Edge, mais il n’existe pas de limite à la profondeur des hôtes en amont pris en charge par le cache connecté Microsoft. Il n’existe aucune différence dans les options de création du conteneur MCC par rapport aux exemples précédents.

Pour plus d’informations sur la configuration des déploiements en couche de passerelles Azure IoT Edge, reportez-vous à la documentation [Connecter des appareils IoT Edge en aval - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true). Notez aussi que quand vous déployez Azure IoT Edge, le cache connecté Microsoft et des modules personnalisés, tous les modules doivent se trouver dans le même registre de conteneurs.

Le diagramme ci-dessous décrit le scénario où une passerelle Azure IoT Edge en tant qu’accès direct aux ressources CDN agit comme parent d’une autre passerelle Azure IoT Edge qui agit elle-même comme parent d’un appareil de nœud terminal Azure IoT, comme un Raspberry Pi. Seul le parent de la passerelle Azure IoT Edge dispose d’une connectivité Internet aux ressources CDN, et les deux enfants Azure IoT Edge et l’appareil Azure IoT sont isolés d’Internet. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Cache connecté Microsoft imbriqué" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Configuration de la passerelle parente

1. Ajoutez le module Cache connecté Microsoft à votre déploiement d’appareil de passerelle Azure IoT Edge dans Azure IoT Hub.
2. Ajoutez les variables d’environnement pour le déploiement. Voici un exemple des variables d’environnement.

    **Variables d’environnement**

    | Nom                 | Value                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Consultez la description de la variable d’environnement ci-dessus. |
    | CUSTOMER_ID                   | Consultez la description de la variable d’environnement ci-dessus. |
    | CUSTOMER_KEY                  | Consultez la description de la variable d’environnement ci-dessus. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | Adresse IP ou nom de domaine complet du serveur proxy                     |

3. Ajoutez les options de création de conteneur pour le déploiement. Il n’existe aucune différence dans les options de création de conteneur MCC par rapport à l’exemple précédent. Voici un exemple des options de création de conteneur.

### <a name="container-create-options"></a>Options de création de conteneur

```markdown
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
```

## <a name="child-gateway-configuration"></a>Configuration de la passerelle enfant

>[!Note]
>Si vous avez des conteneurs répliqués utilisés dans votre configuration dans votre propre registre privé, vous devez modifier les paramètres de configuration de config.toml et les paramètres d’exécution dans le déploiement de votre module. Pour plus d’informations, reportez-vous au [Tutoriel : Créer une hiérarchie d’appareils IoT Edge - Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device).

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

4. Ajoutez les mêmes variables d’environnement et les mêmes options de création de conteneur que celles utilisées dans le déploiement parent.

Pour une validation du bon fonctionnement du cache connecté Microsoft, exécutez la commande suivante dans le terminal de l’appareil IoT Edge hébergeant le module ou sur n’importe quel appareil sur le réseau.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```