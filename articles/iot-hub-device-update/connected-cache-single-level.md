---
title: Exemples de scénarios de déploiement de la préversion du cache connecté Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Tutoriels relatifs aux exemples de scénarios de déploiement de la préversion du cache connecté Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658672"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Exemples de scénarios de déploiement de la préversion du cache connecté Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Passerelle Azure IoT Edge à niveau unique sans proxy

Le diagramme ci-dessous décrit un scénario dans lequel une passerelle Azure IoT Edge a un accès direct aux ressources de réseau CDN et un appareil de nœud terminal Azure IoT, tel qu’un Raspberry PI, est un appareil enfant isolé d’Internet de la passerelle Azure IoT Edge. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Mise à jour d’appareil déconnecté du cache connecté Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Ajoutez le module Cache connecté Microsoft à votre déploiement d’appareil de passerelle Azure IoT Edge dans Azure IoT Hub (consultez `MCC concepts` pour plus d’informations sur la façon d’obtenir le module).
2. Ajoutez les variables d’environnement pour le déploiement. Voici un exemple des variables d’environnement.

    **Variables d’environnement**
    
    | Nom                 | Value                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Consultez la description de la variable d’environnement ci-dessus. |
    | CUSTOMER_ID                   | Consultez la description de la variable d’environnement ci-dessus. |
    | CUSTOMER_KEY                  | Consultez la description de la variable d’environnement ci-dessus. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Ajoutez les options de création de conteneur pour le déploiement. Voici un exemple des options de création de conteneur.

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

Pour une validation du bon fonctionnement du cache connecté Microsoft, exécutez la commande suivante dans le terminal de l’appareil IoT Edge hébergeant le module ou sur n’importe quel appareil dans le réseau.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Passerelle Azure IoT Edge à niveau unique avec proxy non authentifié sortant

Dans ce scénario, il existe une passerelle Azure IoT Edge qui a accès aux ressources de réseau CDN via un proxy non authentifié sortant. Le cache connecté Microsoft est en cours de configuration pour mettre en cache du contenu à partir d’un référentiel personnalisé et le rapport de synthèse a été rendu visible à tous dans le réseau. Vous trouverez ci-dessous un exemple des variables d’environnement MCC qui seraient définies.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Proxy à niveau unique du cache connecté Microsoft" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

Pour une validation du bon fonctionnement du cache connecté Microsoft, exécutez la commande suivante dans le terminal de l’appareil Azure IoT Edge hébergeant le module ou sur n’importe quel appareil dans le réseau.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
