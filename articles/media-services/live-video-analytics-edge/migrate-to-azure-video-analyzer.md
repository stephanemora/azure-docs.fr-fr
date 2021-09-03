---
title: Migrer de Live Video Analytics vers Azure Video Analyzer
description: Découvrez comment migrer de Live Video Analytics vers Azure Video Analyzer.
ms.topic: how-to
ms.date: 08/16/2021
ms.openlocfilehash: a2225e25685bb9177851379fd86b59c986ff28f0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122535009"
---
# <a name="how-to-migrate-from-live-video-analytics-to-azure-video-analyzer"></a>Comment migrer de Live Video Analytics vers Azure Video Analyzer

Cet article doit être utilisé si vous avez déployé le module Edge Live Video Analytics sur un appareil IoT Edge et que vous exécutez actuellement des graphiques multimédias pour traiter les flux à partir de caméras RTSP (Real Time Streaming Protocol).

Si vous n’utilisez pas de caméras RTSP et que vous utilisez l’un des [guides de démarrage rapide ou des didacticiels sur Live Video Analytics](../live-video-analytics-edge/overview.md), vous devez simplement basculer vers le [guide de démarrage rapide ou le didacticiel correspondant avec Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/overview.md).

Les instructions suivantes s’appliquent à la migration de Live Video Analytics vers Azure Video Analyzer pour un seul appareil IoT Edge. Vous devez répéter les étapes pour chaque appareil IoT Edge séparé.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure actif.

   > [!NOTE]
   > Vous allez avoir besoin d’un abonnement Azure dans lequel vous avez accès aux rôles  [Contributeur](../../role-based-access-control/built-in-roles.md)  et  [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md)  au groupe de ressources sous lequel vous allez créer des ressources (identité managée affectée par l’utilisateur, compte de stockage, compte Video Analyzer). Si vous ne disposez pas des autorisations appropriées, demandez à l’administrateur de compte qu’il vous les octroie. 

* Vous aurez besoin de privilèges administratifs sur le ou les appareils IoT Edge sur lesquels vous avez installé Live Video Analytics.

## <a name="create-a-backup-of-current-settings"></a>Créer une sauvegarde des paramètres actuels

Avant de commencer tout effort de migration, il est fortement recommandé d’enregistrer votre manifeste de déploiement Edge et les configurations de graphiques multimédias Live Video Analytics.

> [!NOTE]
> Les étapes répertoriées au-dessous des informations d’identification et d’URL de flux de vos caméras RTSP ne peuvent pas être récupérées.  Vous devez récupérer ces informations directement à partir de l’application de gestion de la caméra.

### <a name="save-the-current-deployment-manifest"></a>Enregistrer le manifeste de déploiement actuel

1. Connectez-vous au portail Azure.

1. Accédez à **IoT Hub** et sélectionnez **IoT Edge** sous **Gestion automatique des appareils**.

1. Sous **IOT Edge**, sélectionnez l’appareil IOT Edge que vous souhaitez migrer vers Azure Video Analyzer.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="Capture d’écran montrant des appareils IoT Edge.":::

1. Sélectionnez **Définir des modules**, puis **Vérifier + créer**.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="Capture d’écran montrant le manifeste de déploiement.":::

1. Copiez la section **Déploiement** dans la fenêtre et enregistrez-la dans un emplacement sûr.

1. Sélectionnez le **X** en haut à droite du portail.  

    > [!NOTE]
    > Ne sélectionnez *pas* **Créer**.  Passez en revue le manifeste de déploiement et localisez le nœud `lvaEdge` sous `modules`.  Si vous avez un nœud `env` sous le nœud `lvaEdge`, notez les valeurs de LOCAL_EDGE_USER et lOCAL_GROUP_ID. Vous en aurez besoin plus tard dans le document.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="Capture d’écran montrant le manifeste de déploiement et la section env pour l’utilisateur et le groupe de périphérie locaux.":::

### <a name="save-the-live-video-analytics-topologies"></a>Enregistrer les topologies Live Video Analytics

1. Pour enregistrer votre graphique multimédia Live Video Analytics, sélectionnez le module Live Video Analytics sous l’appareil IoT Edge.  Dans l’exemple suivant, le nom de conteneur est *IvaEdge*.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="Capture d’écran montrant le nom du conteneur du module IoT Edge Live Video Analytics.":::

1. Sélectionnez **Méthode directe**.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Capture d’écran montrant les appels de méthode directe pour Live Video Analytics.":::

