---
title: Bien démarrer avec Azure Video Analyzer en utilisant le portail Azure - Azure
description: Ce guide de démarrage rapide vous aide à commencer à utiliser Azure Video Analyzer étape par étape en utilisant le portail Azure.
ms.topic: quickstart
ms.date: 05/25/2021
ms.openlocfilehash: 47290a269686cd3727c009742c502bd9c57b579b
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112079532"
---
# <a name="quickstart-get-started-with-azure-video-analyzer"></a>Démarrage rapide : Bien démarrer avec Azure Video Analyzer
Ce guide de démarrage rapide vous guide tout au long des étapes de prise en main d’Azure Video Analyzer. Vous allez créer un compte Azure Video Analyzer et les ressources qui l’accompagnent en utilisant le portail Azure.
Après avoir créé votre compte Video Analyzer, vous allez déployer le module périphérique Video Analyzer et un module simulateur de caméra RTSP sur votre appareil IoT Edge.

Une fois la procédure de configuration terminée, vous pourrez exécuter un flux vidéo en direct simulé par le biais d’un pipeline qui détecte et signale tout mouvement dans ce flux. Le diagramme suivant représente graphiquement ce pipeline.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Détecter le mouvement":::

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure actif. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/).  
[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]
* Un appareil IoT Edge sur lequel vous disposez de privilèges d’administrateur
   * [Déployer sur un appareil IoT Edge](deploy-iot-edge-device.md)
   * [Déployer sur IoT Edge pour Linux sur Windows](deploy-iot-edge-linux-on-windows.md)
