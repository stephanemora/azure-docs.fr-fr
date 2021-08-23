---
title: Analyser des flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale - Azure
description: Ce tutoriel explique comment utiliser Azure Video Analyzer avec la fonctionnalité IA d’analyse spatiale Vision par ordinateur d’Azure Cognitive Services dans le but d’analyser un flux vidéo en direct provenant d’une caméra IP (simulée).
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 06/01/2021
ms.openlocfilehash: 0f0ee0a7288a3ef07f0aa8fa3c04660cac1ad0b5
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604169"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Tutoriel : Analyser des flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale (préversion)

Ce tutoriel explique comment utiliser Azure Video Analyzer avec le [service IA d’analyse spatiale Vision par ordinateur d’Azure Cognitive Services](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) pour analyser un flux vidéo en direct provenant d’une caméra IP (simulée). Vous y verrez comment ce serveur d’inférence vous permet d’analyser du contenu vidéo diffusé en continu pour comprendre les relations spatiales entre les personnes et leur déplacement dans l’espace physique. Un sous-ensemble d’images du flux vidéo est envoyé à ce serveur d’inférence, après quoi les résultats sont envoyés à IoT Edge Hub. Lorsque certaines conditions sont remplies, les clips vidéo sont enregistrés et stockés sous forme de vidéos dans le compte Video Analyzer.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
>
> - Configurer des ressources.
> - Examinez le code.
> - Exécuter l’exemple de code.
> - Analyser des événements.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

Lisez ces articles avant de commencer :

- [Vue d’ensemble de Video Analyzer](overview.md)
- [Terminologie de Video Analyzer](terminology.md)
- [Concepts relatifs aux pipelines](pipeline.md)
- [Enregistrement de vidéo basé sur les événements](record-event-based-live-video.md)
- [Tutoriel : Développement d’un module IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)

## <a name="prerequisites"></a>Prérequis

Vous trouverez ci-dessous les prérequis nécessaires pour connecter le module d’analyse spatiale au module Azure Video Analyzer.

