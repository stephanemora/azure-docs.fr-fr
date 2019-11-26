---
title: Conserver l’état dans Linux – Azure Event Grid IoT Edge | Microsoft Docs
description: Conserver les métadonnées dans Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100336"
---
# <a name="persist-state-in-linux"></a>Conserver l’état dans Linux

Les rubriques et les abonnements créés dans le module Event Grid sont stockés par défaut dans le système de fichiers conteneur. Sans persistance, toutes les métadonnées créées seraient perdues en cas de redéploiement du module. Actuellement, seules les métadonnées sont persistantes. Les événements sont stockés en mémoire. En cas de redéploiement ou de redémarrage du module Event Grid, tous les événements non remis sont perdus.

Cet article donne les étapes à suivre pour déployer le module Event Grid avec persistance dans les déploiements Linux.

> [!NOTE]
>Le module Event Grid s’exécute en tant qu’utilisateur à faibles privilèges avec l’UID `2000` et le nom `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Persistance par montage de volume

 Les [volumes Docker](https://docs.docker.com/storage/volumes/) permettent de conserver les données d’un déploiement à l’autre. Vous pouvez laisser Docker créer automatiquement un volume nommé dans le cadre du déploiement du module Event Grid. Cette option est la plus simple. Vous pouvez spécifier le nom du volume à créer dans la section **Binds** comme suit :

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Ne modifiez pas la deuxième partie de la valeur de liaison. Elle pointe vers un emplacement spécifique au sein du module. Dans le cas du module Event Grid sur Linux, il doit s’agir de **/app/metadataDb**.

Par exemple, la configuration suivante entraîne la création du volume **egmetadataDbVol** où les métadonnées sont conservées.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Vous pouvez également créer un volume Docker à l’aide des commandes du client de Docker. 

## <a name="persistence-via-host-directory-mount"></a>Persistance par montage du répertoire hôte

Au lieu d’un volume Docker, vous avez également la possibilité de monter un dossier hôte.

1. Commencez par créer un utilisateur portant le nom **eventgriduser** et ID **2000** sur l’ordinateur hôte en exécutant la commande suivante :

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Créez un répertoire dans le système de fichiers hôte en exécutant la commande suivante.

   ```sh
   md <your-directory-name-here>
   ```

    Par exemple, l’exécution de la commande suivante crée un répertoire appelé **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Ensuite, faites de **eventgriduser** le propriétaire de ce dossier en exécutant la commande suivante.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Par exemple,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Utilisez **Binds** pour monter le répertoire et redéployer le module Event Grid à partir du Portail Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Par exemple,

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Ne modifiez pas la deuxième partie de la valeur de liaison. Elle pointe vers un emplacement spécifique au sein du module. Dans le cas du module Event Grid sur Linux, il doit s’agir de **/app/metadata**.