1. Entrez **GraphTopologyList** dans le champ Nom de la méthode et entrez le code suivant dans la charge utile :

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="Capture d’écran montrant l’appel de méthode directe IoT Edge.":::

1. Sélectionnez **Appeler la méthode**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="Capture d’écran qui montre la méthode Invoke.":::

1. Le retour sera affiché dans la section des résultats.  Il s’agit de votre topologie de graphique Live Video Analytics.  Copiez le fichier au format JSON et enregistrez-le en lieu sûr.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="Capture d’écran qui montre le résultat de la méthode GraphTopologyList.":::

1. Répétez le processus, mais remplacez `GraphTopologyList` par `GraphInstanceList` et enregistrez la section de résultat.  Voici une liste de vos instances de graphe. Notez le nom de toutes les instances actives.

1.  Après avoir créé une copie de sauvegarde de vos paramètres Live Video Analytics, vous pouvez *Désactiver tous les graphiques multimédias actifs* en entrant **GraphInstanceDeactivate** dans **Nom de la méthode**, puis en entrant le code suivant :

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > Remplacez Graph_Name par le nom des instances actives dans les nouveaux résultats.  Répétez cette procédure jusqu’à ce que tous les graphiques actifs soient désactivés. 

## <a name="azure-video-analyzer-device-preparation"></a>Azure Video Analyzer : préparation de l’appareil 

Pour démarrer la migration, l’appareil IoT Edge doit être configuré pour exécuter le module Azure Video Analyzer. Le module Azure Video Analyzer doit être configuré pour s’exécuter sur l’appareil IoT Edge avec un compte d’utilisateur local non privilégié. Le module a aussi besoin de certains dossiers locaux pour stocker les données de configuration d’application. Le module simulateur de caméra RTSP a besoin de fichiers vidéo avec lesquels il peut synthétiser un flux vidéo en direct. 

1. Connectez-vous à l’appareil IoT Edge par le biais de SSH.

    > [!Note]
    > En fonction de la révision de votre fichier de manifeste de l’appareil IoT Edge (étape 6 dans la section « Créer une sauvegarde des paramètres actuel »), vous devez créer un nouvel utilisateur/groupe sur l’appareil IoT Edge ou réutiliser ceux qui ont été créés sous Live Video Analytics.

    Si l’appareil IoT Edge a été configuré avec un ID d’utilisateur et de groupe local pour Live Video Analytics (figurant dans le manifeste de déploiement), vous pouvez ignorer l’étape 1, mais notez le nom et la valeur de l’utilisateur et du groupe. Ces derniers seront utilisés plus loin dans ce document.  Si vous utilisez également une structure de dossiers unique pour votre déploiement Live Video Analytics (figurant dans le manifeste de déploiement), remplacez *videoanalyzer* par ces valeurs et omettez les deux premières lignes à l’étape 2.  

    L’exemple suivant illustre une structure de dossiers unique pour un déploiement Live Video Analytics.  Notez que *applicationDataDirectory* est /var/lib/videofiles. Dans le cadre de cet exemple, Live Video Analytics a créé *localedgeuser* et *localedgegroup* sur l’appareil IoT Edge.

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    Exécutez ensuite le code suivant :

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. Créez un compte d’utilisateur local sur l’appareil IoT Edge en exécutant le code suivant :

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. Créez la structure de dossiers *avaedge* avec le code suivant :

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## <a name="create-azure-resources"></a>Créer des ressources Azure

L’étape suivante consiste à créer les ressources Azure nécessaires. Les instructions suivantes sont documentées dans la [section Créer des ressources Azure](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md) et répétées ici pour des raisons pratiques. 

Vous allez à présent créer les ressources Azure requises (compte Video Analyzer, compte de stockage et identité managée affectée par l’utilisateur). 

Lorsque vous créez un compte Azure Video Analyzer, vous devez y associer un compte de stockage Azure. Si vous utilisez Video Analyzer pour enregistrer la vidéo en direct à partir d’une caméra, ces données sont stockées en tant que blobs dans un conteneur dans le compte de stockage. Vous devez utiliser une identité managée pour accorder au compte Video Analyzer l’accès approprié au compte de stockage. 

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Créer un compte Video Analyzer dans le portail Azure

