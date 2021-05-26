---
title: Résoudre les problèmes liés à Live Video Analytics sur IoT Edge - Azure
description: Cet article traite des étapes de dépannage pour les problèmes liés à Live Video Analytics sur IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: f806a700fa9f1788e2435a43d8fdcb19fa8134ce
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372651"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Résoudre les problèmes liés à Live Video Analytics sur IoT Edge

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

Cet article traite des étapes de résolution des problèmes liés à Live Video Analytics (LVA) sur Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Résoudre les problèmes de déploiement

### <a name="diagnostics"></a>Diagnostics

Dans le cadre de votre déploiement de Live Video Analytics, vous configurez des ressources Azure telles qu’IoT Hub et des appareils IoT Edge. Pour commencer à diagnostiquer les problèmes, assurez-vous toujours que l’appareil Edge est correctement configuré en suivant les instructions ci-dessous :

1. [Exécutez la commande `check`](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Vérifiez votre version d’IoT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Vérifiez l’état du gestionnaire de sécurité IoT Edge et de ses journaux](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Affichez les messages acheminés via le hub IoT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Redémarrez les conteneurs](../../iot-edge/troubleshoot.md#restart-containers).
1. [Vérifiez vos règles de configuration de pare-feu et de port](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problèmes liés au prédéploiement

Si l’infrastructure Edge est correcte, vous pouvez rechercher des problèmes dans le fichier du manifeste de déploiement. Pour déployer le module Live Video Analytics sur IoT Edge sur l’appareil IoT Edge avec d’autres modules IoT, utilisez un manifeste de déploiement qui contient le hub Edge, l’agent IoT Edge et d’autres modules avec leurs propriétés. Vous pouvez utiliser la commande suivante pour déployer le fichier manifeste :

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Si le code JSON n’est pas bien formé, vous pouvez recevoir l’erreur suivante :   
&nbsp;&nbsp;&nbsp; **Impossible d’analyser le code JSON du fichier : « <deployment manifest.json> » pour l’argument « content ». Exception : « Données supplémentaires : ligne 101 colonne 1 (caractère 5325) »**

Si vous rencontrez cette erreur, nous vous recommandons de vérifier dans le fichier JSON s’il manque des crochets ou si la structure du fichier présente d’autres problèmes. Pour valider la structure des fichiers, vous pouvez utiliser un client tel que [Notepad++ avec le plug-in JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou un outil en ligne tel que [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Pendant le déploiement : diagnostiquer à l’aide des méthodes directes du graphe multimédia 

Une fois que le module Live Video Analytics sur IoT Edge est correctement déployé sur l’appareil IoT Edge, vous pouvez créer et exécuter le graphe multimédia en appelant les [méthodes directes](direct-methods.md).  
>[!NOTE]
>  Les appels de méthode directe ne doivent être effectués que sur le module **`lvaEdge`** .

Vous pouvez utiliser le Portail Azure pour exécuter un diagnostic du graphe multimédia selon les méthodes directes :

1. Dans le portail Azure, accédez au hub IoT connecté à votre appareil IoT Edge.

1. Recherchez **Gestion automatique des appareils**, puis sélectionnez **IoT Edge**.  

1. Dans la liste des appareils Edge, sélectionnez l’appareil que vous souhaitez diagnostiquer.  
         
    ![Capture d’écran du portail Azure affichant une liste d’appareils Edge](./media/troubleshoot-how-to/lva-sample-device.png)


1. Vérifiez si le code de réponse est *200-OK*. Les autres codes de réponse pour le [runtime IoT Edge](../../iot-edge/iot-edge-runtime.md) sont les suivants :
    * 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
    * 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
    * 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
    * 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
    * 500 - Une erreur s’est produite dans le runtime IoT Edge.

    > [!TIP]
    > Si vous rencontrez des problèmes lors de l’exécution de modules Azure IoT Edge dans votre environnement, utilisez l’article **[Étapes de diagnostic standard d’Azure IoT Edge](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** qui vous guidera pour la résolution des problèmes et les diagnostics.
### <a name="post-deployment-direct-method-error-code"></a>Après le déploiement : Code d’erreur de méthode directe
1. Si vous recevez un code de statut `501 code`, vérifiez que le nom de la méthode directe est exact. Si le nom de la méthode et la charge utile de la requête sont exacts, les résultats devraient contenir le code de réussite = 200. 
1. Si la charge utile de demande est inexacte, vous obtenez un code de statut `400 code` et une charge utile de réponse indiquant un code d’erreur et un message utiles pour diagnostiquer le problème de l’appel de méthode directe.
    * La vérification des propriétés rapportées et souhaitées peut vous aider à comprendre si les propriétés du module sont synchronisées avec le déploiement. Si ce n’est pas le cas, vous pouvez redémarrer votre appareil IoT Edge. 
    * Utilisez le guide [Méthodes directes](direct-methods.md) pour appeler un certain nombre de méthodes, notamment les plus simples comme GraphTopologyList. Ce guide précise également les charges utiles de demande et de réponse attendues ainsi que les codes d’erreur. Lorsque les méthodes directes simples ont réussi, vous pouvez être certain que le module IoT Edge Live Video Analytics fonctionne correctement.
        
       ![Capture d’écran du volet « Méthode directe » pour le module IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Si les colonnes **Spécifié lors du déploiement** et **Signalé par l’appareil** indiquent *Oui*, alors vous pouvez appeler des méthodes directes sur le module Live Video Analytics sur IoT Edge. Sélectionnez le module pour accéder à une page dans laquelle vous pouvez vérifier les propriétés souhaitées et rapportées et appeler des méthodes directes. Gardez à l’esprit les points suivants : 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Après le déploiement : diagnostiquer les journaux à la recherche des problèmes survenus lors de l’exécution 

Les journaux des conteneurs de votre module IoT Edge doivent contenir des informations de diagnostic pour vous aider à déboguer les problèmes survenus pendant l’exécution du module. Vous pouvez [vérifier si les journaux des conteneurs indiquent des problèmes](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) et auto-diagnostiquer le problème. 

Si vous avez exécuté toutes les vérifications précédentes et que vous rencontrez toujours des problèmes, rassemblez les journaux à partir de l’appareil IoT Edge [avec la commande `support bundle`](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) pour une analyse plus approfondie par l’équipe Azure. Vous pouvez [nous contacter](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pour obtenir de l’aide et envoyer les journaux collectés.

## <a name="common-error-resolutions"></a>Résolutions des erreurs courantes

Live Video Analytics est déployé en tant que module IoT Edge sur l’appareil IoT Edge et fonctionne de manière collaborative avec les modules du hub et de l’agent IoT Edge. Certaines des erreurs courantes que vous pouvez rencontrer lors du déploiement de Live Video Analytics sont dues à des problèmes liés à l’infrastructure IoT sous-jacente. Les erreurs comprennent :

* [L’agent IoT Edge s’arrête après environ une minute](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [L’agent IoT Edge ne peut pas accéder à l’image d’un module (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Le module de l’agent IoT Edge rapporte le message « fichier config vide » et aucun module ne démarre sur l’appareil](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Le hub IoT Edge ne parvient pas à démarrer](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Le démon de sécurité IoT Edge échoue en raison d’un nom d’hôte non valide](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Le module Live Video Analytics ou tout autre module IoT Edge personnalisé ne parvient pas à envoyer un message au hub IoT Edge avec l’erreur 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Le module IoT Edge est correctement déployé, puis disparaît de l’appareil](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Si vous rencontrez des problèmes lors de l’exécution de modules Azure IoT Edge dans votre environnement, utilisez l’article **[Étapes de diagnostic standard d’Azure IoT Edge](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** qui vous guidera pour la résolution des problèmes et les diagnostics.

Il se peut également que vous rencontriez des problèmes lors de l’exécution du **[script de configuration des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)** . Voici quelques problèmes courants :

* Si vous utilisez un abonnement pour lequel vous ne disposez pas de privilèges de propriétaire, le script échoue avec une erreur **ForbiddenError** ou **AuthorizationFailed**.
    * Pour résoudre ce problème, veillez à disposer des privilèges **PROPRIÉTAIRE** sur l’abonnement que vous prévoyez d’utiliser. Si vous ne pouvez pas le faire par vous-même, contactez l’administrateur de l’abonnement pour qu’il vous accorde les privilèges nécessaires.
* **Le déploiement du modèle a échoué à cause de la violation de stratégie.**
    * Pour résoudre ce problème, contactez votre administrateur informatique pour faire en sorte que le ou les appels à créer une machine virtuelle contournent le blocage de l’authentification SSH. Ce ne sera pas nécessaire, car nous utilisons un réseau Bastion sécurisé qui a besoin d’un nom d’utilisateur et d’un mot de passe pour communiquer avec les ressources Azure. Ces informations d’identification sont stockées dans le fichier **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** dans Cloud Shell, une fois la machine virtuelle créée, déployée et attachée au hub IoT.
* Le script de configuration ne parvient pas à créer un principal de service ou des ressources Azure.
    * Pour résoudre ce problème, vérifiez que ni votre abonnement ni le locataire Azure n’ont atteint leurs limites de service maximales. Pour plus d’informations, consultez [Limites et restrictions du service Azure AD](../../active-directory/enterprise-users/directory-service-limits-restrictions.md) et [Abonnement Azure et limites, quotas et contraintes du service](../../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!TIP]
> Si vous rencontrez des problèmes supplémentaires pour lesquels vous avez besoin d’aide, **[collectez des journaux et soumettez un ticket de support](#collect-logs-for-submitting-a-support-ticket)** . Vous pouvez également nous contacter en nous envoyant un e-mail à l’adresse **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** .
### <a name="live-video-analytics-working-with-external-modules"></a>Live Video Analytics avec des modules externes

Live Video Analytics avec les processeurs d’extension de graphe multimédia peut étendre le graphe multimédia pour envoyer et recevoir des données à partir d’autres modules IoT Edge à l’aide de protocoles HTTP ou gRPC. À titre [d’exemple spécifique](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension), ce graphe multimédia peut envoyer des trames vidéo sous forme d’images à un module d’inférence externe comme Yolo v3 et recevoir des résultats analytiques JSON suivant le protocole HTTP. Dans ce type de topologie, la destination des événements est principalement le hub IoT. Dans les cas où vous ne voyez pas les événements d’inférence sur le hub, vérifiez les éléments suivants :

* Vérifiez si le hub sur lequel est publié le graphe multimédia est le même que celui que vous examinez. Lorsque vous créez plusieurs déploiements, vous pouvez vous retrouver avec plusieurs hubs et vous risquez d’inspecter le mauvais hub d’événement par erreur.
* Sur le Portail Azure, vérifiez si le module externe est déployé et s’il fonctionne. Dans cette image d’exemple, rtspsim, yolov3, tinyyolov3 et logAnalyticsAgent sont des modules IoT Edge qui s’exécutent à l’extérieur du module lvaEdge.

    [ ![Capture d’écran de l’état d’exécution des modules dans Azure IoT Hub.](./media/troubleshoot-how-to/iot-hub-azure.png) ](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Vérifiez que vous envoyez des événements au point de terminaison d’URL approprié. Le conteneur d’IA externe expose une URL et un port par lesquels il reçoit et renvoie les données des requêtes POST. Cette URL est spécifiée en tant que propriété `endpoint: url` pour le processeur d’extension HTTP. Comme indiqué dans l’[URL de topologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json), le point de terminaison est défini sur le paramètre d’URL d’inférence. Assurez-vous que la valeur par défaut du paramètre ou la valeur transmise est exacte. Vous pouvez effectuer un test pour déterminer s’il fonctionne à l’aide de l’URL du client (cURL).  

    À titre d’exemple, voici un conteneur Yolo v3 qui s’exécute sur l’ordinateur local avec l’adresse IP 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Résultat retourné :

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Utilisez la **[commande Docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)** pour trouver l’adresse IP de l’ordinateur.
    
* Si vous exécutez une ou plusieurs instances d’un graphe qui utilise le processeur d’extension de graphe multimédia, utilisez le champ `samplingOptions` pour gérer la fréquence d’images par seconde (FPS) du flux vidéo. 

   * Dans certains cas, lorsque le processeur ou la mémoire de l’ordinateur Edge est fortement sollicité, vous pouvez perdre certains événements d’inférence. Pour résoudre ce problème, définissez une valeur faible pour la propriété `maximumSamplesPerSecond` dans le champ `samplingOptions`. Vous pouvez choisir la valeur 0,5 ("maximumSamplesPerSecond": "0.5") sur chacune des instances du graphe, puis réexécuter l’instance pour rechercher des événements d’inférence sur le hub.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Plusieurs méthodes directes en parallèle – échec au terme du délai d’expiration 

Live Video Analytics sur IoT Edge fournit un modèle de programmation basé sur une méthode directe qui vous permet de configurer plusieurs topologies et plusieurs instances de graphe. Dans le cadre de la configuration de la topologie et du graphe, vous allez procéder à plusieurs appels de méthode directe sur le module IoT Edge. Si vous invoquez ces multiples appels de méthode en parallèle, en particulier ceux qui démarrent et arrêtent les graphes,vous pourriez connaître un échec au terme du délai d’expiration, comme ci-dessous : 

La méthode d’Assembly Initialization Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync a levé l’exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException : Microsoft.Azure.Devices.Common.Exceptions.IotHubException :<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Nous vous recommandons de *pas* appeler des méthodes directes en parallèle. Appelez-les de manière séquentielle (c’est-à-dire, effectuez un appel de méthode directe seulement après la fin du précédent).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Collecter les journaux pour l’envoi d’un ticket de support

Lorsque les étapes de résolution des problèmes auto-guidée ne résolvent pas votre problème, accédez au portail Azure et [ouvrez un ticket de support](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Les journaux peuvent contenir des informations d’identification personnelle telles que votre adresse IP. Toutes les copies locales des journaux seront supprimées dès que nous aurons terminé leur examen et fermé le ticket de support.  

Pour collecter les journaux qui doivent être ajoutés au ticket, suivez les instructions ci-dessous dans l’ordre et chargez les fichiers journaux dans le volet **Détails** de la demande de support.  
1. [Configurez le module Live Video Analytics pour collecter des journaux détaillés](#configure-live-video-analytics-module-to-collect-verbose-logs).
1. [Activer les journaux de débogage](#live-video-analytics-debug-logs).
1. Reproduisez le problème
1. Connectez-vous à la machine virtuelle sur la page **IoT Hub** du portail.
    1. Compressez tous les fichiers dans le dossier *debugLogs*.

       > [!NOTE]
       > Ces fichiers journaux ne sont pas destinés à l’auto-diagnostic. Ils permettent aux ingénieurs Azure d’analyser vos problèmes.

       * Dans la commande suivante, veillez à remplacer **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** par l’emplacement des journaux de débogage sur l’appareil Edge que vous avez configuré à **l’Étape 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Joignez le fichier *debugLogs.zip* au ticket de support.
1. Exécutez la [commande de bundle de support](#use-the-support-bundle-command), collectez les journaux et joignez-les au ticket de support.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Configuration du module Live Video Analytics pour collecter des journaux détaillés
Configurez votre module Live Video Analytics pour collecter des journaux détaillés en définissant `logLevel` et `logCategories` comme suit :
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Il existe deux endroits pour le faire :
* Sur le **Portail Azure**, mettez à jour les propriétés Jumeau d’identité du module Live Video Analytics   [ ![Propriétés Jumeau d’identité de module.](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Dans votre fichier **manifeste de déploiement**, vous pouvez également ajouter ces entrées dans le nœud Propriétés du module Live Video Analytics.

### <a name="use-the-support-bundle-command"></a>Utiliser la commande support-bundle

Lorsque vous devez récupérer des journaux d'activité depuis un périphérique IoT Edge, la façon la plus simple est d’utiliser la commande `support-bundle`. Cette commande récupère :

- Les journaux de module
- Les journaux du gestionnaire de sécurité IoT Edge et du moteur de conteneur
- La sortie JSON du contrôle IoT Edge
- Des informations de débogage utiles

1. Exécutez la commande `support-bundle` avec l’indicateur *--since* pour spécifier la période que vos journaux doivent couvrir. Par exemple, « 2 h » vous permettra de récupérer les journaux des deux dernières heures. Vous pouvez modifier la valeur de cet indicateur pour inclure les journaux de différentes périodes.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Cette commande crée un fichier nommé *support_bundle.zip* dans le répertoire où vous avez exécuté la commande. 
   
1. Joignez le fichier *support_bundle.zip* au ticket de support.

### <a name="live-video-analytics-debug-logs"></a>Journaux de débogage de Live Video Analytics

Pour configurer le module Live Video Analytics sur IoT Edge afin de générer des journaux de débogage, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre hub IoT.
1. Dans le volet gauche, sélectionnez **IoT Edge**.
1. Dans la liste des appareils, sélectionnez l’ID de l’appareil cible.
1. En haut du volet, sélectionnez **Définir les modules**.

   ![Capture d’écran du bouton « Définir les modules » dans le portail Azure.](media/troubleshoot-how-to/set-modules.png)

1. Dans la section **Modules IoT Edge**, recherchez et sélectionnez **lvaEdge**.
1. Sélectionnez **Options de création de conteneur**.
1. Dans la section **Liens**, ajoutez la commande suivante  :

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Cette commande lie les dossiers de journaux entre l’appareil Edge et le conteneur. Si vous souhaitez collecter les journaux dans un emplacement différent, utilisez la commande suivante en remplaçant **$LOG_LOCATION_ON_EDGE_DEVICE** par l’emplacement que vous souhaitez utiliser : `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Sélectionnez **Update**.
1. Sélectionnez **Vérifier + créer**. Un message de validation réussie s’affiche sous une bannière verte.
1. Sélectionnez **Create** (Créer).
1. Mettez à jour **Jumeau d’identité de module** pour pointer vers le paramètre DebugLogsDirectory, qui pointe vers le répertoire dans lequel les journaux sont collectés :

    a. Sous la table **Modules**, sélectionnez **lvaEdge**.  
    b. En haut du volet, sélectionnez **Jumeau d’identité de module**. Un volet modifiable s’ouvre.  
    c. Sous **Clé souhaitée**, ajoutez la paire clé-valeur suivante :  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Cette commande lie les dossiers de journaux entre l’appareil Edge et le conteneur. Si vous souhaitez collecter les journaux à un autre emplacement sur l’appareil :
    > 1. Créez une liaison pour l’emplacement du journal de débogage dans la section **Liaisons**, en remplaçant **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** et **$DEBUG_LOG_LOCATION** par l’emplacement de votre choix : `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Utilisez la commande suivante, en remplaçant **$DEBUG_LOG_LOCATION** par l’emplacement utilisé à l’étape précédente :  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Sélectionnez **Enregistrer**.


1. Vous pouvez arrêter la collecte des journaux en définissant la valeur dans **Jumeau d’identité de module** sur *Null*. Retournez sur la page **Module Identity Twin** et mettez à jour le paramètre suivant comme suit :

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Meilleures pratiques de journalisation

L’article [Surveillance et journalisation](monitoring-logging.md) aide à comprendre la taxonomie et à générer des journaux permettant de déboguer les problèmes liés à LVA. 

Étant donné que l’implémentation du serveur gRPC diffère selon les langages, il n’existe pas de méthode standard pour ajouter la journalisation dans le serveur.  

Par exemple, si vous générez un serveur gRPC à l’aide de .NET Core, le service gRPC ajoute les journaux sous la catégorie **gRPC**. Pour activer les journaux détaillés à partir de gRPC, configurez les préfixes gRPC au niveau de débogage dans votre fichier appsettings.json en ajoutant les éléments suivants à la sous-section LogLevel de Logging : 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Vous pouvez également configurer cet aspect dans le fichier Startup.cs avec ConfigureLogging : 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

L’article [Journalisation et diagnostics dans gRPC sur .NET](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) donne des conseils pour collecter certains journaux de diagnostic à partir d’un serveur gRPC. 

### <a name="a-failed-grpc-connection"></a>Échec d’une connexion gRPC 

Si un graphe est actif et diffusé en continu à partir d’une caméra, la connexion est maintenue par Live Video Analytics. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Surveillance et équilibrage de la charge des ressources de processeur et de GPU lorsque ces dernières deviennent des goulots d’étranglement

Live Video Analytics ne surveille pas les ressources matérielles. Il n’en fournit pas non plus d’analyse. Les développeurs doivent utiliser les solutions de monitoring des fabricants de matériel. Toutefois, si vous utilisez des conteneurs Kubernetes, vous pouvez surveiller l’appareil à l’aide du [tableau de bord Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Les documents gRPC dans .NET Core partagent également des informations précieuses sur les [Meilleures pratiques en matière de niveau de performance](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) et [l’Équilibrage de charge](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Résolution des problèmes d’un serveur d’inférence qui ne reçoit aucune trame et donne une erreur de protocole « inconnu » 

Il existe plusieurs moyens d’obtenir des informations sur le problème.  

* Incluez la catégorie de journal **ediaPipeline** dans les propriétés souhaitées du module Live Video Analytics et vérifiez que le niveau de consignation est défini sur `Information`.  
* Pour tester la connectivité réseau, vous pouvez exécuter la commande suivante à partir de l’appareil Edge. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Si la commande génère une courte chaîne de texte brouillé, c’est le signe que Telnet a pu ouvrir une connexion à votre serveur d’inférence et ouvrir un canal gRPC binaire. Dans le cas contraire, Telnet signale une erreur réseau. 
* Dans votre serveur d’inférence, vous pouvez activer une journalisation supplémentaire dans la bibliothèque gRPC. Elle vous fournira des informations supplémentaires sur le canal gRPC proprement dit. La procédure varie en fonction du langage. Voici des instructions pour [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Prélèvement d’images supplémentaires dans la mémoire tampon de gRPC sans renvoyer le résultat de la première mémoire tampon

Dans le cadre du contrat de transfert de données gRPC, tous les messages que Live Video Analytics envoie au serveur d’inférence gRPC doivent faire l’objet d’un accusé de réception. Tout défaut d’accusé de réception d’une trame d’image rompt le contrat de données, ce qui peut aboutir à des situations indésirables.  

Pour utiliser votre serveur gRPC avec Live Video Analytics, vous pouvez recourir à la mémoire partagée pour des performances optimales. Vous devez pour cela vous servir des fonctionnalités de mémoire partagée Linux exposées par le langage/l’environnement de programmation. 

1. Ouvrez le descripteur de la mémoire partagée Linux.
1. À la réception d’une trame, accédez au décalage d’adresse dans la mémoire partagée.
1. Accusez réception de l’achèvement du traitement de la trame afin que sa mémoire puisse être récupérée par Live Video Analytics.

   > [!NOTE]
   > Si vous retardez l’envoi de l’accusé de réception de la trame à Live Video Analytics pendant une longue période, la mémoire partagée risque de saturer, ce qui peut entraîner des suppressions de données.
1. Stockez chaque trame dans une structure de données de votre choix (liste, tableau, etc.) sur le serveur d’inférence.
1. Vous pouvez exécuter votre logique de traitement dès vous disposez du nombre souhaité de trames d’image.
1. Retournez le résultat d’inférence à Live Video Analytics quand il est prêt.

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Enregistrement vidéo basé sur les événements dans le Cloud et lecture à partir du Cloud](event-based-video-recording-tutorial.md)