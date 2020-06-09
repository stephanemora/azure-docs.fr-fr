---
title: Bien démarrer avec Live Video Analytics sur IoT Edge - Azure
description: Ce guide de démarrage rapide vous montre comment bien démarrer avec Live Video Analytics sur IoT Edge et comment détecter tout mouvement dans un flux vidéo en direct.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261564"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Démarrage rapide : Bien démarrer - Live Video Analytics sur IoT Edge

Ce guide de démarrage rapide vous guide tout au long des étapes de prise en main de Live Video Analytics sur IoT Edge. Il utilise une machine virtuelle Azure comme appareil IoT Edge et un flux vidéo en direct simulé. Une fois la procédure de configuration terminée, vous pourrez exécuter un flux vidéo en direct simulé à travers un graphe multimédia qui détecte et signale tout mouvement dans ce flux. Le diagramme ci-dessous montre une représentation graphique de ce graphe multimédia.

![Live Video Analytics basé sur la détection de mouvement](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement avec [l’extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Le réseau auquel votre machine de développement est connectée doit autoriser le protocole AMQP sur le port 5671 (afin que Azure IoT Tools puisse communiquer avec Azure IoT Hub).

> [!TIP]
> Vous pouvez être invité à installer Docker au moment de l’installation de l’extension Azure IoT Tools. N’hésitez pas à l’ignorer.

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

Les ressources Azure suivantes sont indispensables dans ce tutoriel.

* IoT Hub
* Compte de stockage
* Compte Azure Media Services
* Machine virtuelle Linux dans Azure, avec le [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installé

Pour ce guide de démarrage rapide, nous vous recommandons d’utiliser le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pour déployer les ressources Azure mentionnées ci-dessus dans votre abonnement Azure. Pour ce faire, procédez comme suit :

1. Accédez à https://shell.azure.com.
1. Si vous utilisez Cloud Shell pour la première fois, vous êtes invité à sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez « Créer un stockage » pour créer un compte de stockage pour le stockage de vos informations de session Cloud Shell. Ce compte de stockage est distinct de celui que le script créera pour l’utilisation avec votre compte Azure Media Services.
1. Sélectionnez « Bash » comme environnement dans la liste déroulante sur le côté gauche de la fenêtre Cloud Shell.

    ![Sélecteur d’environnement](./media/quickstarts/env-selector.png)

1. Exécutez la commande suivante :

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Si le script se termine correctement, vous devez voir toutes les ressources mentionnées ci-dessus dans votre abonnement. Le script génère une table de ressources qui répertorie le nom du hub IoT. Recherchez le type de ressource **« Microsoft.Devices/IotHubs »** , puis notez le nom. Vous en aurez besoin à l’étape suivante. Le script génère également quelques fichiers de configuration dans le répertoire ~/clouddrive/lva-sample/. vous en aurez besoin plus tard dans le guide de démarrage rapide.

## <a name="deploy-modules-on-your-edge-device"></a>Déployer des modules sur votre périphérique

Exécutez la commande suivante à partir d’Azure Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

La commande ci-dessus va déployer les modules suivants sur le périphérique (la machine virtuelle Linux) :

* Live Video Analytics sur IoT Edge (nom du module « lvaEdge »)
* Simulateur RTSP (nom du module « rtspsim »)

Le module de simulateur RTSP simule un flux vidéo en direct à l’aide d’un fichier vidéo stocké qui a été copié sur votre périphérique lorsque vous avez exécuté le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). À ce stade, vous disposez des modules déployés, mais aucun graphe multimédia n’est actif.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Configurer l’extension Azure IoT Tools dans Visual Studio Code

Démarrez Visual Studio Code et suivez les instructions ci-dessous pour vous connecter à votre instance Azure IoT Hub à l’aide de l’extension Azure IoT Tools.

1. Accédez à l’onglet Explorateur dans Visual Studio Code via **Affichage** > **Explorateur** ou appuyez simplement sur Ctrl+Maj+E.
1. Dans l’onglet Explorateur, cliquez sur « Azure IoT Hub » dans le coin inférieur gauche.
1. Cliquez sur l’icône Plus d’options pour afficher le menu contextuel et sélectionnez l’option « Définir la chaîne de connexion IoT Hub ».
1. Une zone d’entrée s’affiche où vous pouvez entrer votre chaîne de connexion IoT Hub. Vous pouvez obtenir la chaîne de connexion de votre IoT Hub à partir du fichier ~/clouddrive/lva-sample/appsettings.json dans Cloud Shell.
1. Si la connexion est établie, la liste des périphériques s’affiche. Elle doit contenir au moins un périphérique nommé « lva-sample-device ».
1. Vous pouvez désormais gérer vos périphériques IoT Edge et interagir avec Azure IoT Hub par le biais du menu contextuel.
1. Vous pouvez afficher les modules déployés sur le périphérique Edge en développant le nœud Modules sous « lva-sample-device ».

    ![nœud lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Utiliser des méthodes directes

Vous pouvez utiliser le module pour analyser des flux vidéo en direct en invoquant des méthodes directes. Lisez [Méthodes directes pour Live Video Analytics sur IoT Edge](direct-methods.md) pour comprendre toutes les méthodes directes fournies par le module. 

### <a name="invoke-graphtopologylist"></a>Invoquer GraphTopologyList
Cette étape énumère toutes les [topologies de graphes](media-graph-concept.md#media-graph-topologies-and-instances) du module.

1. Cliquez avec le bouton droit sur le module « lvaEdge » et sélectionnez « Invoquer la méthode directe du module » dans le menu contextuel.
1. Vous verrez une zone de modification apparaître dans la partie centrale supérieure de la fenêtre Visual Studio Code. Entrez « GraphTopologyList » dans la zone de modification et appuyez sur Entrée.
1. Ensuite, copiez et collez la charge utile JSON ci-dessous dans la zone de modification, puis appuyez sur Entrée.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    En quelques secondes, vous verrez la fenêtre SORTIE de Visual Studio Code apparaître avec la réponse suivante

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    La réponse ci-dessus est attendue, car aucune topologie de graphe n’a été créée.
    

### <a name="invoke-graphtopologyset"></a>Invoquer GraphTopologySet

À l’aide des mêmes étapes que celles décrites pour invoquer GraphTopologyList, vous pouvez invoquer GraphTopologySet pour définir une [topologie de graphe](media-graph-concept.md#media-graph-topologies-and-instances) à l’aide du code JSON suivant comme charge utile.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


La charge utile JSON ci-dessus entraîne la création d’une topologie de graphe qui définit trois paramètres (deux ayant des valeurs par défaut). La topologie possède un nœud source (source RTSP), un nœud processeur (processeur de détection de mouvement) et un nœud récepteur (récepteur IoT Hub).

En quelques secondes, vous verrez la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
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
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
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

L’état retourné est 201, ce qui indique qu’une nouvelle topologie a été créée. Essayez ensuite la procédure suivante :

* Appelez de nouveau GraphTopologySet et vérifiez que le code d’état retourné est 200. Le code d’état 200 indique qu’une topologie existante a été mise à jour.
* Invoquez de nouveau GraphTopologySet, mais modifiez la chaîne de description. Notez que le code d’état dans la réponse est 200 et que la description est mise à jour avec la nouvelle valeur.
* Invoquez GraphTopologyList comme indiqué dans la section précédente et notez que vous pouvez maintenant voir la topologie « MotionDetection » dans la charge utile retournée.

### <a name="invoke-graphtopologyget"></a>Invoquer GraphTopologyGet

Invoquez maintenant GraphTopologyGet avec la charge utile suivante

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
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
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
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

Notez les points suivants dans la charge utile de réponse :

* Le code d’état est 200, ce qui indique une réussite.
* La charge utile présente les horodatages « created » et « lastModified ».

### <a name="invoke-graphinstanceset"></a>Invoquer GraphInstanceSet

Ensuite, créez une instance de graphe qui fait référence à la topologie de graphe ci-dessus. Comme expliqué [ici](media-graph-concept.md#media-graph-topologies-and-instances), les instances de graphe vous permettent d’analyser des flux vidéo en direct à partir de plusieurs caméras avec la même topologie de graphe.

Invoquez la méthode directe GraphInstanceSet avec la charge utile suivante.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Notez les points suivants :

* La charge utile ci-dessus spécifie le nom de la topologie (MotionDetection) pour laquelle l’instance doit être créée.
* La charge utile contient la valeur de paramètre pour « rtspUrl », qui n’avait pas de valeur par défaut dans la charge utile de la topologie de graphe.

En quelques secondes, vous verrez la réponse suivante dans la fenêtre OUTPUT :

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Notez les points suivants dans la charge utile de réponse :

* Le code d’état est 201, ce qui indique qu’une nouvelle instance a été créée.
* L’état est « Inactive », ce qui indique que l’instance de graphe a été créée, mais pas activée. Pour plus d’informations, voir les [états de graphe multimédia](media-graph-concept.md).

Essayez ensuite la procédure suivante :

* Invoquez de nouveau GraphInstanceSet avec la même charge utile et notez que le code d’état retourné est maintenant 200.
* Invoquez de nouveau GraphInstanceSet, mais avec une description différente et notez la description mise à jour dans la charge utile de réponse, indiquant que l’instance de graphe a été mise à jour.
* Invoquez GraphInstanceSet, mais remplacez le nom par « Sample-Graph-2 » et observez la charge utile de réponse. Notez qu’une nouvelle instance de graphe est créée (c’est-à-dire que le code d’état est 201).

### <a name="invoke-graphinstanceactivate"></a>Invoquer GraphInstanceActivate

Activez maintenant l’instance de graphe, qui démarre le flux de vidéos en direct dans le module. Invoquez la méthode directe GraphInstanceActivate avec la charge utile suivante.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 dans la charge utile de réponse indique que l’instance de graphe a été activée.

### <a name="invoke-graphinstanceget"></a>Invoquer GraphInstanceGet

Invoquez maintenant la méthode directe GraphInstanceGet avec la charge utile suivante :

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Notez les points suivants dans la charge utile de réponse :

* Le code d’état est 200, ce qui indique une réussite.
* L’état est « Active », ce qui indique que l’instance de graphe est maintenant dans l’état « actif ».

## <a name="observe-results"></a>Observer les résultats

L’instance de graphe que nous avez créée et activée ci-dessus utilise le nœud processeur de détection de mouvement pour détecter les mouvements dans le flux vidéo en direct entrant et envoie des événements au nœud récepteur IoT Hub. Ces événements sont ensuite relayés vers votre hub IoT Edge, qui peut maintenant être observé. Pour cela, procédez comme suit :

1. Ouvrez le volet Explorateur dans Visual Studio Code et recherchez Azure IoT Hub dans l’angle inférieur gauche.
2. Développez le nœud Appareils.
3. Cliquez avec le bouton droit sur lva-sample-device et choisissez l’option « Démarrer la supervision de l’événement intégré ».

![Démarrer la surveillance des événements IoT Hub](./media/quickstarts/start-monitoring-iothub-events.png)

Les messages suivants s’affichent dans la fenêtre SORTIE :

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Notez les points suivants dans le message ci-dessus

* Le message contient une section « body » et une section « applicationProperties ». Pour comprendre ce que ces sections représentent, lisez l’article [Créer et lire des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* « subject » dans applicationProperties fait référence au nœud dans le graphe multimédia à partir duquel le message a été généré. Dans ce cas, le message provient du processeur de détection de mouvement.
* « eventType » dans applicationProperties indique qu’il s’agit d’un événement Analytics.
* « eventTime » indique l’heure à laquelle l’événement s’est produit.
* « body » contient des données relatives à l’événement Analytics. En l’occurrence, l’événement est un événement d’inférence et, par conséquent, le corps contient des données « timestamp » et « inferences ».
* La section « inferences » indique que le « type » est « motion » et contient des données supplémentaires sur l’événement « motion ».

Si vous laissez le graphe multimédia s’exécuter pendant un moment, le message suivant s’affiche également dans la fenêtre de sortie :

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Notez les points suivants dans le message ci-dessus

* « Subject » dans applicationProperties indique que le message a été généré à partir du nœud source RTSP dans le graphe multimédia.
* « EventType » dans applicationProperties indique qu’il s’agit d’un événement de diagnostic.
* « Body » contient des données relatives à l’événement de diagnostic. Dans ce cas, l’événement est MediaSessionEstablished et, par conséquent, le corps.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Invoquer des méthodes directes supplémentaires pour nettoyer

Invoquez maintenant des méthodes directes pour désactiver et supprimer l’instance de graphe (dans cet ordre).

### <a name="invoke-graphinstancedeactivate"></a>Invoquer GraphInstanceDeactivate

Invoquez la méthode directe GraphInstanceActivate avec la charge utile suivante.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que l’instance de graphe a été désactivée.

Essayez ensuite la procédure suivante.

* Invoquez GraphInstanceGet comme indiqué dans les sections précédentes et observez la valeur « state ».

### <a name="invoke-graphinstancedelete"></a>Invoquer GraphInstanceDelete

Invoquez la méthode directe GraphInstanceDelete avec la charge utile suivante

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 dans la réponse indique que l’instance de graphe a été supprimée.

### <a name="invoke-graphtopologydelete"></a>Invoquer GraphTopologyDelete

Invoquez la méthode directe GraphTopologyDelete avec la charge utile suivante :

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

En quelques secondes, vous devriez voir la réponse suivante dans la fenêtre SORTIE :

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Le code d’état 200 indique que la topologie de graphe a été supprimée.

Essayez ensuite la procédure suivante :

* Invoquez GraphTopologyList et observez l’absence de topologie de graphe dans le module.
* Invoquez GraphInstanceList avec la même charge utile que GraphTopologyList et observez qu’aucune instance de graphe n’est énumérée.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous cessez d’utiliser cette application, supprimez les ressources que vous avez créées dans ce guide de démarrage rapide.

## <a name="next-steps"></a>Étapes suivantes

* Apprendre à enregistrer des vidéos avec Live Video Analytics sur IoT Edge
* En savoir plus sur les messages de diagnostic.