1. Connectez-vous au  [portail Azure](https://portal.azure.com/). 

1. Dans la zone de recherche, entrez  **Video Analyzer**. 

1. Sélectionnez  **Analyseurs vidéo** sous **Services**. 

1. Sélectionnez **Ajouter**. 

1. Dans la section  **Créer un compte Video Analyzer** , entrez les valeurs requises : 

   - **Abonnement** : choisissez l’abonnement sous lequel vous souhaitez créer le compte Video Analyzer. 

   - **Groupe de ressources** : choisissez un groupe de ressources pour créer le compte Video Analyzer ou sélectionnez  **Créer nouveau**  pour créer un groupe de ressources. 

   - **Nom du compte Video Analyzer** : entrez un nom de votre compte Video Analyzer. Le nom doit se composer de lettres en minuscules ou de chiffres, sans espaces et comprendre entre 3 et 24 caractères. 

   - **Localisation** : choisissez une région pour déployer votre compte Video Analyzer (par exemple,  **USA Ouest 2**). 

   - **compte de stockage** : créez un compte de stockage. Nous vous recommandons de sélectionner un compte de stockage  [standard v2storage à usage général](../../storage/common/storage-account-overview.md#types-of-storage-accounts) . 

      > [!NOTE]
      > Si vous avez un compte de stockage qui se trouve dans la région dans laquelle vous déployez le compte Azure Video Analyzer, vous pouvez choisir d’utiliser ce compte de stockage au lieu d’en créer un nouveau. 

   - **Identité de l’utilisateur** : Créez et nommez une nouvelle identité managée affectée par l’utilisateur. 

1. En bas du formulaire, activez la case à **cocher** en regard de : « Je certifie avoir obtenu tous les droits et consentements nécessaires en vertu de la loi applicable pour traiter le contenu des médias et j’accepte d’être lié par tous les droits et consentements applicables. » 

1. Sélectionnez  **Vérifier + créer**  en bas du formulaire. 

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="Capture d’écran montrant comment créer le compte Video Analyzer dans le Portail Azure.":::

   > [!NOTE]
   > Ces ressources Azure seront ajoutées à votre groupe de ressources.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="Capture d’écran montrant les ressources qui seront également ajoutées à votre groupe de ressources Azure.":::

## <a name="deploy-the-azure-video-analyzer-edge-module"></a>Déployer le module Edge Azure Video Analyzer

### <a name="generate-azure-video-analyzer-edge-module-token"></a>Générer un jeton de module Edge Azure Video Analyzer

1. Accédez à votre compte Video Analyzer.

1. Sélectionnez **Modules en périphérie** sous le panneau **Edge**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="Capture d’écran montrant la création du module Edge de compte Azure Video Analyzer.":::

1. Sélectionnez **Ajouter des modules en périphérie**, entrez **avaedge** en tant que nom du nouveau module en périphérie, puis sélectionnez **Ajouter**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="Capture d’écran montrant comment ajouter des modules en périphérie.":::

1. L’écran **Copier le jeton d’approvisionnement** va s’afficher à droite. Sous **Propriétés souhaitées recommandées pour le déploiement de module IoT**, copiez l’extrait de code.  Vous en aurez besoin ultérieurement.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="Capture d’écran montrant le volet du jeton d’approvisionnement.":::

### <a name="deploy-the-azure-video-analyzer-edge-module"></a>Déployer le module Edge Azure Video Analyzer

1.  Accédez à votre Azure IoT Hub.

1. Sous **Gestion automatique des appareils**, choisissez **Sélectionner IoT Edge**.

1. Sélectionnez la valeur d’ID de votre appareil IoT Edge.

1. Sélectionnez Définir des modules.

1. Sélectionnez **Ajouter**, puis **Place de marché de module IoT Edge** dans le menu déroulant.

1. Entrez Azure Video Analyzer dans le champ de recherche.

1. Sélectionnez Azure Video Analyzer.

1. Dans le **volet Définir les modules**, sélectionnez **AzureVideoAnalyzerEdge**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="Capture d’écran montrant IoT Edge qui ajoute le module Azure Video Analyzer.":::

1. Dans le champ Nom du module IoT Edge, entrez **avaedge**.
1. Sélectionnez **Variables d’environnement**. 

   > [!NOTE]
   > Si votre déploiement Live Video Analytics utilisait un groupe et un utilisateur local trouvés à l’étape 6 de la section « Créer une sauvegarde des paramètres actuels », utilisez ces valeurs au lieu de LOCAL_USER_ID, LOCAL_GROUP_ID à l’étape suivante.

1. Dans le champ de nom, entrez **LOCAL_USER_ID** et dans le champ de valeur, entrez **1010**.  Dans le champ de nom suivant, entrez **LOCAL_GROUP_ID** et dans le champ de valeur, entrez **1010**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="Capture d’écran montrant les variables d’environnement d’Azure Video Analyzer.":::

1. Sélectionnez les **Opérations de création de conteneur**, puis collez les éléments suivants :

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

1. Sélectionnez  **Paramètres du jumeau de module**  et collez l’extrait de code que vous avez copié précédemment dans la page  **Copier le jeton d’approvisionnement** dans le compte Video Analyzer. 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>` est un espace réservé pour votre jeton d’approvisionnement capturé précédemment dans la section Générer le jeton du module Edge Azure Video Analyzer, étape 4.

1. Sélectionnez **Mettre à jour**.

1. Sélectionnez  **Itinéraires**.
1. Sous  **NAME**, entrez  **AVAToHub**. Sous  **VALUE**, entrez  __FROM /messages/modules/avaedge/outputs/* INTO $upstream__. 
1. Supprimez l’itinéraire **LVAToHub** en sélectionnant l’icône de la corbeille à droite de l’itinéraire.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="Capture d’écran montrant comment supprimer l’itinéraire IoT Edge Live Video Analytics.":::

1. Sous **Modules**, localisez votre module *lvaEdge* et supprimez-le en sélectionnant l’icône Corbeille à droite.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="Capture d’écran montrant comment supprimer le module IoT Edge Live Video Analytics.":::

1. Sélectionnez  **Vérifier + créer**, puis sélectionnez  **Créer**  pour déployer votre module en périphérie  **avaedge** . 

### <a name="verify-your-deployment"></a>Vérifier votre déploiement

Dans la page des détails de l’appareil, vérifiez que le module  **avaedge**   est répertorié à la fois en tant que  **Spécifié dans le déploiement**  et  **Signalé par l’appareil**. 

Le démarrage du module sur l’appareil et son signalement à IoT Hub peuvent prendre quelques instants. Actualisez la page pour afficher un état mis à jour. Le code d’état  **200 –OK**  signifie que le  [runtime IoT Edge](../../iot-edge/iot-edge-runtime.md)  est sain et fonctionne correctement. 

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="Capture d’écran qui montre le runtime IoT Edge signale l’état 200.":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="La capture d’écran montrant le runtime IoT Edge signale que tous les modules sont en cours d’exécution.":::

## <a name="convert-media-graph-topologies-to-azure-video-analyzer-pipelines"></a>Convertir des topologies de graphiques multimédias en pipelines Azure Video Analyzer

Il existe quelques différences entre les topologies de graphiques multimédias utilisées par Live Video Analytics et les topologies de pipeline utilisées par Azure Video Analyzer.  Utilisez le tableau suivant pour comparer les topologies Live Video Analytics et Azure Video Analyzer.

| Scénario                                                     | Graphique multimédia (Live Video Analytics)                                            | Pipeline (Azure Video Analyzer)                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Enregistrer un objet d’intérêt à l’aide de plusieurs modèles IA         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| Audio et vidéo                                              | [audio-video]()                                               | [audio-video]()                                               |
| enregistrement vidéo en continu                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| Enregistrement et inférence vidéo en continu à l’aide de l’extension gRPC | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| Enregistrement et inférence vidéo en continu à l’aide de l’extension HTTP | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| Enregistrement vidéo continu avec détection de mouvement             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| Enregistrement vidéo basé sur les événements sur [vidéo/ressources Azure Video Analyzer] basé sur les événements de l’IA externe | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sinkhttps://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| Enregistrement vidéo basé sur les événements sur [vidéo/ressources Azure Video Analyzer] basé sur les événements de l’IA externe | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| Enregistrement vidéo basé sur les événements sur [ressources/récepteur vidéo] basé sur des objets spécifiques qui sont détectés par le moteur d’inférence externe | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| Enregistrement vidéo dans des fichiers basé sur des événements en fonction de messages envoyés via IoT Edge Hub | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| Vidéo basée sur des événements enregistrée dans des fichiers locaux basés sur des événements de mouvement | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| Enregistrement vidéo basé sur des événements pour [ressources/récepteur vidéo] et des fichiers locaux basés sur des événements de mouvement | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| Analyse de la vidéo en direct à l’aide de l’extension gRPC pour envoyer des images au module OpenVINO(TM) DL Streamer - Edge AI Extension à partir d’Intel | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| Analyse de la vidéo en direct à l’aide de l’extension HTTP pour envoyer des images à un moteur d’inférence externe. | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| Analyse de la vidéo en direct à l’aide de l’extension HTTP pour envoyer des images au module OpenVINO™ Model Server – AI Extension d’Intel | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| Analyse de flux vidéo en direct avec le service Vision par ordinateur pour l’analyse spatiale | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> Vous pouvez rechercher et comparer à la fois les [topologies de graphiques multimédias Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies) et les [topologies de pipeline Azure Video Analyzer](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies) dans notre section d’exemples sur GitHub. En examinant ces exemples, vous serez en mesure de mettre à jour vos topologies de graphiques multimédias existantes vers des topologies de pipeline Azure Video Analyzer.

### <a name="set-and-activate-the-pipeline-topology"></a>Définir et activer la topologie de pipeline

Une fois que vous avez converti la topologie de graphiques multimédias (Live Video Analytics) en topologie de pipeline (Azure Video Analyzer), vous devez définir le pipeline sur le module *avaedge*.

1. Pour définir votre topologie, sélectionnez le module Edge Azure Video Analyzer sous l’appareil IoT Edge.  Dans l’exemple ci-dessous, le nom du conteneur est *avaedge*.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="Capture d’écran montrant le module IoT Edge Live Video Analytics.":::

1. Sélectionnez **Méthode directe**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Capture d’écran montrant les appels de méthode directe pour Live Video Analytics.":::

1. Entrez **pipelineTopologySet** dans **Nom de la méthode** et entrez le JSON du pipeline converti dans la charge utile.

1. Sélectionnez **Appeler la méthode**.

      Dans le champ de retour, vous devez voir « Status » : 201 suivi de votre topologie.  Cet état indique qu’une topologie a été créée.

1. Ensuite, nous devons créer le pipeline en direct à l’aide de la topologie.  Utilisez les étapes 1-3, toutefois, dans le champ Nom de la méthode, entrez **livePipelineSet** et dans le champ de charge utile, entrez :

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > Remplacez les valeurs `<live pipeline name>`, `<Name of the set topology>`, `<RTSP camera string>` et `<Camera User Password>` par celles de votre environnement. 

1. Sélectionnez **Appeler la méthode**.
   
      Dans le champ de retour, vous devez voir « Status » : 201 suivi de votre pipeline en direct.  Cet état indique qu’une nouvelle topologie a été créée.

2. Enfin, activez votre pipeline en direct en suivant les étapes 1 à 3. Toutefois, dans **Nom de la méthode**, entrez **livePipelineActivate**, et dans le champ de charge utile, entrez :

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > Remplacez `<live pipeline name>` par le nom du pipeline en direct défini ci-dessus.

4. Sélectionnez **Appeler la méthode**.
   
   Dans le champ de retour, vous devez voir « Status » : 200.  Cet état indique qu’une nouvelle topologie a été activée.

## <a name="direct-methods"></a>Méthodes directes

Live Video Analytics et Azure Video Analyzer utilisent tous deux des appels de méthode directe.  Dans le cadre de la migration, vous remarquerez également que les méthodes directes ont été mises à jour et que vous devez passer en revue les workflows susceptibles de les utiliser dans les solutions Live Video Analytics.  Le tableau suivant présente les différences entre les [appels de méthode directe Live Video Analytics](direct-methods.md) et les [appels de méthode directe Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md).

| Analyses vidéo en direct | Azure Video Analyzer |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## <a name="miscellaneous-changes"></a>Modifications diverses 

- Appsettings.json : mettez à jour le fichier appsettings.json de façon à ce que moduleId fasse référence à votre module. Autrement dit, affectez-lui la valeur *avaeEdge*. 

- Il y a une modification mineure du [contrat gRPC](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc). Bien que votre extension gRPC existante doive continuer à fonctionner, il est recommandé de mettre à jour les fichiers de prototype. La modification est une nouvelle ligne dans inferencing.proto : string sequence_id=14.

## <a name="next-steps"></a>Étapes suivantes

- Essayez le  [guide de démarrage rapide pour enregistrer des vidéos dans le cloud quand des mouvements sont détectés](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md). 
- Essayez le  [guide de démarrage rapide pour analyser les vidéo en direct](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md). 
- Passez en revue la [Terminologie d’Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/terminology.md). 
- Passez en revue le [Pipeline Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/pipeline.md). 