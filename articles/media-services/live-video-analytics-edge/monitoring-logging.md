---
title: Supervision et journalisation - Azure
description: Cet article fournit une vue d’ensemble de la supervision et de la journalisation dans Live Video Analytics sur IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e81b1e98fb30bb8876c78c8c911585f5448db8f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730238"
---
# <a name="monitoring-and-logging"></a>Surveillance et journalisation

Dans cet article, vous allez apprendre à recevoir des événements pour la supervision à distance à partir du module Live Video Analytics sur IoT Edge. 

Vous apprendrez également à contrôler les journaux générés par le module.

## <a name="taxonomy-of-events"></a>Taxonomie des événements

Live Video Analytics sur IoT Edge émet des événements ou des données de télémétrie, selon la taxonomie suivante :

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagramme illustrant la taxonomie des événements.":::

* En fonctionnement : Événements générés par les actions d’un utilisateur ou lors de l’exécution d’un [graphe multimédia](media-graph-concept.md)
   
   * Volume : Censé être faible (quelques occurrences par minute, voire moins).
   * Exemples :

      - Enregistrement démarré (illustré dans l’exemple suivant)
      - Enregistrement arrêté
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostics : Événements permettant de diagnostiquer les problèmes de performances

   * Volume : Peut être élevé (plusieurs occurrences par minute)
   * Exemples :
   
      - Informations [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) RTSP (illustré dans l’exemple suivant) 
      - Écarts dans le flux vidéo entrant

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics : Événements générés dans le cadre de l’analyse vidéo

   * Volume : Peut être élevé (plusieurs occurrences par minute ou plus)
   * Exemples :
      
      - Mouvement détecté (illustré dans l’exemple suivant) 
      - Résultat de l’inférence

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Les événements émis par le module sont envoyés au [hub IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). De là, ils peuvent être acheminés vers d’autres destinations. 

### <a name="timestamps-in-analytic-events"></a>Timestamps des événements analytiques

