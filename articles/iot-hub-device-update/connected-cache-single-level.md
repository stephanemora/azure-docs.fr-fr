---
title: Exemples de scénarios de déploiement de la préversion du cache connecté Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Tutoriels relatifs aux exemples de scénarios de déploiement de la préversion du cache connecté Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811721"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Exemples de scénarios de déploiement de la préversion du cache connecté Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Passerelle Azure IoT Edge à niveau unique sans proxy

Le diagramme ci-dessous décrit un scénario dans lequel une passerelle Azure IoT Edge a un accès direct aux ressources de réseau CDN et un appareil de nœud terminal Azure IoT, tel qu’un Raspberry PI, est un appareil enfant isolé d’Internet de la passerelle Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Mise à jour d’appareil déconnecté du cache connecté Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Ajoutez le module Cache connecté Microsoft à votre déploiement d’appareil de passerelle Azure IoT Edge dans Azure IoT Hub (consultez [Prise en charge des appareils déconnectés](connected-cache-disconnected-device-update.md) pour plus d’informations sur l’obtention du module).
2. Ajoutez les variables d’environnement pour le déploiement. Voici un exemple des variables d’environnement.

    **Variables d’environnement**
    
    | Nom                          | Valeur                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Voir les descriptions de la [variable d’environnement](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Voir les descriptions de la [variable d’environnement](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Voir les descriptions de la [variable d’environnement](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Ajoutez les options de création de conteneur pour le déploiement. Voici un exemple des options de création de conteneur.

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

Pour une validation du bon fonctionnement du cache connecté Microsoft, exécutez la commande suivante dans le terminal de l’appareil IoT Edge hébergeant le module ou sur n’importe quel appareil dans le réseau. Remplacez \<Azure IoT Edge Gateway IP\> par l’adresse IP ou le nom d’hôte de votre passerelle IoT Edge. (consultez les détails de la variable d’environnement pour plus d’informations sur la visibilité de ce rapport).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Passerelle Azure IoT Edge à niveau unique avec proxy non authentifié sortant

Dans ce scénario, il existe une passerelle Azure IoT Edge qui a accès aux ressources de réseau CDN via un proxy non authentifié sortant. Le cache connecté Microsoft est en cours de configuration pour mettre en cache du contenu à partir d’un référentiel personnalisé et le rapport de synthèse a été rendu visible à tous dans le réseau. Vous trouverez ci-dessous un exemple des variables d’environnement MCC qui seraient définies.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Proxy à niveau unique du cache connecté Microsoft" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Ajoutez le module Cache connecté Microsoft à votre déploiement d’appareil de passerelle Azure IoT Edge dans Azure IoT Hub.
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
    | UPSTREAM_PROXY                | IP ou FQDN de votre serveur proxy                                          |

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

Pour une validation du bon fonctionnement du cache connecté Microsoft, exécutez la commande suivante dans le terminal de l’appareil Azure IoT Edge hébergeant le module ou sur n’importe quel appareil dans le réseau. Remplacez \<Azure IoT Edge Gateway IP\> par l’adresse IP ou le nom d’hôte de votre passerelle IoT Edge. (consultez les détails de la variable d’environnement pour plus d’informations sur la visibilité de ce rapport).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