* [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :

    * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

> [!TIP] 
> Vous serez peut-être invité à installer Docker pendant l’installation de l’extension Azure IoT Tools. N’hésitez pas à ignorer cette invite.

## <a name="preparing-your-iot-edge-device"></a>Préparation de votre appareil IoT Edge
Le module Azure Video Analyzer doit être configuré pour s’exécuter sur l’appareil IoT Edge avec un compte d’utilisateur local non privilégié. Le module a aussi besoin de certains dossiers locaux pour stocker les données de configuration d’application. Le module simulateur de caméra RTSP a besoin de fichiers vidéo avec lesquels il peut synthétiser un flux vidéo en direct.

https://aka.ms/ava/prepare-device  
  
**Exécuter la commande suivante sur votre appareil IoT Edge**  
`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

Le script prep-device utilisé ci-dessus automatise la tâche de création de dossiers d’entrée et de configuration, de téléchargement de fichiers d’entrée vidéo et de création de comptes d’utilisateur avec les privilèges appropriés. Une fois la commande terminée, les dossiers suivants auront normalement été créés sur votre appareil Edge. 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    Notez les fichiers vidéo (« *.mkv ») dans le dossier /home/localedgeuser/samples/input, qui sont utilisés pour simuler la vidéo en direct. 
## <a name="creating-azure-resources"></a>Création des ressources Azure
Dans l’étape suivante, vous créez les ressources Azure nécessaires (compte Video Analyzer, compte de stockage, identité managée affectée par l’utilisateur), créez un registre de conteneurs facultatif et inscrivez un module périphérique Video Analyzer avec le compte Video Analyzer.

Lorsque vous créez un compte Azure Video Analyzer, vous devez y associer un compte de stockage Azure. Si vous utilisez Video Analyzer pour enregistrer la vidéo en direct à partir d’une caméra, ces données sont stockées en tant que blobs dans un conteneur dans le compte de stockage. Vous devez utiliser une identité managée pour accorder au compte Video Analyzer l’accès approprié au compte de stockage, comme suit.


   [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Créer un compte Video Analyzer dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans la barre de recherche située en haut, entrez **Video Analyzer**.
1. Sous *Services*, cliquez sur *Analyseurs vidéo*.
1. Cliquez sur **Add**.
1. Dans la section **Créer un compte Video Analyzer**, entrez les valeurs requises.
    - **Abonnement** : Choisissez l’abonnement sous lequel vous souhaitez créer le compte Video Analyzer.
    - **Groupe de ressources** : Choisissez un groupe de ressources pour créer le compte Video Analyzer ou cliquez sur **Créer** pour créer un groupe de ressources.
    - **Nom du compte Video Analyzer** : Il s’agit du nom de votre compte Video Analyzer. Le nom doit se composer de lettres en minuscules ou de chiffres, sans espaces, et comprendre entre 3 et 24 caractères.
    - **Localisation** : Choisissez une région pour déployer votre compte Video Analyzer, par exemple **USA Ouest 2**.
    - **Compte de stockage** : Créez un compte de stockage. Il est recommandé de sélectionner un compte de stockage [universel v2 standard](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
    - **Identité de l’utilisateur** : Créez et nommez une nouvelle identité managée affectée par l’utilisateur.

1. Au bas du formulaire, cliquez sur **Examiner et créer**.

### <a name="create-a-container-registry"></a>Créer un registre de conteneur
1. Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.
1. Sous l’onglet **Informations de base**, entrez les valeurs pour **Groupe de ressources** **_(utilisez le même _*groupe de ressources** des sections précédentes) **_ et _*Nom du registre**. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques.
1. Acceptez les valeurs par défaut pour les autres paramètres. Sélectionnez ensuite **Passer en revue + créer** . Après avoir passé en revue les paramètres, sélectionnez **Créer**.

## <a name="deploying-edge-modules"></a>Déploiement de modules périphériques

### <a name="deploying-video-analyzer-edge-module"></a>Déploiement du module périphérique Video Analyzer

1. Accédez à votre compte Video Analyzer.
1. Sélectionnez **Modules périphériques** sous le panneau **Périphérie**.
1. Sélectionnez **Ajouter des modules périphériques**, entrez **_avaedge_ *_ comme nom pour le nouveau module périphérique, puis sélectionnez _* Ajouter**.
1. L’écran **Copier le jeton de provisionnement** s’affiche sur le côté droit.
1. Copiez l’extrait de code sous **Propriétés souhaitées recommandées pour le déploiement de module IoT**, vous en aurez besoin dans une étape ultérieure.
    ```JSON
      {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "XXXXXXX",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": true,
          "telemetryOptOut": false
      }
    ```
1. Accédez à votre hub IoT.
1. Sélectionnez **IoT Edge** sous **Gestion automatique des appareils**.
1. Sélectionnez l’**ID d’appareil** de votre appareil IoT Edge.
1. Sélectionnez **Définir des modules**.
1. Sélectionnez **Ajouter**, puis **Module IoT Edge** dans le menu déroulant.
1. Entrez **avaedge** pour le **Nom du module IoT Edge**.
1. Copiez et collez la ligne suivante dans le champ **URI de l’image** : `mcr.microsoft.com/media/video-analyzer:1`
1. Sélectionnez **Variables d’environnement**. 
1. Sous **NOM**, entrez **LOCAL_USER_ID**, puis sous **VALEUR**, entrez **1010**.
1. Sur la seconde ligne, sous **NOM**, entrez **LOCAL_GROUP_ID**, puis sous **VALEUR**, entrez **1010**.
1. Sélectionnez **Opérations de création de conteneur**, puis copiez et collez les lignes suivantes :  
    ```json
            {
                "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                            "max-size": "10m",
                            "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
                    ],
                    "IpcMode": "host",
                    "ShmSize": 1536870912
                }
            }
    ```
1. Sélectionnez **Paramètres de jumeau de module** et collez l’extrait de code que vous avez copié précédemment à partir de la page **Copier le jeton de provisionnement** dans le compte Video Analyzer.
    ```JSON
      {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "XXXXXXX",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": true,
          "telemetryOptOut": false
      }
    ```
1. Sélectionnez **Ajouter** en bas de votre écran.
1. Sélectionnez **Routes**.
1. Sous **NOM**, entrez **AVAToHub**, puis sous **VALEUR**, entrez FROM /messages/modules/avaedge/outputs/* INTO $upstream
1. Sélectionnez **Vérifier + créer**, puis **Créer**, et votre module périphérique **avaedge** sera déployé.

### <a name="deploying-rtsp-camera-simulator-edge-module"></a>Déploiement du module périphérique de simulateur de caméra RTSP
1. Accédez à votre hub IoT.
1. Sélectionnez **IoT Edge** sous **Gestion automatique des appareils**.
1. Sélectionnez l’**ID d’appareil** de votre appareil IoT Edge.
1. Sélectionnez **Définir des modules**.
1. Sélectionnez **Ajouter**, puis **Module IoT Edge** dans le menu déroulant.
1. Entrez **rtspsim** pour le **Nom du module IoT Edge**.
1. Copiez et collez la ligne suivante dans le champ **URI de l’image** : `mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2`
1. Sélectionnez **Opérations de création de conteneur**, puis copiez et collez les lignes suivantes :  
    ```json
            {
                "HostConfig": {
                    "Binds": [
                         "/home/localedgeuser/samples/input:/live/mediaServer/media"
                    ]
                }
            }
    ```
1. Sélectionnez **Ajouter** en bas de votre écran.
1. Sélectionnez **Vérifier + créer**, puis **Créer**, et votre module périphérique **rtspsim** sera déployé.

### <a name="verify-your-deployment"></a>Vérifier votre déploiement

Dans la page des détails de l’appareil, vérifiez que les modules **avaedge** et **rtspsim** sont listés à la fois comme **Spécifié dans le déploiement** et **Signalé par l’appareil**.  

Le démarrage des modules sur l’appareil et leur signalement au hub IoT peuvent prendre quelques instants. Actualisez la page pour afficher un état mis à jour.
Code d’état : 200 –OK signifie que [le runtime d’IoT Edge](../../iot-edge/iot-edge-runtime.md) est sain et fonctionne correctement.  

![La capture d’écran affiche une valeur d’état pour un runtime IoT Edge.](./media/deploy-iot-edge-device/status.png)


## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

### <a name="obtain-your-iot-hub-connection-string"></a>Obtenir votre chaîne de connexion IoT Hub

1. Dans le portail Azure, accédez à votre compte IoT Hub.
1. Recherchez l’option **Stratégies d’accès partagé** dans le volet de navigation de gauche, puis cliquez dessus.
1. Cliquez sur la stratégie nommée **iothubowner**
1. Copiez la **chaîne de connexion principale**. Elle ressemble à `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`

### <a name="connect-to-the-iot-hub"></a>Se connecter au hub IoT

1. Ouvrez Visual Studio Code, sélectionnez **Afficher** > **Explorateur**. Vous pouvez aussi sélectionner Ctrl+Maj+E.
1. Dans l’angle en bas à gauche de l’onglet **Explorateur**, sélectionnez **Azure IoT Hub**.
1. Sélectionnez l’icône **Plus d’options** pour voir le menu contextuel. Sélectionnez ensuite **Définir la chaîne de connexion IoT Hub**.
1. Quand une zone d’entrée s’affiche, entrez votre chaîne de connexion IoT Hub.
1. Après environ 30 secondes, actualisez Azure IoT Hub dans la section inférieure gauche. Vous devriez voir votre **ID d’appareil**, dont les modules suivants sont normalement déployés :
    * Module de périphérie Video Analyzer (nom du module : **avaedge**)
    * Simulateur RTSP (nom du module **rtspsim**)


> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="Développez le nœud Modules":::

> [!TIP]
> Si vous [avez déployé manuellement Video Analyzer](deploy-iot-edge-device.md) sur un appareil de périphérie (par exemple un appareil ARM64), vous voyez le module s’afficher sous cet appareil, sous le hub Azure IoT Hub. Vous pouvez sélectionner ce module et suivre le reste des étapes ci-dessous.

### <a name="prepare-to-monitor-the-modules"></a>Préparer la surveillance des modules 

Si vous suivez ce guide de démarrage rapide, les événements sont envoyés au hub IoT. Pour voir ces événements, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X), puis recherchez **Azure IoT Hub**.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="Sélectionnez Paramètres d’extension":::
1. Recherchez et activez Afficher le message détaillé.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="Afficher le message détaillé":::
1. Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez **Azure IoT Hub** dans l’angle inférieur gauche.
1. Développez le nœud **Appareils**.
1. Cliquez avec le bouton droit sur votre **ID d’appareil** et sélectionnez **Démarrer la surveillance du point de terminaison d’événements intégré**.

    > [!NOTE]
    > Vous pouvez être invité à fournir des informations sur le point de terminaison intégré pour le hub IoT. Pour obtenir ces informations, dans le portail Azure, accédez à votre hub IoT et recherchez l’option **Points de terminaison intégrés** dans le volet de navigation gauche. Cliquez et recherchez l’option **Point de terminaison compatible Event Hub** sous la section **Point de terminaison compatible Event Hub**. Copiez et utilisez le texte dans la zone. Le point de terminaison doit ressembler à ceci :  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="use-direct-method-calls"></a>Utiliser des appels de méthode directe

Vous pouvez maintenant analyser les flux vidéo en direct en appelant des méthodes directes exposées par le module de périphérie Video Analyzer. Lisez [Méthodes directes de Video Analyzer](direct-methods.md) pour examiner toutes les méthodes directes fournies par le module. 

### <a name="enumerate-pipeline-topologies"></a>Énumérer les topologies de pipeline

Cette étape énumère toutes les [topologies de pipeline](pipeline.md) du module.

1. Cliquez avec le bouton droit sur le module « avaedge », puis sélectionnez **Invoquer la méthode directe du module** dans le menu contextuel.
1. Vous verrez une zone de modification apparaître dans la partie centrale supérieure de la fenêtre Visual Studio Code. Entrez « pipelineTopologyList » dans la zone d’édition, puis appuyez sur Entrée.
1. Ensuite, copiez et collez la charge utile JSON ci-dessous dans la zone de modification, puis appuyez sur Entrée.
   
```json
{
    "@apiVersion" : "1.0"
}
```

En quelques secondes, vous verrez la réponse suivante dans la fenêtre SORTIE :
    
```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [deviceId/avaedge] ...
[DirectMethod] Response from [deviceId/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

La réponse ci-dessus est attendue, car aucune topologie de pipeline n’a été créée.

### <a name="set-a-pipeline-topology"></a>Définir une topologie de pipeline

En suivant les mêmes étapes que celles ci-dessus, vous pouvez invoquer `pipelineTopologySet` pour configurer une topologie de pipeline à l’aide du code JSON suivant comme charge utile. Vous allez créer une topologie de pipeline nommée « MotionDetection ».


```json
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUrl",
                "type": "string",
                "description&quot;: &quot;rtspUrl"
            },
            {
                "name": "rtspUserName",
                "type": "string",
                "description": "rtspUserName",
                "default&quot;: &quot;dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "string",
                "description": "rtspPassword",
                "default&quot;: &quot;dummypw"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password&quot;: &quot;${rtspPassword}"
                    },
                    "url&quot;: &quot;${rtspUrl}"
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
                "sensitivity": "medium",
                "name": "motionDetection",
                "inputs": [
                    {
                        "nodeName": "rtspSource",
                        "outputSelectors": []
                    }
                ]
            }
        ],
        "sinks": [
            {
                "hubOutputName": "inferenceOutput",
                "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
                "name": "iotHubSink",
                "inputs": [
                    {
                        "nodeName&quot;: &quot;motionDetection"
                    }
                ]
            }
        ]
    }
}
```

Cette charge utile JSON crée une topologie qui définit trois paramètres, dont deux ont des valeurs par défaut. La topologie comprend un nœud source ([source RTSP](pipeline.md#rtsp-source)), un nœud processeur ([processeur de détection de mouvement](pipeline.md#motion-detection-processor)) et un nœud récepteur ([récepteur de messages IoT Hub](pipeline.md#iot-hub-message-sink)). La représentation visuelle de la topologie est indiquée ci-dessus.

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE**.

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

L’état retourné est 201. Cet état indique qu’une topologie a été créée.

Essayez les étapes suivantes :

1. Invoquez à nouveau `pipelineTopologySet`. Le code d’état retourné est 200. Ce code indique qu’une topologie existante a été correctement mise à jour.
1. Invoquez de nouveau `pipelineTopologySet`, mais changez la chaîne de description. Le code d’état retourné est 200 et la description est mise à jour avec la nouvelle valeur.
1. Invoquez `pipelineTopologyList` comme indiqué dans la section précédente. Vous pouvez maintenant voir la topologie « MotionDetection » dans la charge utile retournée.

### <a name="read-the-pipeline-topology"></a>Lire la topologie de pipeline

Invoquez `pipelineTopologyGet` à l’aide de la charge utile suivante.

```json
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Dans la charge utile de réponse, notez les informations suivantes :

* Le code d’état est 200, ce qui indique la réussite.
* La charge utile comprend l’horodatage `createdAt` et l’horodatage `lastModifiedAt`.

### <a name="create-a-live-pipeline-using-the-topology"></a>Créer un pipeline en direct à l’aide de la topologie

Créez ensuite un pipeline en direct qui référence la topologie de pipeline ci-dessus. Invoquez la méthode directe `livePipelineSet` avec la charge utile suivante :

```json
{
    "@apiVersion" : "1.0",
    "name": "mdpipeline1",
    "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample pipeline description",
        "parameters": [
            {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
            },
            {
                "name": "rtspUserName",
                "value": "testuser"
            },
            {
                "name": "rtspPassword",
                "value": "testpassword"
            }
        ]
    }
}
```

Notez que cette charge utile :

* La charge utile ci-dessus spécifie la topologie (« MotionDetection ») qui doit être utilisée par le pipeline en direct.
* La charge utile contient une valeur de paramètre pour `rtspUrl`, qui n’avait pas de valeur par défaut dans la charge utile de la topologie. Cette valeur est un lien vers l’exemple de vidéo ci-dessous :

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]


Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

Dans la charge utile de réponse, notez les points suivants :

* Le code d’état est 201, ce qui indique qu’un nouveau pipeline en direct a été créé.
* L’état est « Inactif », ce qui indique que le pipeline en direct a été créé mais qu’il n’est pas activé. Pour plus d’informations, consultez [États du pipeline](pipeline.md#pipeline-states).

Essayez les méthodes directes suivantes en exécutant la procédure suivante :

* Appelez à nouveau `livePipelineSet` avec la même charge utile. Vous noterez que le code d’état retourné est à présent 200.
* Invoquez de nouveau `livePipelineSet`, mais avec une description différente et notez la description mise à jour dans la charge utile de réponse, indiquant que le pipeline en direct a été mis à jour.
* Appelez `livePipelineSet`, mais remplacez le nom par « mdpipeline2 », et `rtspUrl` par « rtsp://rtspsim:554/media/lots_015.mkv ». Dans la charge utile de réponse, notez le pipeline en direct nouvellement créé (autrement dit, le code d’état 201).
    > [!NOTE]
    > Comme expliqué dans [Topologies de pipeline](pipeline.md#pipeline-topologies), vous pouvez créer plusieurs pipelines en direct pour analyser les flux vidéo en direct à partir de plusieurs caméras, à l’aide de la même topologie de pipeline. Si vous créez d’autres pipelines en direct, veillez à les supprimer au cours de l’étape de nettoyage.

### <a name="activate-the-live-pipeline"></a>Activer le pipeline en direct

Vous pouvez ensuite activer le pipeline en direct, ce qui entraîne le démarrage du flux de la vidéo en direct (simulée) à travers le pipeline. Invoquez la méthode directe `livePipelineActivate` avec la charge utile suivante :

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre SORTIE.

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été activé.

### <a name="check-the-state-of-the-live-pipeline"></a>Vérifier l’état du pipeline en direct

À présent, invoquez la méthode directe `livePipelineGet` avec la charge utile suivante :

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre SORTIE.

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

Dans la charge utile de réponse, notez les informations suivantes :

* Le code d’état est 200, ce qui indique la réussite.
* L’état est « Actif », ce qui indique que le pipeline en direct est désormais actif.

## <a name="observe-results"></a>Observer les résultats

Le pipeline en direct que vous avez créé et activé ci-dessus utilise le nœud processeur de détection de mouvement pour détecter les mouvements dans le stream vidéo en direct entrant, puis envoie les événements au récepteur IoT Hub. Ces événements sont ensuite relayés vers votre instance IoT Hub sous la forme de messages, ce que vous pouvez désormais observer. Vous verrez des messages dans la fenêtre de sortie avec ce qui suit dans la section « body » :


```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

Notez les détails suivants :

*   La section « inferences » indique que le type est « motion ». Elle fournit des données supplémentaires sur l’événement « motion » et fournit un cadre englobant pour la région de la trame vidéo (à l’horodatage indiqué) où le mouvement a été détecté.

    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Invoquer des appels de méthodes directes supplémentaires pour nettoyer

Vous pouvez appeler ensuite des méthodes directes pour désactiver et supprimer le pipeline en direct (dans cet ordre).

### <a name="deactivate-the-live-pipeline"></a>Désactiver le pipeline en direct

Invoquez la méthode directe `livePipelineDeactivate` avec la charge utile suivante :

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été désactivé. 

Ensuite, essayez d’invoquer `livePipelineGet` comme indiqué précédemment dans cet article. Notez la valeur de l’état.

### <a name="delete-the-live-pipeline"></a>Supprimer le pipeline en direct

Invoquez la méthode directe `livePipelineDelete` avec la charge utile suivante :

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE** :

```json
{
  "status": 200,
  "payload": null
}
```
Le code d’état 200 indique que le pipeline en direct a été supprimé.

Si vous avez également créé le pipeline nommé « mdpipeline2 », vous ne pouvez pas supprimer la topologie de pipeline sans supprimer également ce pipeline supplémentaire. Invoquez à nouveau la méthode directe `livePipelineDelete` à l’aide de la charge utile suivante :

```
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline2"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre de sortie :

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que le pipeline en direct a été supprimé.

Vous pouvez invoquer `livePipelineList` à l’aide de la même charge utile que `pipelineTopologyList`. Notez qu’aucun pipeline en direct n’est énuméré.

### <a name="delete-the-pipeline-topology"></a>Supprimer la topologie de pipeline

Une fois que tous les pipelines en direct ont été supprimés, vous pouvez appeler la méthode directe `pipelineTopologyDelete` avec la charge utile suivante :

```json
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Au bout de quelques secondes, la réponse suivante s’affiche dans la fenêtre **SORTIE**.

```json
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que la topologie a été supprimée.

Vous pouvez essayer d’invoquer `pipelineTopologyList` pour voir que le module ne contient pas de topologies.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]
    
## <a name="next-steps"></a>Étapes suivantes

* Essayez le [guide de démarrage rapide pour enregistrer des vidéos dans le cloud quand des mouvements sont détectés](detect-motion-record-video-clips-cloud.md).
* Essayez le [guide de démarrage rapide pour analyser les vidéos en direct](analyze-live-video-use-your-model-http.md).
* En savoir plus sur les [messages de diagnostic](monitor-log-edge.md)
