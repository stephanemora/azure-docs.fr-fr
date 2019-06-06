---
title: Visualisation des données en temps réel des données de capteur à partir de votre Azure IoT hub dans une application web | Microsoft Docs
description: Utiliser une application web pour visualiser les données de température et d’humidité collectées à partir d’un capteur et envoyées à votre Iot hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476006"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualiser les données de capteur en temps réel à partir de votre Azure IoT hub dans une application web

![Diagramme de bout en bout](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Dans ce didacticiel, vous allez apprendre à visualiser les données de capteur en temps réel que votre IoT hub reçoit avec une application web node.js sur votre ordinateur local. Après avoir exécuté l’application web localement, vous pouvez éventuellement suivre des étapes pour héberger l’application web dans Azure App Service. Si vous souhaitez essayer de visualiser les données dans votre IoT Hub avec Power BI, voir [Visualiser des données de capteur en temps réel depuis Azure IoT Hub à l’aide de Power BI](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Procédure

* Ajouter un groupe de consommateurs à votre IoT hub que l’application web utilisera pour lire les données de capteur
* Télécharger le code d’application web à partir de GitHub
* Examinez le code d’application web
* Configurer les variables d’environnement pour stocker les artefacts requis par votre application web de l’IoT Hub
* Exécuter l’application web sur votre ordinateur de développement
* Ouvrir une page web pour afficher des données de température et d’humidité en temps réel à partir de votre IoT hub
* (Facultatif) Utiliser Azure CLI pour héberger votre application web dans Azure App Service

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Terminer la [simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) didacticiel ou un des didacticiels appareil ; par exemple, [Raspberry Pi avec node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ces couvrent les exigences suivantes :

  * Un abonnement Azure actif
  * Un IoT Hub associé à votre abonnement
  * Une application cliente qui envoie des messages à votre IoT Hub

* [Télécharger Git](https://www.git-scm.com/downloads)

* Les étapes de cet article supposent un ordinateur de développement Windows ; Toutefois, vous pouvez facilement effectuer ces étapes sur un système Linux dans votre interpréteur de commandes préféré.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Exécutez la commande suivante afin d’ajouter l’extension Microsoft Azure IoT pour Azure CLI à votre instance Cloud Shell. L’extension IoT ajoute des commandes IoT Hub, IoT Edge et IoT Device Provisioning Service (DPS) à Azure CLI.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Ajouter un groupe de consommateurs à votre instance IoT Hub

[Groupes de consommateurs](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fournissent des vues indépendantes dans le flux d’événements qui permettent aux applications et services Azure pour consommer indépendamment des données à partir du même point de terminaison concentrateur d’événements. Dans cette section, vous ajoutez un groupe de consommateurs à intégrés point de terminaison votre IoT hub utilisera pour lire les données à partir de l’application web.

Exécutez la commande suivante pour ajouter un groupe de consommateurs au point de terminaison intégré de votre IoT hub :

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Notez le nom que vous choisissez, vous en aurez besoin plus loin dans ce didacticiel.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obtenir une chaîne de connexion de service pour votre IoT hub

IoT hubs sont créés avec plusieurs stratégies d’accès par défaut. Une telle stratégie est la **service** policy, qui fournit des autorisations suffisantes pour un service lire et écrire des points de terminaison d’IoT hub. Exécutez la commande suivante pour obtenir une chaîne de connexion pour votre IoT hub qui respecte la stratégie de service :

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

La chaîne de connexion doit ressembler à ce qui suit :

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Notez la chaîne de connexion de service, vous en aurez besoin plus loin dans ce didacticiel.

## <a name="download-the-web-app-from-github"></a>Télécharger l’application web à partir de GitHub

Ouvrez une fenêtre de commande et entrez les commandes suivantes pour télécharger l’exemple à partir de GitHub et au répertoire de l’exemple :

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examinez le code d’application web

À partir du répertoire web-apps-node-iot-hub-data-visualization, ouvrez l’application web dans votre éditeur favori. Ce qui suit montre la structure de fichiers affichée dans VS Code :

![Structure de fichiers d’application Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Prenez un moment pour examiner les fichiers suivants :

* **Server.js** est un script côté service qui initialise le socket web et la classe wrapper de Hub d’événements. Il fournit un rappel à la classe wrapper de Hub d’événements que la classe utilise pour diffuser des messages entrants vers le socket web.

* **Événements-hub-reader.js** est un script côté service qui se connecte au point de terminaison intégré d’IoT hub à l’aide du groupe de consommateurs et de la chaîne de connexion spécifiée. Il extrait l’ID de périphérique et EnqueuedTimeUtc à partir des métadonnées sur les messages entrants et relaie ensuite le message à l’aide de la méthode de rappel inscrite par server.js.

* **Graphique-device-data.js** est un script côté client qui écoute sur le socket web, effectue le suivi de chaque ID de périphérique et stocke les 50 derniers points de données entrantes pour chaque appareil. Il lie ensuite les données de l’appareil sélectionné à l’objet graphique.

* **Index.HTML** gère la disposition de l’interface utilisateur pour la page web et référence les scripts nécessaires pour la logique côté client.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurer les variables d’environnement pour l’application web

Pour lire les données à partir de votre IoT hub, l’application web doit la chaîne de connexion de votre hub IoT et le nom de groupe de consommateurs qui il doit lire. Il obtient ces chaînes à partir de l’environnement du processus dans les lignes suivantes dans server.js :

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Définir les variables d’environnement dans votre fenêtre de commande avec les commandes suivantes. Remplacez les valeurs d’espace réservé par la chaîne de connexion de service pour votre IoT hub et le nom du groupe de consommateurs que vous avez créé précédemment. Ne met entre guillemets les chaînes.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Exécuter l’application web

1. Assurez-vous que votre appareil est en cours d’exécution et l’envoi de données.

2. Dans la fenêtre de commande, exécutez les lignes suivantes pour télécharger et installer des packages référencés et démarrer le site Web :

   ```cmd
   npm install
   npm start
   ```

3. Vous devez voir la sortie dans la console qui indique que l’application web s’est connecté à votre IoT hub et est à l’écoute sur le port 3000 :

   ![Application Web démarrée sur la console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Ouvrir une page web pour afficher les données à partir de votre IoT hub

Ouvrez un navigateur à `http://localhost:3000`.

Dans le **sélectionner un appareil** , sélectionnez votre appareil pour voir un tracé en cours d’exécution de la dernière 50 points de données de température et d’humidité envoyés par l’appareil à votre IoT hub.

![Page d’application web affichant l’humidité et la température en temps réel](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Vous devez également voir la sortie dans la console qui affiche les messages dont la diffusion de votre application web au client de navigateur :  

![Application Web diffuser la sortie de console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Héberger l’application web dans App Service

Le [fonctionnalité Web Apps d’Azure App Service](https://docs.microsoft.com/azure/app-service/overview) fournit une plateforme en tant que service (PAAS) pour l’hébergement d’applications web. Les applications Web hébergées dans Azure App Service peuvent bénéficier de fonctionnalités Azure telles que la sécurité supplémentaire, équilibrage de charge, et l’évolutivité en tant qu’ainsi que Azure et partenaire DevOps solutions comme le déploiement continu, la gestion des packages et ainsi de suite. Azure App Service prend en charge les applications web développées dans de nombreux langages populaires et déployées sur une infrastructure Windows ou Linux.

Dans cette section, vous approvisionnez une application web dans App Service et déployez votre code à ce dernier à l’aide de commandes Azure CLI. Vous trouverez des détails des commandes utilisées dans le [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) documentation. Avant de commencer, assurez-vous que vous avez effectué les étapes à [ajouter un groupe de ressources à votre IoT hub](#add-a-consumer-group-to-your-iot-hub), [obtenir une chaîne de connexion de service pour votre IoT hub](#get-a-service-connection-string-for-your-iot-hub), et [télécharger l’application web à partir de GitHub](#download-the-web-app-from-github).

1. Un [plan App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) définit un ensemble de ressources de calcul pour une application hébergée dans App Service à exécuter. Dans ce didacticiel, nous utilisons le niveau développeur/gratuit pour héberger l’application web. Avec le niveau gratuit, votre application web s’exécute sur des ressources partagées avec d’autres applications App Service, y compris les applications d’autres clients Windows. Azure également les offres Qu'app Service prévoit de déployer des applications web sur Linux des ressources de calcul. Vous pouvez ignorer cette étape si vous avez déjà un plan App Service que vous souhaitez utiliser.

   Pour créer un plan App Service utilisant le niveau gratuit de Windows, exécutez la commande suivante. Utiliser le même groupe de ressources votre IoT hub dans. Nom de votre plan de service peut contenir supérieur et des lettres minuscules, des chiffres et des traits d’union.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. À présent, configurez une application web dans votre plan App Service. Le `--deployment-local-git` paramètre active le code d’application web d’être téléchargées et déployées à partir d’un référentiel Git sur votre ordinateur local. Le nom de votre application web doit être globalement unique et peut contenir supérieur et des lettres minuscules, des chiffres et des traits d’union.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Ajoutez maintenant les paramètres d’Application pour les variables d’environnement qui spécifient la chaîne de connexion IoT hub et le groupe de consommateurs Event hub. Les paramètres individuels sont délimitée dans le `-settings` paramètre. Utilisez la chaîne de connexion de service pour votre IoT hub et le groupe de consommateurs que vous avez créé précédemment dans ce didacticiel. Ne met entre guillemets les valeurs.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Activer les Sockets Web de protocole pour l’application web et configurer l’application web pour recevoir HTTPS demande uniquement (les requêtes HTTP sont redirigées vers HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Pour déployer le code sur App Service, vous allez utiliser votre [informations d’identification de déploiement au niveau de l’utilisateur](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Vos informations d’identification de déploiement au niveau de l’utilisateur sont différentes de vos informations d’identification Azure et sont utilisées pour les déploiements de FTP pour une application web et de Git local. Une fois définies, elles sont valides dans l’ensemble de vos applications App Service dans tous les abonnements de votre compte Azure. Si vous avez précédemment configuré les informations d’identification de déploiement au niveau de l’utilisateur, vous pouvez les utiliser.

   Si vous n’avez pas défini précédemment les informations d’identification de déploiement au niveau de l’utilisateur ou si vous avez oublié votre mot de passe, exécutez la commande suivante. Votre nom d’utilisateur de déploiement doit être unique au sein d’Azure, et ne doit pas contenir le ' @' pour Git local exécute un push de symboles. Lorsque vous y êtes invité, entrez et confirmez votre nouveau mot de passe. Le mot de passe doit comporter au moins huit caractères et inclure deux des trois éléments suivants : lettres, chiffres et symboles.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Obtenir l’URL Git à utiliser pour envoyer votre code jusqu'à App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Ajouter un référentiel distant à votre clone qui référence le référentiel Git pour l’application web dans App Service. Pour \<URL de clone Git\>, utilisez l’URL retournée à l’étape précédente. Exécutez la commande suivante dans votre fenêtre de commande.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Pour déployer le code sur App Service, entrez la commande suivante dans votre fenêtre de commande. Si vous êtes invité aux informations d’identification, entrez les informations d’identification de déploiement au niveau de l’utilisateur que vous avez créé à l’étape 5. Assurez-vous que vous envoyez à la branche principale du Service d’application distant.

    ```cmd
    git push webapp master:master
    ```

9. La progression du déploiement met à jour dans votre fenêtre de commande. Un déploiement réussi prendra fin par des lignes similaires à la sortie suivante :

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Exécutez la commande suivante pour interroger l’état de votre application web et vous assurer qu’il est en cours d’exécution :

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Dans un navigateur, accédez à `https://<your web app name>.azurewebsites.net`. Une page web similaire à la table que vous avez vu lorsque vous avez exécuté l’application web localement affiche. En supposant que votre appareil est en cours d’exécution et l’envoi de données, vous devez voir un tracé en cours d’exécution des 50 dernières température et humidité relevés envoyés par l’appareil.

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes avec cet exemple, essayez les étapes décrites dans les sections suivantes. Si vous rencontrez des problèmes, envoyez-nous vos commentaires au bas de cette rubrique.

### <a name="client-issues"></a>Problèmes de client

* Si un appareil n’apparaît pas dans la liste, ou aucun graphique n’est dessinée, vérifiez que le code de l’appareil est en cours d’exécution sur votre appareil.

* Dans le navigateur, ouvrez les outils de développement (dans de nombreux navigateurs la touche F12 clé s’ouvrira il) et recherchez la console. Recherchez les éventuels avertissements ou erreurs imprimés il.

* Vous pouvez déboguer un script côté client dans /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problèmes de site Web local

* Regardez la sortie dans la fenêtre où vous avez lancé le nœud de sortie de console.

* Déboguer le code du serveur, en particulier server.js et /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problèmes liés à Azure App Service

* Dans le portail Azure, accédez à votre application web. Sous **surveillance** dans le volet gauche, sélectionnez **journaux App Service**. Activer **journal des applications (système de fichiers)** , définir **niveau** à erreur, puis sélectionnez **enregistrer**. Puis ouvrez **flux de journal** (sous **surveillance**).

* À partir de votre application web dans le portail Azure, sous **outils de développement** sélectionnez **Console** et valider des versions de nœud et npm avec `node -v` et `npm -v`.

* Si vous voyez une erreur relative à un package est introuvable, il pourrez que vous avez exécuté les étapes en désordre. Lorsque le site est déployé (avec `git push`) s’exécute le service d’application `npm install`, qui s’exécute en fonction de la version actuelle du nœud a configuré. Si qui est modifié dans la configuration ultérieurement, vous devez apporter une modification sans signification au code et envoyez à nouveau.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement utilisé votre application web pour visualiser en temps réel des données de capteur à partir de votre IoT Hub.

Pour une autre façon de visualiser les données à partir d’Azure IoT Hub, consultez [utilisez Power BI pour visualiser les données de capteur en temps réel à partir de votre IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
