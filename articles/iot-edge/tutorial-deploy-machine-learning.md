---
title: Déployer Azure Machine Learning avec Azure IoT Edge | Microsoft Docs
description: Dans ce didacticiel, vous déployez Azure Machine Learning en tant que module sur un appareil Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/22/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: ddc5adbb5b6bc7f04a893b05df3601714ed85eec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991451"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Didacticiel : Déployer Azure Machine Learning en tant que module IoT Edge (préversion)

Vous pouvez utiliser des modules IoT Edge pour déployer du code qui implémente votre logique métier directement sur vos appareils IoT Edge. Ce tutoriel vous guide tout au long du déploiement d’un module Azure Machine Learning qui prédit l’échec d’un appareil à partir des données de température de machine simulée. Pour plus d’informations sur Azure ML sur IoT Edge, consultez la [documentation Azure Machine Learning](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md).

Le module Azure Machine Learning que vous créez dans ce didacticiel lit les données de l’environnement générées par votre appareil et étiquette les messages comme étant anormaux ou pas.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un module Azure Machine Learning
> * Envoyer (push) un conteneur de module dans un registre de conteneurs Azure
> * Déployer un module Azure Machine Learning sur votre appareil Azure IoT Edge
> * Afficher les données générées

>[!NOTE]
>Les modules Azure Machine Learning sur Azure IoT Edge sont en préversion publique. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).
* Le module Azure Machine Learning ne prend pas en charge les processeurs ARM.

Ressources cloud :

* Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 
* Un compte Azure Machine Learning. Suivez les instructions de la page [Créer des comptes Azure Machine Learning et installer Azure Machine Learning Workbench](../machine-learning/desktop-workbench/quickstart-installation.md). Vous n’avez pas besoin d’installer l’application Workbench dans le cadre de ce didacticiel. 

Ressources de développement :

* Gestion des modèles pour Azure ML. Pour configurer votre environnement et créer un compte, suivez les instructions de la page [Configuration de la gestion des modèles](../machine-learning/desktop-workbench/deployment-setup-configuration.md). Pendant l’installation du déploiement, il est recommandé de choisir les étapes locales au lieu du cluster, si possible.

### <a name="disable-process-identification"></a>Désactiver l’identification du processus

>[!NOTE]
>
> En préversion, Azure Machine Learning ne prend pas en charge la fonctionnalité de sécurité d’identification du processus activée par défaut avec IoT Edge. 
> Voici les étapes permettant de la désactiver. Elles ne sont toutefois pas appropriées pour une utilisation en production. Ces étapes sont nécessaires uniquement sous Linux, comme vous les aurez déjà exécutées lors de l’installation du runtime Windows Edge.

Pour désactiver l’identification du processus sur votre appareil IoT Edge, vous devez fournir l’adresse IP et le port pour **workload_uri** et **management_uri** dans la section **Connecter** de la configuration du démon IoT Edge.

Récupérez d’abord l’adresse IP. Entrez `ipconfig` dans votre ligne de commande et copiez l’adresse IP de l’interface **docker0**.

Modifiez le fichier de configuration du démon IoT Edge :

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Mettez à jour la section **Connecter** de la configuration avec votre adresse IP. Par exemple :
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Entrez les mêmes adresses dans la section **Écouter** de la configuration. Par exemple :

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Créez une variable d’environnement IOTEDGE_HOST avec l’adresse management_uri (pour la définir définitivement, ajoutez-la à `/etc/environment`). Par exemple :

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Créer le conteneur Azure ML
Dans cette section, vous allez télécharger les fichiers de modèles entraînés et les convertir en un conteneur Azure ML.

Sur l’ordinateur exécutant la Gestion des modules pour Azure ML, téléchargez et enregistrez [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) et [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl), que vous trouverez dans le kit de ressources Azure ML IoT sur GitHub. Ces fichiers définissent le modèle d’apprentissage de la machine entraînée qui sera déployé sur l’appareil IoT Edge.

Utilisez le modèle entraîné pour créer un conteneur qui pourra être déployé sur des appareils IoT Edge. Utilisez la commande suivante pour :

   * enregistrer votre modèle
   * Créez un manifeste.
   * créer une image de conteneur Docker nommée *machinelearningmodule*
   * déployer l’image vers un cluster Azure Kubernetes Service (AKS).

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>Afficher le référentiel de conteneur

Vérifiez que votre image de conteneur a bien été créée et stockée dans le registre de conteneurs Azure associé à votre environnement Machine Learning.