Comme indiqué précédemment, des timestamps sont associés aux événements générés dans le cadre de l’analyse vidéo. Si vous avez [enregistré la vidéo en direct](video-recording-concept.md) dans le cadre de votre topologie graphique, ces timestamps vous permettent de trouver où un événement particulier s’est produit dans la vidéo enregistrée. Les instructions suivantes expliquent comment mapper le timestamp d’un événement analytique avec la chronologie de la vidéo enregistrée dans un [élément multimédia Azure Media Services](terminology.md#asset).

Tout d'abord, extrayez la valeur `eventTime`. Utilisez cette valeur dans un [filtre d'intervalle de temps](playback-recordings-how-to.md#time-range-filters) pour extraire une portion appropriée de l'enregistrement. Par exemple, vous souhaitez peut-être récupérer une vidéo qui commence 30 secondes avant `eventTime` et se termine 30 secondes après. Pour l’exemple précédent, où `eventTime` est 2020-05-12T23:33:09.381Z, une demande de manifeste HLS pour les 30 secondes avant et après `eventTime` ressemblerait à cette demande :

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

L’URL précédente renvoie une [playlist principale](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) maître qui contient des URL pour les playlists multimédia. La playlist multimédia contiendrait des entrées semblables à celle-ci :

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
L’entrée précédente indique qu’un fragment vidéo est disponible et qu’il commence à une valeur de `timestamp` de `143039375031270`. La valeur de `timestamp` dans l’événement analytique utilise la même échelle de temps que la playlist multimédia. Elle peut être utilisée pour identifier le fragment vidéo approprié et rechercher l’image appropriée.

Pour plus d’informations, consultez ces [articles sur la recherche précise d’images](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) dans HLS.

## <a name="controlling-events"></a>Contrôle des événements

Vous pouvez utiliser les propriétés de jumeau de module suivantes pour contrôler les événements opérationnels et de diagnostic publiés par le module Live Video Analytics sur IoT Edge. Ces propriétés sont documentées dans le [schéma JSON de jumeau de module](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName` : Pour obtenir des événements de diagnostic en provenance du module, incluez cette propriété et fournissez une valeur pour celle-ci. Omettez-la ou laissez-la vide pour empêcher le module de publier des événements de diagnostic.
   
- `operationalEventsOutputName` : Pour obtenir des événements opérationnels en provenance du module, incluez cette propriété et fournissez une valeur pour celle-ci. Omettez-la ou laissez-la vide pour empêcher le module de publier des événements opérationnels.
   
Les événements analytiques sont générés par des nœuds tels que le processeur de détection de mouvement ou le processeur d’extension HTTP. Le récepteur du hub IoT est utilisé pour les envoyer au hub IoT Edge. 

Vous pouvez contrôler le [routage de tous les événements précédents](../../iot-edge/module-composition.md#declare-routes) à l’aide de la propriété `desired` du jumeau de module `$edgeHub` dans le manifeste de déploiement :

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Dans le code JSON précédent, `lvaEdge` est le nom du module Live Video Analytics sur IoT Edge. La règle d’acheminement suit le schéma défini dans [Déclarer des itinéraires](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Pour veiller à ce que les événements analytiques atteignent le hub IoT Edge, vous devez disposer d’un nœud récepteur du hub IoT en aval d’un nœud de processeur de détection de mouvement et/ou d’un nœud de processeur d’extension HTTP.

## <a name="event-schema"></a>Schéma d’événement

Les événements proviennent du périphérique et peuvent être utilisés sur cette périphérie ou dans le cloud. Les événements générés par Live Video Analytics sur IoT Edge sont conformes au [modèle de messagerie en diffusion en continu](../../iot-hub/iot-hub-devguide-messages-construct.md) établi par Azure IoT Hub. Le modèle se compose de propriétés système, de propriétés d’application et d’un corps.

### <a name="summary"></a>Résumé

Lorsqu’il est observé via IoT Hub, chaque événement possède un ensemble de propriétés communes :

|Propriété   |Type de propriété| Type de données   |Description|
|---|---|---|---|
|`message-id`   |système |guid|  ID d’événement unique.|
|`topic`|   applicationProperty |string|    Chemin Azure Resource Manager du compte Azure Media Services.|
|`subject`| applicationProperty |string|    Sous-chemin de l’entité émettrice de l’événement.|
|`eventTime`|   applicationProperty|    string| Heure à laquelle l’événement a été généré.|
|`eventType`|   applicationProperty |string|    Identificateur du type d’événement. (Voir la section suivante.)|
|`body`|body    |object|    Données d’événement particulières.|
|`dataVersion`  |applicationProperty|   string  |{Majeure}.{Mineure}|

### <a name="properties"></a>Propriétés

#### <a name="message-id"></a>message-id

Identificateur global unique (GUID) de l’événement.

#### <a name="topic"></a>topic

Représente le compte Azure Media Services associé au graphe.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Entité qui émet l’événement :

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

La propriété `subject` vous permet de mapper des événements génériques au module de génération. Par exemple, en cas de nom d’utilisateur ou mot de passe RTSP non valide, l’événement généré est `Microsoft.Media.Graph.Diagnostics.ProtocolError` sur le nœud `/graphInstances/myGraph/sources/myRtspSource`.

#### <a name="event-types"></a>Types d’événements

Les types d’événements sont attribués à un espace de noms selon ce schéma :

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Classes d’événements

|Nom de classe|Description|
|---|---|
|Analytics  |Événements générés dans le cadre de l’analyse du contenu.|
|Diagnostics    |Événements qui facilitent le diagnostic des problèmes et du niveau de performance.|
|En fonctionnement    |Événements générés dans le cadre des opérations des ressources.|

Les types d’événements sont propres à chaque classe d’événement.

Exemples :

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Heure de l’événement

L’heure de l’événement est formatée dans une chaîne ISO 8601. Elle représente l’heure à laquelle l’événement s’est produit.

### <a name="azure-monitor-collection-via-telegraf"></a>Collection Azure Monitor via Telegraf

Seront rapportées du module Live Video Analytics sur IoT Edge les métriques suivantes :  

|Nom de métrique|Type|Étiquette|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Jauge|iothub, edge_device, module_name, graph_topology|Nombre total de graphes actifs par topologie.|
|lva_received_bytes_total|Compteur|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Nombre total d’octets reçus par un nœud. Pris en charge uniquement pour les sources RTSP.|
|lva_data_dropped_total|Compteur|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Compteur des données supprimées (événements, médias, etc.).|

> [!NOTE]
> Un [point de terminaison Prometheus](https://prometheus.io/docs/practices/naming/) est exposé au port 9600 du conteneur. Si vous nommez votre module Live Video Analytics sur IoT Edge « lvaEdge », il pourra accéder aux métriques en envoyant une requête GET à http://lvaEdge:9600/metrics.   

Pour activer la collecte de métriques à partir du module Live Video Analytics sur IoT Edge, procédez comme suit :

1. Créez un dossier sur votre ordinateur de développement et accédez-y.

1. Dans le dossier, créez un fichier `telegraf.toml` qui contient les configurations suivantes :
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Veillez à remplacer les variables dans le fichier .toml. Les variables sont désignées par des accolades (`{}`).

1. Dans le même dossier, créez un Dockerfile qui contient les commandes suivantes :
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Utilisez les commandes de l’interface de ligne de commande Docker pour générer le fichier Docker et publier l’image dans votre registre de conteneurs Azure.
    
   Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Docker pour effectuer un envoi (push) vers un registre de conteneurs, consultez [Envoyer (push) et tirer (pull) des images Docker](../../container-registry/container-registry-get-started-docker-cli.md). Pour plus d’informations sur Azure Container Registry, consultez la [documentation](../../container-registry/index.yml).


1. Une fois l’envoi (push) vers Azure Container Registry terminé, ajoutez le nœud suivant à votre manifeste de déploiement :
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Veillez à remplacer les variables dans le fichier manifeste. Les variables sont désignées par des accolades (`{}`).


   Azure Monitor peut être [authentifié via le principal de service](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Le plug-in Azure Monitor Telegraf expose [plusieurs méthodes d’authentification](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Pour utiliser l’authentification par principal de service, définissez les variables d’environnement suivantes :  
     `AZURE_TENANT_ID` : locataire auprès duquel l’authentification aura lieu.  
     `AZURE_CLIENT_ID` : ID client de l’application à utiliser.  
     `AZURE_CLIENT_SECRET` : secret de l’application à utiliser.  
     
     >[!TIP]
     > Vous pouvez attribuer au principal du service le rôle **Éditeur de métriques de surveillance**. Suivez les étapes décrites dans **[Créer un principal de service](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** pour créer le principal de service et attribuer le rôle.

1. Une fois les modules déployés, les mesures s’affichent dans Azure Monitor sous un espace de noms unique. Les noms de métriques correspondent à ceux émis par Prometheus. 

   Dans ce cas, dans le portail Azure, accédez au hub IoT et sélectionnez **Métriques** dans le volet gauche. Les métriques y figurent.

### <a name="log-analytics-metrics-collection"></a>Collecte de métriques Log Analytics
En utilisant le [point de terminaison Prometheus](https://prometheus.io/docs/practices/naming/) avec [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-analytics-tutorial), vous pouvez générer et [superviser des métriques](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported) telles que CPUPercent, MemoryUsedPercent, etc.   

> [!NOTE]
> La configuration ci-dessous ne collecte pas de journaux, mais **uniquement des métriques**. Il est possible d’étendre le module de collecte afin de collecter et charger également des journaux.

[ ![Diagramme qui illustre la collecte de métriques à l’aide de Log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Découvrez comment [collecter des métriques](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector).
1. Utilisez des commandes de l’interface de ligne de commande Docker pour générer le [fichier Docker](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) et publier l’image dans votre registre de conteneurs Azure.
    
   Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Docker pour effectuer un envoi (push) vers un registre de conteneurs, consultez [Envoyer (push) et tirer (pull) des images Docker](../../container-registry/container-registry-get-started-docker-cli.md). Pour plus d’informations sur Azure Container Registry, consultez la [documentation](../../container-registry/index.yml).

1. Une fois l’envoi (push) vers Azure Container Registry terminé, les éléments suivants sont insérés dans le manifeste de déploiement :
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Les modules `edgeHub`, `edgeAgent` et `lvaEdge` sont les noms des modules définis dans le fichier manifeste de déploiement. Vérifiez que les noms des modules correspondent.   

    Vous pouvez récupérer vos valeurs `LogAnalyticsWorkspaceId` et `LogAnalyticsSharedKey` en effectuant les étapes suivantes :
    1. Accédez au portail Azure.
    1. Recherchez vos espaces de travail Log Analytics.
    1. Une fois que vous avez trouvé votre espace de travail Log Analytics, accédez à l’option `Agents management` dans le volet de navigation gauche.
    1. Vous y trouverez l’ID de l’espace de travail et les clés secrètes que vous pouvez utiliser.

1. Ensuite, créez un classeur en cliquant sur l’onglet `Workbooks` dans le volet de navigation gauche.
1. À l’aide du langage de requête Kusto, vous pouvez écrire des requêtes comme ci-dessous et obtenir le pourcentage du processeur utilisé par les modules IoT Edge.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Diagramme montrant les métriques à l’aide d’une requête Kusto.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Journalisation

Comme avec d’autres modules IoT Edge, vous pouvez également [examiner les journaux de conteneur](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) sur le périphérique. Vous pouvez configurer les informations écrites dans les journaux à l’aide des [propriétés de jumeau de module](module-twin-configuration-schema.md) suivantes :

* `logLevel`

   * Les valeurs autorisées sont `Verbose`, `Information`, `Warning`, `Error` et `None`.
   * La valeur par défaut est `Information`. Les journaux contiendront des messages d’erreur, d’avertissement et d’information.
   * Si vous définissez la valeur sur `Warning`, les journaux contiennent des messages d’erreur et d’avertissement.
   * Si vous définissez la valeur sur `Error`, les journaux contiennent uniquement des messages d’erreur.
   * Si vous définissez la valeur sur `None`, aucun journal n’est généré. (Nous ne recommandons pas cette configuration.)
   * Utilisez `Verbose` uniquement si vous devez partager des journaux avec Support Azure pour diagnostiquer un problème.

* `logCategories`

   * Liste séparée par des virgules d’une ou plusieurs des valeurs suivantes : `Application`, `Events`, `MediaPipeline`.
   * La valeur par défaut est `Application, Events`.
   * `Application` : Informations élémentaires provenant du module, comme des messages de démarrage du module, des erreurs d’environnement et des appels de méthode directs.
   * `Events` : Tous les événements décrits précédemment dans cet article.
   * `MediaPipeline` : Journaux détaillés qui peuvent offrir des insights lorsque vous résolvez des problèmes, comme des difficultés à établir une connexion avec une caméra compatible RTSP.
   
### <a name="generating-debug-logs"></a>Génération des journaux de débogage

Dans certains cas, pour aider Support Azure à résoudre un problème, vous aurez peut-être besoin de générer des journaux plus détaillés que ceux décrits précédemment. Pour générer ces journaux :

1. [Liez le stockage du module au stockage de l’appareil](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via `createOptions`. Si vous examinez un [modèle de manifeste de déploiement](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) à partir des démarrages rapides, vous verrez ce code :

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Ce code permet au module Edge d’écrire des journaux sur le chemin de stockage `/var/local/mediaservices/` de l’appareil. 

 1. Ajoutez la propriété `desired` suivante au module :

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Le module écrira maintenant les journaux de débogage dans un format binaire sous le chemin de stockage `/var/local/mediaservices/debuglogs/` de l’appareil. Vous pouvez partager ces journaux avec Support Azure.

## <a name="faq"></a>Forum Aux Questions

Si vous avez des questions, consultez la [FAQ sur la supervision et les métriques](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Étapes suivantes

[Enregistrement vidéo en continu](continuous-video-recording-tutorial.md)
