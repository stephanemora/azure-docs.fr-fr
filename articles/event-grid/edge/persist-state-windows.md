---
title: Conserver l’état sur Windows – Azure Event Grid IoT Edge | Microsoft Docs
description: Conserver l’état sur Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991762"
---
# <a name="persist-state-in-windows"></a>Conserver l’état sur Windows

Les rubriques et les abonnements créés dans le module Event Grid sont stockés par défaut dans le système de fichiers conteneur. Sans persistance, toutes les métadonnées créées seraient perdues en cas de redéploiement du module. Pour conserver les données d’un déploiement à l’autre, il est nécessaire de les rendre persistantes en dehors du système de fichiers conteneur. Actuellement, seules les métadonnées sont persistantes. Les événements sont stockés en mémoire. En cas de redéploiement ou de redémarrage du module Event Grid, tous les événements non remis sont perdus.

Cet article donne les étapes à suivre pour déployer le module Event Grid avec persistance dans les déploiements Windows.

> [!NOTE]
>Le module Event Grid s’exécute en tant qu’utilisateur à faibles privilèges **ContainerUser** sur Windows.

## <a name="persistence-via-volume-mount"></a>Persistance par montage de volume

Les [volumes Docker](https://docs.docker.com/storage/volumes/) permettent de conserver les données d’un déploiement à l’autre. Pour monter un volume, il faut le créer avec des commandes Docker, accorder les autorisations nécessaires pour que le conteneur puisse le lire et y écrire des données, puis déployer le module. Aucune configuration ne permet de créer automatiquement un volume doté des autorisations requises sur Windows. Il doit être créé avant le déploiement.

1. Créez un volume en exécutant la commande suivante :

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Par exemple,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Récupérez le répertoire hôte auquel le volume est mappé en exécutant la commande ci-dessous :

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Par exemple,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Exemple de sortie :

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Ajoutez le groupe **Utilisateurs** à la valeur pointée par **Mountpoint** :
    1. Lancez l’Explorateur de fichiers.
    1. Accédez au dossier pointé par **Mountpoint**.
    1. Cliquez avec le bouton droit, puis sélectionnez **Propriétés**.
    1. Sélectionnez **Sécurité**.
    1. Sous *Noms de groupes ou d’utilisateurs, sélectionnez **Modifier**.
    1. Sélectionnez **Ajouter**, entrez `Users`, sélectionnez **Vérifier les noms**, puis sélectionnez **OK**.
    1. Sous *Autorisations pour les utilisateurs*, sélectionnez **Modifier**, puis **OK**.
1. Utilisez **Binds** pour monter ce volume et redéployer le module Event Grid à partir du Portail Azure.

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Ne modifiez pas la deuxième partie de la valeur Binds. Elle pointe vers un emplacement spécifique dans le module. Dans le cas du module Event Grid sur Windows, il doit s’agir de **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistance par montage du répertoire hôte

Vous pouvez également choisir de créer et de monter un répertoire sur le système hôte.

1. Créez un répertoire dans le système de fichiers hôte en exécutant la commande suivante.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Par exemple,

   ```sh
   mkdir C:\myhostdir
   ```
1. Utilisez **Binds** pour monter votre répertoire et redéployer le module Event Grid à partir du Portail Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Ne modifiez pas la deuxième partie de la valeur Binds. Elle pointe vers un emplacement spécifique dans le module. Dans le cas du module Event Grid sur Windows, il doit s’agir de **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