1. Sur le [Portail Azure](https://portal.azure.com), accédez à **Tous les services** et sélectionnez **Registres de conteneurs**.
2. Sélectionnez votre registre. Le nom doit commencer par **mlcr** : il appartient au groupe de ressources, à l’emplacement et à l’abonnement que vous avez utilisés pour configurer la Gestion des modules.
3. Sélectionnez **Clés d’accès**.
4. Copiez **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**.  Vous en aurez besoin pour accéder au registre sur vos appareils Edge.
5. Sélectionnez **Référentiels**.
6. Sélectionnez **machinelearningmodule**.
7. Vous avez maintenant le chemin d’accès complet de l’image du conteneur. Notez le chemin d’accès de l’image pour la section suivante. Il devrait se présenter ainsi : **<nom_registre>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Déployer sur votre appareil

1. Accédez à votre IoT Hub sur le [portail Azure](https://portal.azure.com).

1. Accédez à **IoT Edge** et sélectionnez votre appareil IoT Edge.

1. Sélectionnez **Définir des modules**.

1. Dans la section **Paramètres du registre**, ajoutez les informations d’identification que vous avez copiées à partir de votre registre de conteneurs Azure. 

   ![Ajouter les informations d’identification du registre](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Si vous avez déjà déployé le module tempSensor sur votre appareil IoT Edge, il est possible qu’il se remplisse automatiquement. S’il ne figure pas encore dans la liste des modules, ajoutez-le.

    1. Cliquez sur **Ajouter** et sélectionnez **Module IoT Edge**.
    2. Dans le champ **Nom**, entrez `tempSensor`.
    3. Dans le champ **URI de l’image**, entrez `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Sélectionnez **Enregistrer**.

1. Ajoutez le module de Machine Learning que vous avez créé.

    1. Cliquez sur **Ajouter** et sélectionnez **Module IoT Edge**.
    1. Dans le champ **Nom**, entrez `machinelearningmodule`.
    1. Dans le champ **Image**, saisissez l’adresse de votre image, par exemple `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Sélectionnez **Enregistrer**.

1. Revenez à l’étape **Ajouter des modules** et sélectionnez **Suivant**.

1. À l’étape **Spécifier des itinéraires**, copiez le JSON ci-dessous dans la zone de texte. Le premier itinéraire transmet les messages du capteur de température au module Machine Learning le point de terminaison « amlInput », le point de terminaison utilisé par tous les modules Azure Machine Learning. Le deuxième itinéraire transmet les messages du module Machine Learning à IoT Hub. Dans cet itinéraire, « amlOutput » correspond au point de terminaison utilisé par tous les modules Azure Machine Learning pour transmettre des données, et « $upstream » signifie IoT Hub.

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ```

1. Sélectionnez **Suivant**.

1. À l’étape **Vérifier le déploiement**, sélectionnez **Envoyer**.

1. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**.  Le nouveau module **machinelearningmodule** devrait apparaître en cours d’exécution avec les modules **tempSensor** et Runtime IoT Edge.

## <a name="view-generated-data"></a>Afficher les données générées

Vous pouvez afficher les messages générés par chaque module IoT Edge, et vous pouvez afficher les messages envoyés à votre hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Afficher les données sur votre appareil IoT Edge

Vous pouvez afficher les messages envoyés par chaque module individuel sur votre appareil IoT Edge. 

Si vous exécutez ces commandes sur un appareil Linux, vous devez peut-être utiliser `sudo` pour obtenir des autorisations élevées.

1. Affichez tous les modules sur votre appareil IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Affichez les messages envoyés par un appareil spécifique. Utilisez le nom du module de la sortie de la commande précédente.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Afficher les données reçues par votre hub IoT

Vous pouvez afficher les messages appareil-cloud reçus par votre hub IoT à l’aide de [l’extension du kit de ressources Azure IoT pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Les étapes suivantes montrent comment configurer Visual Studio Code pour surveiller les messages appareil-à-cloud reçus par votre hub IoT. 

1. Dans Visual Studio Code, sélectionnez **Appareils IoT Hub**.

2. Sélectionnez **...**, puis **Définir la chaîne de connexion IoT Hub** dans le menu.

   ![Menu plus Appareils IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Dans la zone de texte qui s’ouvre en haut de la page, entrez la chaîne de connexion iothubowner de votre hub IoT. Votre appareil IoT Edge devrait apparaître dans la liste Appareils IoT Hub.

4. Sélectionnez à nouveau **...**, puis **Commencer le monitoring du message D2C**.

5. Observez les messages en provenance de tempSensor toutes les cinq secondes. Le corps du message contient une propriété appelée **anomalie** qu’offre le machinelearningmodule avec la valeur true ou false. La propriété **AzureMLResponse** contient la valeur « OK » si le modèle a été exécuté correctement.

   ![Réponse d’Azure ML dans le corps du message](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Supprimer les ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un module IoT Edge optimisé par Azure Machine Learning. Vous pouvez continuer avec l’un des autres didacticiels pour en savoir plus sur les autres façons dont Azure IoT Edge peut vous aider à transformer des données en informations métier à la périphérie.

> [!div class="nextstepaction"]
> [Filtrer les données de capteur à l’aide du code C#](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
