---
title: Analyser des flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale - Azure
description: Ce didacticiel explique comment utiliser Live Video Analytics avec la fonctionnalité IA d’analyse spatiale Vision par ordinateur d’Azure Cognitive Services pour analyser un flux vidéo en direct à partir d’une caméra IP (simulée).
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929654"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analyser des flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale (préversion)

Ce didacticiel explique comment utiliser Live Video Analytics avec le [service IA d’analyse spatiale Vision par ordinateur d’Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pour analyser un flux vidéo en direct à partir d’une caméra IP (simulée). Vous y verrez comment ce serveur d’inférence vous permet d’analyser du contenu vidéo diffusé en continu pour comprendre les relations spatiales entre les personnes et leur déplacement dans l’espace physique.  Un sous-ensemble d’images du flux vidéo est envoyé à ce serveur d’inférence, après quoi les résultats sont envoyés à IoT Edge Hub. Lorsque certaines conditions sont remplies, les clips vidéo sont alors enregistrés et stockés sous forme de ressources Azure Media Services.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer des ressources.
> * Examinez le code.
> * Exécuter l’exemple de code.
> * Analyser des événements.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

Lisez ces articles avant de commencer :

* [Présentation de Live Video Analytics sur IoT Edge](overview.md)
* [Terminologie de Live Video Analytics sur IoT Edge](terminology.md)
* [Concepts relatifs aux graphes multimédia](media-graph-concept.md)
* [Enregistrement de vidéo basé sur les événements](event-based-video-recording-concept.md)
* [Tutoriel : Développement d’un module IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Déployer Live Video Analytics sur Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Prérequis

Vous trouverez ci-dessous les conditions préalables à remplir pour connecter le module d’analyse spatiale au module Live Video Analytics.

* [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement. Vérifiez que vous disposez de l’[extension Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Vérifiez que le réseau auquel votre machine de développement est connectée autorise le protocole AMQP (Advanced Message Queueing Protocol) sur le port 5671. Cette configuration permet à Azure IoT Tools de communiquer avec Azure IoT Hub.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) avec accélération GPU.  
    Nous vous recommandons d’utiliser Azure Stack Edge avec l’accélération GPU, bien que le conteneur s’exécute sur n’importe quel autre appareil doté d’un [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Conteneur Vision par ordinateur d’Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pour l’analyse spatiale.  
    Pour pouvoir utiliser ce conteneur, vous devez disposer d’une ressource Vision par ordinateur pour obtenir la **clé API** associée ainsi que d’un **URI de point de terminaison**. La clé API est disponible sur les pages Vue d’ensemble et Clés du service Vision par ordinateur dans le portail Azure. Pour démarrer le conteneur, vous avez besoin de la clé et du point de terminaison.

## <a name="overview"></a>Vue d’ensemble

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Vue d’ensemble de l’analyse spatiale":::
 
Ce diagramme montre comment les signaux circulent dans ce tutoriel. Un [module de périphérie](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simule une caméra IP hébergeant un serveur RTSP (Real-Time Streaming Protocol). Un nœud de [source RTSP](media-graph-concept.md#rtsp-source) récupère le flux vidéo à partir de ce serveur et envoie des images vidéo au nœud [processeur de filtre de fréquence d’images](media-graph-concept.md#frame-rate-filter-processor). Ce processeur limite la fréquence d’images du flux vidéo qui atteint le nœud processeur MediaGraphCognitiveServicesVisionExtension.

Le nœud MediaGraphCognitiveServicesVisionExtension joue le rôle d’un proxy. Il convertit les images vidéo dans le type d’image spécifié. Il relaie ensuite l’image via une **mémoire partagée** vers un autre module de périphérie qui exécute des opérations IA derrière un point de terminaison gRPC. Dans cet exemple, ce module de périphérie est le module d’analyse spatiale. Le nœud processeur MediaGraphCognitiveServicesVisionExtension effectue deux opérations :

* Il rassemble les résultats et publie les événements sur le nœud [récepteur IoT Hub](media-graph-concept.md#iot-hub-message-sink). Le nœud envoie ensuite ces événements à [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub). 
* Il capture également un clip vidéo de 30 secondes à partir de la source RTSP en utilisant un [processeur de signaux](media-graph-concept.md#signal-gate-processor) et le stocke en tant que ressource Media Services.

## <a name="create-the-computer-vision-resource"></a>Créer la ressource Vision par ordinateur

Vous devez créer une ressource Azure de type Vision par ordinateur sur le [Portail Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) ou via Azure CLI. Vous serez en mesure de créer la ressource une fois que votre demande d’accès au conteneur aura été approuvée et que votre ID d’abonnement Azure aura été inscrit. Rendez-vous sur https://aka.ms/csgate pour envoyer votre cas d’usage et votre ID d’abonnement Azure.  Vous devez créer la ressource Azure à l’aide du même abonnement Azure que celui qui a été fourni dans le formulaire de demande d’accès.

### <a name="gathering-required-parameters"></a>Collecte des paramètres requis

Il existe trois paramètres principaux pour tous les conteneurs de Cognitive Services requis, y compris le conteneur d’analyse spatiale. Le contrat de licence utilisateur final (CLUF) doit être présent et avoir été accepté. En outre, une URL de point de terminaison et une clé API sont nécessaires.

### <a name="endpoint-uri-endpoint_uri"></a>URI de point de terminaison {ENDPOINT_URI}

La valeur URI de point de terminaison est disponible sur la page Vue d’ensemble du portail Azure de la ressource Cognitive Services. Accédez à la page Vue d’ensemble et recherchez l’URI de point de terminaison. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Clés et point de terminaison":::

### <a name="keys-api_key"></a>Clés {API_KEY}

Cette clé est utilisée pour démarrer le conteneur d’analyse spatiale et est disponible sur la page Clés de la ressource Cognitive Services correspondante sur le portail Azure. Accédez à la page Clés, puis recherchez les clés.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="URI de point de terminaison":::

## <a name="set-up-azure-stack-edge"></a>Configurer Azure Stack Edge

Suivez [ces étapes](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) pour configurer Azure Stack Edge, puis continuez à suivre les étapes ci-dessous pour déployer le module Live Video Analytics et le module d’analyse spatiale.

## <a name="set-up-your-development-environment"></a>Configurer l''environnement de développement

1. Clonez le dépôt à partir de cet emplacement : https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Dans Visual Studio Code, ouvrez le dossier dans lequel le dépôt a été téléchargé.
1. Dans Visual Studio Code, accédez au dossier src/cloud-to-device-console-app. À partir de là, créez un fichier et nommez-le appsettings.json. Ce fichier contiendra les paramètres nécessaires à l’exécution du programme.
1. Récupérez la valeur IotHubConnectionString dans Azure Stack Edge en procédant comme suit :

    * Accédez à votre IoT Hub dans le Portail Azure, puis cliquez sur `Shared access policies` dans le volet de navigation de gauche.
    * Cliquez sur `iothubowner` pour obtenir les clés d’accès partagé.
    * Copiez la valeur `Connection String – primary key` et collez-la dans la zone de saisie sur le VSCode.
    
        La chaîne de connexion doit ressembler à : <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Copiez le contenu ci-dessous dans le fichier. Veillez à remplacer les variables.
    
    ```json
    {
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Accédez au dossier src/edge, puis créez un fichier nommé .env.
1. Copiez le contenu du fichier /clouddrive/lva-sample/edge-deployment/.env. Le texte doit ressembler au code suivant.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Configurer un modèle de déploiement  

Ajoutez le module d’analyse spatiale dans le fichier /src/edge/deployment.template.json. Le modèle contient le module lvaEdge, le module rtspsim et notre module d’analyse spatiale.

<p>
<details>
<summary>Développez-le et reportez-vous à notre exemple de modèle de déploiement.  
Copiez le contenu à partir de cet emplacement et collez-le dans votre fichier /src/edge/deployment.template.json.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
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
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

Tenez bien compte des conditions suivantes :

1. Définissez la liaison de ports.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. Le paramètre IpcMode dans le module lvaEdge et le paramètre createOptions dans le module d’analyse spatiale doivent être identiques et être définis sur l’hôte.
1. Votre fichier de modèle de déploiement doit contenir le terme « deployment » dans le nom de fichier ; dans le cas contraire, il ne sera pas reconnu et ne pourra pas générer le manifeste nécessaire au déploiement.
1. Pour que le simulateur RTSP fonctionne, assurez-vous que vous avez configuré le paramètre Volume Bounds. Pour plus d’informations, consultez la page [Setup Docker Volume Mounts](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts) (Configurer les montages de volumes Docker).

    1. [Connectez-vous au partage SMB](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share) et copiez le [fichier vidéo](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) sur le partage local.
    1. Vérifiez que le module rtspsim contient les éléments suivants :
        
        ```json
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
1. En regard du volet AZURE IOT HUB, sélectionnez l’icône Autres actions pour définir la chaîne de connexion IoT Hub. Vous pouvez copier la chaîne à partir du fichier src/cloud-to-device-console-app/appsettings.json.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Analyse spatiale : chaîne de connexion":::
1. Cliquez avec le bouton droit sur src/edge/deployment.template.json, puis sélectionnez Générez un manifeste de déploiement IoT Edge.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Analyse spatiale : fichier json amd64 de déploiement":::
    
    Cette action doit créer un fichier manifeste nommé deployment.amd64.json dans le dossier src/edge/config.
1. Cliquez avec le bouton droit sur src/edge/config/deployment.amd64.json, sélectionnez Créer un déploiement pour un seul appareil, puis sélectionnez le nom de votre appareil de périphérie.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Analyse spatiale : fichier json du modèle de déploiement":::   
1. Quand vous êtes invité à sélectionner un appareil IoT Hub, choisissez le nom de votre instance Azure Stack Edge dans le menu déroulant.
1. Après environ 30 secondes, dans l’angle en bas à gauche de la fenêtre, actualisez Azure IoT Hub. L’appareil de périphérie affiche maintenant les modules déployés suivants :
    
    * Live Video Analytics sur IoT Edge (nom du module lvaEdge)
    * Simulateur RTSP (Real-Time Streaming Protocol) (nom de module rtspsim)
    * Analyse spatiale (nom de module spatialAnalysis).
    
Si le déploiement réussit, un message semblable à ce qui suit s’affiche :

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Vous pouvez ensuite rechercher les modules `lvaEdge`, `rtspsim`, `spatialAnalysis` et `rtspsim` sous Appareils/Modules ; vérifiez qu’ils sont à l’état « en cours d’exécution ».

## <a name="prepare-to-monitor-events"></a>Préparer la supervision d’événements

Pour voir ces événements, effectuez les étapes suivantes :

1. Ouvrez le volet Explorateur dans Visual Studio Code, puis recherchez Azure IoT Hub dans l’angle inférieur gauche.
1. Développez le nœud Appareils.
1. Cliquez avec le bouton droit sur votre instance Azure Stack Edge et sélectionnez Start Monitoring Built-in Event Endpoint (Démarrer la surveillance du point de terminaison d’événements intégré).
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Analyse spatiale : démarrer l’analyse":::
     
## <a name="run-the-program"></a>Exécuter le programme

Il existe un fichier program.cs qui permet d’appeler les méthodes directes dans src/cloud-to-device-console-app/operations.json. Nous devons configurer operations.json et fournir une topologie pour l’utilisation de Media Graph.
Dans operations.json :

Définissez la topologie comme suit (topologyFile pour la topologie locale, topologyUrl pour la topologie en ligne) :

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

Créer une instance Graph comme suit, en définissant ici les paramètres de la topologie :

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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

<p>
<details>
<summary>Développez pour voir l’exemple de fichier de topologie obtenu pour le module spatialAnalysis :
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
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
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
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
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
        {
          "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "image": {
            "scale": {
              "mode": "pad",
              "width": "1408",
              "height": "786"
            },
            "format": {
              "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
              "pixelFormat": "bgr24"
            }
          },
          "inputs": [
            {
              "nodeName": "frameRateFilter"
            }
          ]
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


Utilisez MediaGraphRealTimeComputerVisionExtension pour vous connecter au module d’analyse spatiale. Définissez le paramètre ${grpcUrl} dans tcp://spatialAnalysis:<PORT_NUMBER>, c’est-à-dire tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

Exécutez une session de débogage et suivez les instructions du Terminal. Cette opération permet de définir la topologie, de définir l’instance Graph, d’activer l’instance Graph et enfin supprime les ressources.

## <a name="interpret-results"></a>Interpréter les résultats

Lors de l’instanciation d’un Media Graph, vous devez voir l’événement « MediaSessionEstablished » ; dans notre cas, un [exemple d’événement MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Le module d’analyse spatiale enverra également des événements AI Insight à Live Video Analytics, puis à IoT Hub, et affiche le contenu en sortie. Le paramètre ENTITY désigne des objets de détection, et EVENT les événements spaceanalytics. Cette sortie est ensuite transmise à Live Video Analytics.

Exemple de sortie pour personZoneEvent (à partir de l’opération cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics) :

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Essayez les différentes opérations qu’offre le module `spatialAnalysis`, par exemple **personCount** et **personDistance** en basculant l’indicateur « enabled » dans le nœud graphique de votre fichier manifeste de déploiement.
>[!Tip]
> Utilisez un [fichier vidéo](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) qui présente plusieurs personnes dans une image.

> [!NOTE]
> Vous ne pouvez exécuter qu’une seule opération à la fois. Veillez donc à ce qu’un seul indicateur soit défini sur **true** et que les autres soient définis sur **false**.