- [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement. Vérifiez que vous disposez de l’[extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
  - Vérifiez que le réseau auquel votre machine de développement est connectée autorise le protocole AMQP (Advanced Message Queueing Protocol) sur le port 5671. Cette configuration permet à Azure IoT Tools de communiquer avec Azure IoT Hub.
- [Conteneur Vision par ordinateur d’Azure Cognitive Services](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) pour l’analyse spatiale.
  Pour pouvoir utiliser ce conteneur, vous devez disposer d’une ressource Vision par ordinateur pour obtenir la **clé API** associée ainsi que d’un **URI de point de terminaison**. La clé API est disponible sur les pages Vue d’ensemble et Clés du service Vision par ordinateur dans le portail Azure. Pour démarrer le conteneur, vous avez besoin de la clé et du point de terminaison.

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

1. Pour exécuter le conteneur d’analyse spatiale, vous avez besoin d’un appareil de calcul avec une [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Nous vous recommandons d’utiliser [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) avec l’accélération GPU. Cependant, le conteneur peut s’exécuter sur n’importe quel autre ordinateur de bureau hôte sur lequel est installé [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/).

   #### <a name="azure-stack-edge-device"></a>[Appareil Azure Stack Edge](#tab/azure-stack-edge)

   Azure Stack Edge est une solution matérielle en tant que service ainsi qu’un appareil de computing en périphérie basé sur l’intelligence artificielle. Il est doté de fonctionnalités de transfert de données via le réseau. Pour obtenir des instructions détaillées sur la préparation et la configuration, consultez la [documentation Azure Stack Edge](../../databox-online/azure-stack-edge-deploy-prep.md).

   #### <a name="desktop-machine"></a>[Ordinateur de bureau](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>Conditions matérielles minimales requises

   - RAM système de 4 Go
   - 4 Go de RAM GPU
   - Processeur 8 cœurs
   - 1 GPU NVIDIA Tesla T4
   - 20 GB d’espace HDD

   #### <a name="recommended-hardware"></a>Matériel recommandé

   - RAM système de 32 Go
   - 16 Go de RAM GPU
   - Processeur 8 cœurs
   - 2 GPU NVIDIA Tesla T4
   - 50 Go d’espace SSD

   Dans cet article, vous allez télécharger et installer les packages logiciels suivants. L’ordinateur hôte doit être en mesure d’exécuter les opérations suivantes (voir les instructions ci-dessous) :

   - [Pilotes graphiques NVIDIA](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) et [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
   - Configurations pour [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (service multiprocessus).
   - [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) et [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker)
   - Runtime [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md).

   #### <a name="azure-vm-with-gpu"></a>[Machine virtuelle Azure avec GPU](#tab/virtual-machine)

   Vous pouvez utiliser une [machine virtuelle de série NC](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dotée d’un GPU K80.

   1. Connectez-vous à votre machine virtuelle, puis, dans le terminal, tapez la commande suivante :

        `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
        Le module Azure Video Analyzer s’exécute sur l’appareil de périphérie avec des comptes d’utilisateurs locaux non privilégiés. Il a aussi besoin de certains dossiers locaux pour le stockage des données de configuration d’application. Enfin, pour ce guide pratique, nous tirons profit d’un [simulateur RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) qui relaie un flux vidéo en temps réel vers le module AVA à des fins d’analyse. Ce simulateur prend comme entrée des fichiers vidéo préenregistrés à partir d’un répertoire d’entrée. 
    
        Le script de préparation d’appareil utilisé ci-dessus permet d’automatiser ces tâches, ce qui vous permet de créer en une seule commande tous les dossiers d’entrée et de configuration pertinents, les fichiers d’entrée vidéo et les comptes d’utilisateur avec privilèges. Une fois la commande terminée, les dossiers suivants auront normalement été créés sur votre appareil Edge. 
    
        * `/home/localedgeuser/samples`
        * `/home/localedgeuser/samples/input`
        * `/var/lib/videoanalyzer`
        * `/var/media`
    
        Notez la présence des fichiers vidéo (*.mkv) dans le dossier /home/localedgeuser/samples/input, qui servent de fichiers d’entrée à analyser. 

1. [Configurer l’appareil périphérique](../../cognitive-services/computer-vision/spatial-analysis-container.md#set-up-the-host-computer)

1. Ensuite, déployez les autres ressources Azure.

   [![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > Le bouton ci-dessus crée et utilise la machine virtuelle par défaut qui n’est pas dotée d’un GPU NVIDIA. Utilisez l’option « Utiliser un appareil périphérique existant » lorsque cela vous est demandé dans le modèle Azure Resource Manager (ARM), et utilisez les informations relatives à IoT Hub et à l’appareil de l’étape précédente.
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="Utiliser un appareil existant":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="Vue d’ensemble de l’analyse spatiale":::

Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud [source RTSP](pipeline.md#rtsp-source) extrait le flux vidéo provenant de ce serveur et envoie des images vidéo au nœud `CognitiveServicesVisionProcessor`.

Le nœud `CognitiveServicesVisionProcessor` joue un rôle de proxy. Il convertit les images vidéo dans le type d’image spécifié. Il relaie ensuite l’image via une **mémoire partagée** vers un autre module de périphérie qui exécute des opérations IA derrière un point de terminaison gRPC. Dans cet exemple, ce module de périphérie est le module d’analyse spatiale. Le nœud `CognitiveServicesVisionProcessor` effectue deux opérations :

- Il rassemble les résultats et publie les événements sur le nœud [récepteur IoT Hub](pipeline.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub).
- Il capture également un clip vidéo de 30 secondes à partir de la source RTSP en utilisant un [processeur de signaux](pipeline.md#signal-gate-processor), et le stocke en tant que fichier vidéo.

## <a name="create-the-computer-vision-resource"></a>Créer la ressource Vision par ordinateur

Vous devez créer une ressource Azure de type Vision par ordinateur sur le [Portail Azure](../../iot-edge/how-to-deploy-modules-portal.md) ou via Azure CLI. 

### <a name="gathering-required-parameters"></a>Collecte des paramètres requis

Il existe trois paramètres principaux pour tous les conteneurs Cognitive Services nécessaires, y compris le conteneur spatialanalysis. Le contrat de licence utilisateur final (CLUF) doit être présent et avoir été accepté. Par ailleurs, un URI de point de terminaison et une clé API sont nécessaires.

### <a name="keys-and-endpoint-uri"></a>Clés et URI de point de terminaison

Une clé est utilisée pour démarrer le conteneur d’analyse spatiale ; elle est disponible dans la page `Keys and Endpoint` de la ressource Cognitive Services correspondante sur le portail Azure. Accédez à cette page et recherchez les clés et l’URI de point de terminaison.  

Vous aurez besoin de cette clé et de cet URI de point de terminaison dans vos fichiers de manifeste de déploiement pour déployer le conteneur spatialanalysis.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="URI de point de terminaison":::

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

1. Clonez le dépôt à partir de cet emplacement : [https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp).
1. Dans Visual Studio Code, ouvrez le dossier dans lequel le dépôt a été téléchargé.
1. Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app. À partir de là, créez un fichier et nommez-le *appsettings.json*. Ce fichier contiendra les paramètres nécessaires à l’exécution du programme.
1. Copiez le contenu du fichier appsettings.json à partir du portail Azure. Le texte doit ressembler au code suivant.
   ```json
   {
     "IoThubConnectionString": "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
     "deviceId": "<your Edge Device name>",
     "moduleId": "avaedge"
   }
   ```

1. Accédez au dossier src/edge, puis créez un fichier nommé .env.
1. Copiez le contenu du fichier env.txt à partir du portail Azure. Le texte doit ressembler au code suivant.

   ```env
   SUBSCRIPTION_ID="<Subscription ID>"
   RESOURCE_GROUP="<Resource Group>"
   AVA_PROVISIONING_TOKEN="<The provisioning token>"
   VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
   VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
   APPDATA_FOLDER_ON_DEVICE="/var/lib/videoanalyzer"
   CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
   CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

## <a name="set-up-deployment-template"></a>Configurer un modèle de déploiement

Recherchez le fichier de déploiement dans /src/edge/deployment.spatialAnalysis.template.json. Le modèle contient le module avaedge, le module rtspsim et notre module spatialanalysis.

Il y a plusieurs éléments auxquels vous devez faire attention dans le fichier de modèle de déploiement :

1. Définissez la liaison de port dans le module `spatialanalysis`.

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `IpcMode` dans `avaedge` et le module createOptions `spatialanalysis` doivent être identiques et être définis sur **host**.
1. Pour que le simulateur RTSP fonctionne, vérifiez que le paramètre Volume Bounds est bien configuré lorsque vous utilisez l’appareil Azure Stack Edge.

   1. [Connectez-vous au partage SMB](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) et copiez l’[exemple de fichier vidéo stairwell](https://lvamedia.blob.core.windows.net/public/2018-03-05.10-27-03.10-30-01.admin.G329.mkv) sur le partage local.

      > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWDRJd]

   1. Vérifiez que le module rtspsim présente la configuration suivante :
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>Générer et déployer le manifeste de déploiement

Le manifeste de déploiement définit les modules qui sont déployés sur un appareil de périphérie. Il définit également les paramètres de configuration de ces modules.

Effectuez les étapes suivantes pour générer le manifeste à partir du fichier de modèle, puis déployez-le sur l’appareil de périphérie.

1. Ouvrez Visual Studio Code.
1. À côté du volet `AZURE IOT HUB`, sélectionnez l’icône Autres actions pour définir la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier `src/cloud-to-device-console-app/appsettings.json`.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="Analyse spatiale : chaîne de connexion":::

1. Cliquez avec le bouton droit sur `src/edge/deployment.spatialAnalysis.template.json` et sélectionnez Générer le manifeste de déploiement IoT Edge.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="Analyse spatiale : fichier json amd64 de déploiement":::

   Cette action doit créer un fichier manifeste nommé `deployment.spatialAnalysis.amd64.json` dans le dossier src/edge/config.

1. Cliquez avec le bouton droit sur `src/edge/config/deployment.spatialAnalysis.amd64.json`, sélectionnez **Créer un déploiement pour un seul appareil**, puis sélectionnez le nom de votre appareil périphérique.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="Analyse spatiale : Déployer sur un seul appareil":::

1. En haut de la page, vous êtes invité à sélectionner un appareil IoT Hub et à choisir le nom de votre appareil périphérique dans le menu déroulant.
1. Après environ 30 secondes, en bas à gauche de la fenêtre, actualisez le volet **AZURE IOT HUB**. L’appareil de périphérie affiche maintenant les modules déployés suivants :

   - Azure Video Analyzer (nom de module **avaedge**).
   - Simulateur RTSP (Real-Time Streaming Protocol) (nom de module **rtspsim**).
   - Analyse spatiale (nom de module **spatialanalysis**).

Une fois le déploiement réussi, un message s’affiche dans la fenêtre de sortie :

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

Vous pouvez ensuite rechercher les modules `avaedge`, `spatialanalysis`et `rtspsim` sous Appareils/Modules. Leur état doit être **running** (en cours d’exécution).

## <a name="prepare-to-monitor-events"></a>Préparer la supervision d’événements

Pour voir ces événements, effectuez les étapes suivantes :

1. Dans Visual Studio Code, ouvrez l’onglet **Extensions** (ou appuyez sur Ctrl+Maj+X) et recherchez Azure IoT Hub.
1. Cliquez avec le bouton droit et sélectionnez **Paramètres d’extension**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="Paramètres d’extension":::

1. Recherchez et activez « Afficher le message détaillé ».

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="Afficher le message détaillé":::

1. Ouvrez le volet Explorateur et recherchez **AZURE IOT HUB** en bas à gauche. Cliquez dessus avec le bouton droit puis sélectionnez Démarrer la supervision du point de terminaison d’événement intégré.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="Analyse spatiale : démarrer l’analyse":::

## <a name="run-the-program"></a>Exécuter le programme

Il existe un fichier program.cs qui appelle les méthodes directes dans `src/cloud-to-device-console-app/operations.json`. Nous devons configurer operations.json et fournir la pipelineTopology que le pipeline doit utiliser.

Dans operations.json :

- Définissez la pipelineTopology de la façon suivante :

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json"
      }
  },
  ```

- Créez un livePipeline comme celui-ci, puis définissez les paramètres dans pipelineTopology :

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "InferencingWithCVExtension",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": " rtsp://rtspsim:554/media/2018-03-05.10-27-03.10-30-01.admin.G329.mkv"
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
  },
  ```

  > [!Note]
  > Vérifiez l’utilisation de `CognitiveServicesVisionExtension` pour se connecter au module spatialanalysis. Définissez le paramètre ${grpcUrl} sur **tcp://spatialAnalysis:<NUMÉRO_PORT>** , c’est-à-dire tcp://spatialAnalysis:50051

  ```json
  {
          "@type": "#Microsoft.VideoAnalyzer.CognitiveServicesVisionProcessor",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "operation": {
            "@type": "#Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation",
            "zones": [
              {
                "zone": {
                  "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
                  "polygon": "[[0.37,0.43],[0.48,0.42],[0.53,0.56],[0.34,0.57],[0.34,0.46]]",
                  "name": "stairlanding"
                },
                "events": [
                  {
                    "trigger": "event",
                    "outputFrequency": "1",
                    "threshold": "16",
                    "focus": "bottomCenter"
                  }
                ]
              }
            ]
          }
        }
      ],
  ```

Exécutez une session de débogage en sélectionnant F5, puis suivez les instructions du **TERMINAL**. Cette opération permet de définir pipelineTopology et livePipeline, d’activer livePipeline et enfin, de supprimer les ressources.

## <a name="interpret-results"></a>Interpréter les résultats

Lors de l’instanciation de pipelineTopology, vous devez voir l’événement « MediaSessionEstablished ». Voici un [exemple d’événement MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Le module spatialanalysis enverra également des événements Insights IA à Azure Video Analyzer, puis à IoT Hub, ce qui s’affichera dans la fenêtre **OUTPUT**. Le paramètre ENTITY désigne des objets de détection, et EVENT les événements spaceanalytics. Cette sortie est ensuite passée à Azure Video Analyzer.

Exemple de sortie pour personZoneEvent (à partir de l’opération `SpatialAnalysisPersonZoneCrossingOperation`) :

```
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": [
        "8724dff43d3c4716936aa6c9a808ee2e"
      ],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="operations"></a>Opérations :

### <a name="person-zone-crossing"></a>Traversée d’une zone par une personne

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zones                     | list    | Liste de zones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | Nom convivial de cette zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | string  | Chaque paire de valeurs représente x et y pour les vertex d’un polygone. Le polygone représente les zones dans lesquelles les personnes sont suivies ou comptées. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre. Les événements sont émis lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 48 quand le type est zonecrossing et 16 quand l’heure est DwellTime. Il s’agit des valeurs recommandées pour obtenir une précision maximale. |
| eventType                 | string  | Pour cognitiveservices.vision.spatialanalysis-personcrossingpolygon, cela doit être zonecrossing ou zonedwelltime.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| déclencheur                   | string  | Type de déclencheur pour l’envoi d’un événement. Valeurs prises en charge : « event » : se déclenche quand une personne entre ou quitte la zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| focus                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint.                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": ["8724dff43d3c4716936aa6c9a808ee2e"],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="more-operations"></a>Autres opérations :
Le module `spatialAnalysis` propose différentes opérations :

- **personCount**
- **personDistance**
- **personCrossingLine**
- **personZoneCrossing**
- **customOperation**
<br></br>
<details>
  <summary>Cliquez pour développer et afficher les différentes options de configuration pour chacune des opérations.</summary>

### <a name="person-line-crossing"></a>Traversée d’une ligne par une personne

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lignes                     | list    | Liste de lignes.                                                                                                                                                                                                                                                                |
| name                      | string  | Nom convivial de cette ligne.                                                                                                                                                                                                                                                  |
| line                      | string  | Chaque paire de valeurs représente le début et la fin de la ligne. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre.                            |
| outputFrequency           | int     | Vitesse à laquelle les événements sont émis. Quand output_frequency = X, chaque événement X est émis, par exemple, output_frequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre output_frequency s’applique à la fois à event et à interval.                                                       |
| focus                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint. |
| threshold                 | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 16. Il s’agit de la valeur recommandée pour obtenir une précision maximale.                                                                                                |
| enableFaceMaskClassifier  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.          |
| detectorNodeConfiguration | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                      |

#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="person-distance"></a>Distance de la personne

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zones                     | list    | Liste de zones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | Nom convivial de cette zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | string  | Chaque paire de valeurs représente x et y pour les vertex d’un polygone. Le polygone représente les zones dans lesquelles les personnes sont suivies ou comptées. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre. Les événements sont émis lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 48 quand le type est zonecrossing et 16 quand l’heure est DwellTime. Il s’agit des valeurs recommandées pour obtenir une précision maximale. |
| outputFrequency           | int     | Vitesse à laquelle les événements sont émis. Quand output_frequency = X, chaque événement X est émis, par exemple, output_frequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre output_frequency s’applique à la fois à event et à interval.                                                                                                                                                                                                                                                                                                                                                     |
| focus                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint.                                                                                                                                                                                                                                                                                               |
| threshold                 | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| outputFrequency           | int     | Vitesse à laquelle les événements sont émis. Quand output_frequency = X, chaque événement X est émis, par exemple, output_frequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre output_frequency s’applique à la fois à event et à interval.                                                                                                                                                                                                                                                                                                                                                     |
| minimumDistanceThreshold  | float   | Distance en pieds qui déclenchera un événement « TooClose » lorsque les personnes sont moins éloignées que cette distance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| maximumDistanceThreshold  | float   | Distance en pieds qui déclenchera un événement « TooFar » lorsque les personnes sont plus éloignées que cette distance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| aggregationMethod         | string  | Méthode d’agrégation du résultat persondistance. aggregationMethod s’applique à la fois à mode et à average.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

### <a name="person-count"></a>Nombre de personnes

#### <a name="parameters"></a>Paramètres :

| Nom                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zones                     | list    | Liste de zones.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | string  | Nom convivial de cette zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | string  | Chaque paire de valeurs représente x et y pour les vertex d’un polygone. Le polygone représente les zones dans lesquelles les personnes sont suivies ou comptées. Les valeurs float représentent la position du vertex par rapport au coin supérieur gauche. Pour calculer les valeurs x, y absolues, vous multipliez ces valeurs par la taille de cadre. Les événements sont émis lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone. La valeur par défaut est 48 quand le type est zonecrossing et 16 quand l’heure est DwellTime. Il s’agit des valeurs recommandées pour obtenir une précision maximale. |
| outputFrequency           | int     | Vitesse à laquelle les événements sont émis. Quand output_frequency = X, chaque événement X est émis, par exemple, output_frequency = 2 signifie qu’un événement sur deux fait l’objet d’une sortie. Le paramètre output_frequency s’applique à la fois à event et à interval.                                                                                                                                                                                                                                                                                                                                                     |
| déclencheur                   | string  | Type de déclencheur pour l’envoi d’un événement. Les valeurs prises en charge sont event pour l’envoi d’événements lorsque le nombre change, ou interval pour envoyer des événements régulièrement, que le nombre ait changé ou non.                                                                                                                                                                                                                                                                                                                                                           |
| focus                     | string  | Position du point dans le cadre englobant de la personne utilisé pour calculer les événements. La valeur de focus peut être footprint (l’encombrement de la personne), bottom_center (la partie centrale inférieure du cadre englobant de la personne) ou center (le centre du cadre englobant de la personne). La valeur par défaut est footprint.                                                                                                                                                                                                                                                                                               |
| threshold                 | float   | Les événements sont envoyés lorsque la personne dépasse ce nombre de pixels à l’intérieur de la zone.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| enableFaceMaskClassifier  | boolean | true pour activer la détection des personnes qui portent des masques dans le flux vidéo, false pour la désactiver. Par défaut, cette fonctionnalité est désactivée. La détection des masques nécessite que le paramètre de largeur de vidéo d’entrée soit égal à 1920 ("INPUT_VIDEO_WIDTH": 1920). L’attribut face mask ne sera pas retourné.                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | string  | Paramètres DETECTOR_NODE_CONFIG pour toutes les opérations d’analyse spatiale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

### <a name="custom"></a>Custom

#### <a name="parameters"></a>Paramètres :

| Nom                   | Type   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | string | Représentation JSON de l’opération. |

#### <a name="output"></a>Sortie :

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

</details>

## <a name="playing-back-the-recording"></a>Lecture de l’enregistrement

Vous pouvez examiner la ressource vidéo Video Analyzer qui a été créée par le pipeline en direct en vous connectant au portail Azure et en visionnant la vidéo.

1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.
1. Localisez votre compte Video Analyzer parmi les ressources qui figurent dans votre abonnement, puis ouvrez le volet du compte.
1. Sélectionnez **Vidéos** dans la liste **Video Analyzer**.
1. Vous y trouvez une vidéo portant le nom `personcount`. Il s’agit du nom choisi dans votre fichier de topologie de pipeline.
1. Sélectionnez la vidéo.
1. Dans la page d’informations de la vidéo, cliquez sur l’icône de **lecture**

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="Capture d’écran de la lecture de vidéo":::
   
1. Pour afficher les métadonnées d’inférence sous forme de cadres englobants sur la vidéo, cliquez sur l’icône de **cadre englobant**
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="Icône de cadre englobant":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="troubleshooting"></a>Dépannage

spatialanalysis est un conteneur de grande taille qui peut mettre jusqu’à 30 secondes pour démarrer. Une fois que le conteneur spatialanalysis est opérationnel, il commence à envoyer les événements d’inférences.

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/cafeteria.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:38:42 PM] Message received from [ase03-edge/avaedge]:
{
  "timestamp": 145860472980260,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "647aacf9d8bc47078a1ed31d1c459c24",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.7583008
        },
        "box": {
          "l": 0.48213565,
          "t": 0.21217245,
          "w": 0.056364775,
          "h": 0.29961595
        }
      },
      "extensions": {
        "centerGroundPointY": "0.0",
        "centerGroundPointX": "0.0",
        "footprintX": "0.5087100982666015",
        "footprintY&quot;: &quot;0.49634415356080924"
      }
    },
    {
      "type": "event",
      "inferenceId": "dae6c2b742634196b615c128654845dc",
      "relatedInferences": [
        "647aacf9d8bc47078a1ed31d1c459c24"
      ],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone&quot;: &quot;stairlanding"
        }
      }
    }
  ]
}

```

> [!NOTE]
> Vous pouvez voir des messages **« initializing »** . Ces messages s’affichent lors du démarrage du module spatialAnalysis et peuvent prendre jusqu’à 60 secondes pour passer à l’état « running » (en cours d’exécution). Vous devez attendre un peu avant de voir l’événement d’inférence.

## <a name="next-steps"></a>Étapes suivantes

Essayez les différentes opérations proposées par le module `spatialAnalysis`. Pour cela, consultez les pipelineTopology suivantes :

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [personZoneCrossing](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)

> [!Tip]
> Utilisez un **[exemple de fichier vidéo](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)** qui présente plusieurs personnes dans l’image.
