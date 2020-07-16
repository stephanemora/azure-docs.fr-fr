---
title: Résoudre les problèmes liés à Live Video Analytics sur IoT Edge - Azure
description: Cet article traite des étapes de dépannage pour les problèmes liés à Live Video Analytics sur IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045572"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Résoudre les problèmes liés à Live Video Analytics sur IoT Edge

Cet article traite des étapes de dépannage pour les problèmes liés à Live Video Analytics sur IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Résoudre les problèmes de déploiement

### <a name="diagnostics"></a>Diagnostics

Dans le cadre du déploiement de Live Video Analytics, vous configurez des ressources Azure telles que IoT Hub et l’appareil IoT Edge. Une première étape pour diagnostiquer des problèmes consiste toujours à vérifier que la périphérie est correctement configurée en suivant les instructions ci-dessous :

1. [Exécuter la commande « vérification »](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Vérifier votre version d’IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Vérifier l’état des journaux et du gestionnaire de sécurité IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Afficher les messages acheminés via hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Redémarrer les conteneurs](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Vérifier vos règles de configuration de pare-feu et de port](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Problèmes liés au prédéploiement

Si l’infrastructure de la périphérie est correcte, vous pouvez rechercher des problèmes liés au fichier manifeste de déploiement. Pour déployer en Live Video Analytics sur le module IoT Edge sur le périphérique et tout autre module IoT en même temps, vous devez utiliser un manifeste de déploiement qui contient le hub Edge, l’agent Edge et d’autres modules avec leurs propriétés. Si le fichier JSON n’est pas bien formé, vous pouvez recevoir l’erreur ci-dessous : 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Impossible d’analyser JSON à partir du fichier : « <deployment manifest.json> » pour l’argument « content » avec l’exception : « Données supplémentaires : ligne 101 colonne 1 (char 5325) »

Si vous rencontrez cette erreur, nous vous recommandons de vérifier s’il manque des crochets dans le fichier JSON ou de rechercher d’autres problèmes liés à la structure du fichier. Vous pouvez utiliser un client tel que [Notepad++ avec le plug-in JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou un outil en ligne comme https://jsonformatter.curiousconcept.com/ afin de valider la structure du fichier.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Déploiement : faire un diagnostic à l’aide des méthodes directes du graphique média 

Une fois que le module Live Video Analytics sur IoT Edge est déployé correctement sur le périphérique, vous pouvez créer et exécuter le graphique média en appelant les [méthodes directes](direct-methods.md). Vous pouvez utiliser le portail pour exécuter le diagnostic du graphique média via les méthodes directes :

1. Depuis le portail, accédez au hub IoT connecté à votre périphérique.
    1. Une fois dans le panneau IoT Hub, recherchez Gestion automatique des appareils->IoT Edge.
    1. Cliquez sur IoT Edge pour afficher la liste des périphériques. Sélectionnez l’appareil à diagnostiquer.
         
        ![Périphériques](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Vérifiez si le code de réponse est 200-OK. Il existe différents autres codes de réponse pour le [Runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) tels que :
        1. 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
        1. 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
        1. 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
        1. 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
        1. 500 - Une erreur s’est produite dans le runtime IoT Edge.
    1. Cliquez sur l’appareil affiche également la liste des modules IoT Edge attendus qui ont été déployés, ainsi que leurs états
    1. Si les colonnes « spécifié lors du déploiement » et « signalé par l’appareil » indiquent « Oui », alors vous pouvez faire appel aux méthodes directes sur Live Video Analytics sur le module IoT Edge. Cliquez sur le module et vous serez redirigé vers un écran sur lequel vous pourrez vérifier les propriétés désirées et signalées et invoquer les méthodes directes. 
        1. La vérification des propriétés signalées et désirées permet de comprendre si les propriétés du module ont été synchronisées avec le déploiement. Si ce n’est pas le cas, vous pouvez redémarrer votre périphérique 
        1. Utilisez le guide [Méthodes directes](direct-methods.md) vous appeler un certain nombre de méthodes, notamment les plus simples comme GraphTopologyList. Ce guide précise également les charges utiles de demande et de réponse attendues ainsi que les codes d’erreur. Lorsque les méthodes directes simples ont réussi, vous pouvez vérifier que le module Edge Live Video Analytics fonctionne correctement.
        
        ![Méthode directe](./media/troubleshoot-how-to/direct-method.png) 
1. Si vous recevez le code d’état 501, vérifiez que le nom de la méthode direct est exact. Si le nom de la méthode et la charge utile de demande sont exacts, les résultats devraient contenir le code de réussite = 200. Dans le cas où la charge utile de demande serait inexacte, vous obtiendrez l’état = 400 et la charge utile de réponse indiquera un code d’erreur et un message qui vous aideront à diagnostiquer le problème avec votre appel de méthode directe. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Après le déploiement : diagnostiquer les journaux pour les problèmes survenus lors de l’exécution 

Les journaux de conteneur pour notre module Edge doivent contenir des informations<!--<todo:add link to diagnostics doc>--> de diagnostic qui aideront à résoudre les problèmes survenus lors du runtime du module. Vous pouvez [Rechercher des problèmes dans les journaux de conteneur](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) et faire un diagnostic personnel. Cependant, si vous avez effectué les vérifications ci-dessus et que vous êtes toujours confronté à des problèmes, collectez plutôt les journaux de l’appareil IoT Edge [ à l’aide de la commande « bundle de support »](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) qui pourront faire l’objet d’un examen plus approfondi par l’équipe Azure. Vous pouvez [nous contacter](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pour obtenir un support technique et envoyer les journaux collectés.

## <a name="common-error-resolutions"></a>Résolutions des erreurs courantes

Live Video Analytics est déployé en tant que module IoT Edge sur le périphérique et fonctionne de manière collaborative avec les modules agent et hub d’IoT Edge. Vous rencontrerez certaines erreurs courantes lors du déploiement de Live Video Analytics dues à des problèmes liés à l’infrastructure IoT sous-jacente. Voici des exemples d’erreurs courantes du hub et de l’agent IoT Edge :

1. [L’agent IoT Edge s’arrête après environ une minute](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [L’agent IoT Edge ne peut pas accéder à l’image d’un module (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Le module Edge Agent rapporte le message « fichier config vide » et aucun module ne démarre sur l’appareil](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [Le hub IoT Edge ne parvient pas à démarrer](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [Le démon de sécurité IoT Edge échoue en raison d’un nom d’hôte non valide](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Live Video Analytics ou tout autre module IoT Edge personnalisé ne parvient pas à envoyer un message à Edge Hub avec l’erreur 404](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [Après son déploiement, le module IoT Edge n’est plus visible sur l’appareil](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Problèmes liés au script de configuration Edge

Dans notre documentation, nous fournissons un [script de configuration](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pour déployer les ressources Edge et Cloud et bien démarrer avec Live Video Analytics Edge. Dans cette section, nous avons capturé les erreurs que vous pourrez rencontrer avec le script et nous vous indiquons comment les résoudre.

Le script s’exécute partiellement et crée de nouvelles ressources mais échoue en indiquant le message suivant :

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Pour résoudre ce problème :

1. Exécutez la commande suivante :

    ```
    az --version
    ```
1. Assurez-vous que les extensions suivantes sont installées. Au moment de la rédaction de ce guide, les versions des extensions étaient les suivantes :

    | Extension | Version |
    |---|---|
    |azure-cli   |      2.5.1*|
    |command-modules-nspkg         |   2.0.3|
    |core  |    2.5.1*|
    |nspkg    | 3.0.4|
    |télémétrie| 1.0.4|
    |Extensions :    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Si l’une des extensions a un numéro de mise en production plus ancien que ceux cités ci-dessus, mettez-la à niveau vers la dernière version à l’aide de cette commande :

    ```
    az extension update --name <Extension name>
    ```

    Par exemple : `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Problèmes avec l’application exemple

Dans le cadre de notre mise en production, nous avons fourni des exemples de code .NET pour lancer notre communauté de développeurs. Dans cette section, nous avons capturé des erreurs que vous pouvez rencontrer lors de l’exécution de l’exemple de code et nous vous indiquons comment les résoudre.

1. Program.cs échoue avec l’erreur suivante sur l’appel de méthode directe :

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Assurez-vous d’avoir installé les [outils Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dans votre environnement Visual Studio Code et d’être connecté à votre configuration IoT Hub. (Ctrl + Maj + P, puis choisir la méthode Select IoT Hub (Sélectionner IoT Hub) pour vous connecter à votre abonnement et à IoT Hub)
1. Vérifiez si vous pouvez appeler une méthode directe sur le module Edge via Visual Studio Code (par exemple, appelez GraphToplogyList avec la charge utile suivante {« @apiVersion» : « 1.0 »}) et vous devez obtenir la réponse suivante. 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Si les étapes ci-dessus échouent, essayez ce qui suit :
    1. Rendez-vous sur l’invite de commandes sur votre appareil IoT Edge et tapez.
    
    ```
    sudo systemctl restart iotedge
    ```

    Cela redémarre le périphérique et tous les modules. Attendez quelques minutes et exécutez la commande suivante pour vérifier que les modules sont en cours d’exécution, avant d’essayer à nouveau d’utiliser la méthode directe.

    ```
    sudo iotedge list
    ```
    1. Si ce qui précède échoue également, essayez de redémarrer votre machine virtuelle ou votre ordinateur.
    1. Si rien ne fonctionne, exécutez la commande suivante pour obtenir un fichier ZIP contenant tous les [journaux pertinents](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) à associer à ce [ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Si vous recevez le code de réponse d’erreur 400, assurez-vous que la charge utile d’appel de votre méthode est bien formée conformément au guide des [Méthodes directes](direct-methods.md).
1. Si vous recevez le code d’état 200, cela signifie que votre hub fonctionne correctement et que le déploiement de votre module est correct et réactif. L’étape suivante consiste à vérifier si les configurations d’application sont exactes. La configuration de votre application se compose des champs suivants dans le fichier appsettings.json. Vérifiez soigneusement que deviceId et moduleId sont exacts. Un moyen simple pour vérifier cela consiste à utiliser la section d’extension Azure IoT Hub dans Visual Studio Code. Les valeurs contenues dans le fichier appsettings.json et la section IoT Hub doivent correspondre.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)

1. Enfin, assurez-vous d’avoir fourni la chaîne de connexion du hub IoT dans le fichier appsettings.json et non la chaîne de connexion de l’appareil IoT Hub, car leurs [formats](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) sont différents.

### <a name="live-video-analytics-working-with-external-modules"></a>Live Video Analytics avec des modules externes

Live Video Analytics via le processeur d’extension HTTP peut étendre le graphique média pour envoyer et recevoir des données à partir d’autres modules de IoT Edge sur HTTP à l’aide de REST.  Prenons comme [exemple spécifique](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) le graphique média qui peut envoyer des trames vidéo en tant qu’images à un module d’inférence externe tel que Yolo V3 et recevoir les résultats d’analyse JSON en retour. Dans ce type de topologie, la destination finale des événements est principalement IoT Hub. Dans les cas où vous ne voyez pas les événements d’inférence sur le hub, vérifiez les éléments suivants :

1. Vérifiez si le hub sur lequel est publié le graphique média est le même que celui que vous examinez. Parfois, lorsque vous créez plusieurs déploiements, vous vous retrouvez avec plusieurs hubs et vous risquez d’inspecter le mauvais hub d’événement par erreur.
1. Vérifiez via VS Code que le module externe est déployé et en cours d’exécution. Dans cette image d’exemple, rtspsim et cv sont des modules IoT Edge qui s’exécutent à l’extérieur du module lvaEdge.

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)
1. Vérifiez que vous envoyez des événements au point de terminaison d’URL correct. Le conteneur d’IA externe expose une URL et un port sur lequel il reçoit et renvoie les données des requêtes POST. Cette URL est spécifiée en tant que point de terminaison : propriété URL pour le processeur d’extension HTTP. Comme indiqué dans l’[URL de topologie](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), elle est définie sur le paramètre d’URL d’inférence. Assurez-vous que la valeur par défaut du paramètre (http://yolov3/score) ou que la valeur transmise sont correctes. Vous pouvez tester si elles fonctionnent en utilisant Curl.  
    1. Par exemple, un conteneur Yolo v3 s’exécute sur l’ordinateur local et l’adresse IP du conteneur est 172.17.0.3 (utilisez l’inspection docker pour trouver l’adresse IP).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Résultat retourné :

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Si vous exécutez une ou plusieurs instances d’un graphique en tirant parti du processeur d’extension HTTP, vous devez avoir un filtre de fréquence d’images avant chaque processeur d’extension HTTP pour gérer les images par seconde (FPS) du flux vidéo. Dans certains cas où l’UC/la mémoire de la solution de périphérie sont fortement utilisées, vous pouvez perdre certains événements d’inférence. Pour résoudre ce problème, définissez une valeur faible pour la propriété FPS maximum sur le filtre de fréquence d’images. Vous pouvez définir la valeur 0,5 (« FPS maximum » : 0,5) sur chaque instance du graphique, puis réexécutez pour rechercher les événements d’inférence sur le hub.
    1. Sinon, vous pouvez vous procurer une solution de périphérie plus puissante avec une UC et une mémoire plus élevées.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Plusieurs méthodes directes en parallèle – échec au terme du délai d’expiration 

Live Video Analytics sur IoT Edge fournit un modèle de programmation basé sur une méthode directe qui permet la configuration de plusieurs topologies et plusieurs instances de graphe. Dans le cadre de la configuration de la topologie et du graphe, vous allez procéder à plusieurs appels de méthode directe sur le module Edge. Si vous invoquez ces appels multiples de méthode, en particulier ceux qui démarrent et arrêtent les graphiques, en parallèle, vous pouvez faire face à des échecs au terme du délai d’expiration, comme ci-dessous. 

La méthode d’Assembly Initialization Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync a levé l’exception. Microsoft.Azure.Devices.Common.Exceptions.IotHubException : Microsoft.Azure.Devices.Common.Exceptions.IotHubException :<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Nous vous recommandons de ne pas appeler plusieurs méthodes directes en parallèle, mais de le faire de façon séquentielle, c’est-à-dire passer un appel de méthode directe uniquement lorsque le premier est terminé.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>Récupérer des journaux d’activité pour envoyer un ticket de support

Lorsque les étapes de résolution des problèmes auto-guidée ne résolvent pas vos problèmes, vous devriez aller sur le Portail Azure et [ouvrir un ticket de support](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Procédez comme suit pour récupérer les journaux d’activité appropriés qui doivent être ajoutés au ticket. Vous pourrez charger les journaux d’activité dans l’onglet **Détails**de la demande de support.

### <a name="support-bundle"></a>Support-bundle

Lorsque vous devez récupérer des journaux d'activité depuis un périphérique IoT Edge, la façon la plus simple est d’utiliser la commande `support-bundle`. Cette commande récupère :

- Les journaux de module
- Les journaux du gestionnaire de sécurité IoT Edge et du moteur de conteneur
- Sortie Json de vérification Iotedge
- Des informations de débogage utiles

#### <a name="use-the-iot-edge-security-manager"></a>Utiliser le gestionnaire de sécurite IoT Edge
 
Le gestionnaire de sécurité IoT Edge est responsable des opérations comme l’initialisation du système IoT Edge au démarrage et l’approvisionnement des appareils. Si IoT Edge ne démarre pas, les journaux du gestionnaire de sécurité peuvent fournir des informations utiles. Pour afficher des journaux d’activité du gestionnaire de sécurité IoT Edge plus détaillés :

1. Éditez les paramètres du démon IoT Edge sur l’appareil IoT Edge :

    ```
    sudo systemctl edit iotedge.service
    ```

1. Mettez à jour les lignes suivantes :

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Redémarrez les Démon de Sécurité IoT Edge en exécutant ces commandes :

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Exécutez la commande `support-bundle` avec l’indicateur --since pour spécifier jusqu’à quand doivent remonter les journaux que vous souhaitez récupérer. Par exemple, 2h vous permettra de récupérer les journaux d'activité des deux dernières heures. Vous pouvez changer la valeur de cet indicateur pour inclure les journaux d’activité d’une période différente.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Journaux d’activité de débogage LVA

Procédez comme suit pour configurer le LVA sur le module IoT Edge pour générer des journaux d'activité de débogage :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Sélectionnez **IoT Edge** dans le menu.
1. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.
1. Cliquez sur le lien **Modules d’ensemble** depuis le menu supérieur.

  ![modules d’ensemble portail azure](media/troubleshoot-how-to/set-modules.png)

5. Dans la section Modules IoT Edge, trouvez et sélectionnez **lvaEdge**.
1. Cliquez sur **Options de création de conteneur**.
1. Dans la section Binds, ajoutez la commande suivante :

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    Cela lie les dossiers de journaux d'activité entre le périphérique et le conteneur.

1. Cliquez sur le bouton **Mettre à jour**
1. Cliquez sur le bouton **Vérifier + créer** en bas de la page. Une validation simple aura lieu et un message de validation réussie sur fond vert.
1. Cliquez sur le bouton **Créer**.
1. Ensuite, mettez à jour le **Jumeau d’identité de module** pour diriger le paramètre DebugLogsDirectory vers le répertoire dans lequel les journaux d'activité seront enregistrés :
    1. Sélectionnez **lvaEdge** sous la table **Modules**.
    1. Cliquez sur le lien **Jumeau d’identité de module**. Vous le trouverez en haut de la page. Cela ouvrira un volet modifiable.
    1. Ajoutez la paire clé-valeur suivante sous **clé souhaitée** :

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Cliquez sur **Save**(Enregistrer).

1. Reproduisez le problème.
1. Connectez-vous à la machine virtuelle à partir de la page IoT Hub de votre portail.
1. Accédez au dossier `/var/local/mediaservices/logs` et compressez le contenu bin de ce dossier avant de nous le partager. (Ces fichiers journaux ne sont pas destinés à un diagnostic personnel. Ils permettent aux ingénieurs Azure d’analyser vos problèmes.)

1. La collection de journaux peut être arrêtée en définissant cette valeur dans **Module Identity Twin** comme *null* de nouveau. Retournez sur la page **Module Identity Twin** et mettez à jour le paramètre suivant comme suit :

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Enregistrement vidéo basé sur les événements dans le Cloud et lecture à partir du Cloud](event-based-video-recording-tutorial.md)
