---
title: Tutoriel consacré au déploiement d'une tâche Azure Stream Analytics sur un appareil - Azure IoT Edge | Microsoft Docs
description: Dans ce tutoriel, vous déployez Azure Stream Analytics en tant que module dans une zone d’un appareil Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 31330c3d2b10e6245db775da4039fd1948539df4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106013"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutoriel : Déployer Azure Stream Analytics en tant que module IoT Edge

De nombreuses solutions IoT utilisent les services d’analytiques pour obtenir des informations sur les données envoyées par les appareils IoT au fur et à mesure de leur arrivée dans le Cloud. Avec Azure IoT Edge, vous pouvez utiliser cette logique [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) et la transposer à l’appareil lui-même. Lors du traitement des flux de données de télémétrie en périphérie, vous pouvez réduire la quantité de données chargées et réduire le temps nécessaire pour réagir aux informations actionnables.

Azure IoT Edge et Azure Stream Analytics sont intégrés pour que vous puissiez créer un travail Azure Stream Analytics dans le portail Azure et ensuite le déployer comme module IoT Edge sans code supplémentaire.  

Azure Stream Analytics offre une syntaxe de requête très structurée pour l’analyse des données dans le cloud et sur les appareils IoT Edge. Pour plus d’informations, consultez sur Azure Stream Analytics sur IoT Edge, consultez la [documentation Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Le module Stream Analytics de ce didacticiel calcule la température moyenne sur une fenêtre propagée de 30 secondes. Lorsque cette moyenne atteint 70, le module envoie les données et alerte l’appareil afin de prendre des mesures. Dans ce cas, cette mesure consiste à réinitialiser le capteur de température simulée. Dans un environnement de production, vous pouvez utiliser cette fonctionnalité pour arrêter un ordinateur ou prendre des mesures préventives quand la température atteint des niveaux dangereux. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un travail Azure Stream Analytics pour traiter les données à la périphérie.
> * Connecter le nouveau travail Azure Stream Analytics à d’autres modules IoT Edge.
> * Déployer le travail Azure Stream Analytics sur un appareil IoT Edge depuis le portail Azure.

<center>

![Diagramme – Tutoriel – Structurer, mettre en lots et déployer une tâche ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).

Ressources cloud :

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Créer un travail Azure Stream Analytics

Dans cette section, vous créez un travail Azure Stream Analytics pour extraire des données de votre IoT Hub, demander les données de télémétries envoyées depuis votre appareil, puis envoyer les résultats à un conteneur de stockage Azure Blob. 

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Lorsque vous créez un travail Azure Stream Analytics pour s’exécuter sur un appareil IoT Edge, il doit être stocké de manière à pouvoir être appelé à partir de l’appareil. Vous pouvez utiliser un compte de stockage Azure existant ou en créer un nouveau maintenant. 

1. Dans le portail Azure, accédez à **Créer une ressource** > **Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**. 

1. Fournissez les valeurs suivantes pour créer votre compte de stockage :

   | Champ | Valeur |
   | ----- | ----- |
   | Nom | Fournissez un nom unique pour votre compte de stockage. | 
   | Lieu | Choisissez un emplacement proche de vous. |
   | Abonnement | Choisissez le même abonnement que votre IoT Hub. |
   | Groupe de ressources | Nous vous recommandons d’utiliser le même groupe de ressources pour toutes les ressources de test que vous créez dans le cadre des démarrages rapides et didacticiels IoT Edge. Par exemple, **IoTEdgeResources**. |

1. Conservez les valeurs par défaut pour les autres champs et sélectionnez **Créer**. 

### <a name="create-a-new-job"></a>Créer un travail

1. Dans le portail Azure, accédez à **Créer une ressource** > **Internet des objets** > **Travail Stream Analytics**.

1. Fournissez les valeurs suivantes pour créer votre travail :

   | Champ | Valeur |
   | ----- | ----- |
   | Nom du travail | Fournissez un nom pour votre travail. Par exemple, **IoTEdgeJob** | 
   | Abonnement | Choisissez le même abonnement que votre IoT Hub. |
   | Groupe de ressources | Nous vous recommandons d’utiliser le même groupe de ressources pour toutes les ressources de test que vous créez dans le cadre des démarrages rapides et didacticiels IoT Edge. Par exemple, utilisez **IoTEdgeResources**. |
   | Lieu | Choisissez un emplacement proche de vous. | 
   | Environnement d’hébergement | Sélectionnez **Edge**. |
 
1. Sélectionnez **Créer**.

### <a name="configure-your-job"></a>Configurer votre travail

Lorsque votre travail Stream Analytics est créé dans le portail Azure, vous pouvez le configurer avec une entrée, une sortie et une requête à exécuter sur les données qui le traverse. 

À l’aide des trois éléments d’entrée, de sortie et de requête, cette section crée un travail qui reçoit des données de température de l’appareil IoT Edge. Il analyse ces données dans une fenêtre de 30 secondes consécutives. Si la température moyenne de cette fenêtre dépasse 70 degrés, une alerte est envoyée à l’appareil IoT Edge. Vous allez spécifier exactement d’où les données proviennent et où elles passent dans la section suivante lorsque vous déployez le travail.  

1. Accédez à votre travail Stream Analytics dans le portail Azure. 

1. Sous **Topologie de la tâche**, sélectionnez **Entrées** puis **Ajouter une entrée de flux**.

   ![Entrée Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Choisissez **Edge Hub** dans la liste déroulante.

1. Dans le volet **Nouvelle entrée**, saisissez **température** comme alias d’entrée. 

1. Conservez les valeurs par défaut pour les autres champs et sélectionnez **Enregistrer**.

1. Sous **Topologie de la tâche**, ouvrez **Sorties**, puis sélectionnez **Ajouter**.

   ![Sortie Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. Choisissez **Edge Hub** dans la liste déroulante.

1. Dans le volet **Nouvelle sortie**, saisissez **alerte** comme alias de sortie. 

1. Conservez les valeurs par défaut pour les autres champs et sélectionnez **Enregistrer**.

1. Sous **Topologie de la tâche**, sélectionnez **Requête**.

1. Remplacez le texte par défaut par la requête suivante. Le code SQL envoie une commande de réinitialisation à la sortie d’alerte si la température moyenne de la machine dans une fenêtre de 30 secondes atteint 70 degrés. La commande de réinitialisation a été préprogrammée dans le capteur comme une action qui peut être effectuée. 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. Sélectionnez **Enregistrer**.

### <a name="configure-iot-edge-settings"></a>Configurer les paramètres IoT Edge

Pour préparer votre travail Stream Analytics à déployer sur un appareil IoT Edge, vous devez associer le travail à un conteneur dans un compte de stockage. Lorsque vous passez au déploiement de votre travail, la définition du travail est exportée vers le conteneur de stockage. 

1. Sous **Configurer**, sélectionnez **Paramètres du compte de stockage**.

1. Sélectionnez **Ajouter un compte de stockage**. 

1. Sélectionnez votre **Compte de stockage** dans la liste déroulante.

1. Pour le champ **Conteneur**, sélectionnez **Créer nouveau** et fournissez un nom pour le conteneur de stockage. 

1. Sélectionnez **Enregistrer**. 

## <a name="deploy-the-job"></a>Déployer la tâche

Vous êtes désormais prêt à déployer le travail Azure Stream Analytics sur votre appareil IoT Edge. 

Dans cette section, vous utilisez l’Assistant **Définir des modules** dans le portail Azure pour créer un *manifeste de déploiement*. Un manifeste de déploiement est un fichier JSON qui décrit tous les modules qui seront déployés sur un appareil, les registres de conteneurs qui stockent les images de modules, comment les modules doivent être gérés et comment les modules peuvent communiquer entre eux. Votre appareil IoT Edge récupère son manifeste de déploiement d’IoT Hub, puis utilise les informations qu’il contient pour déployer et configurer tous les modules assignés. 

Pour ce didacticiel, vous déployez deux modules. Le premier est **tempSensor**, un module qui simule un capteur de température et d’humidité. Le second est votre travail Stream Analytics. Le module de capteur fournit le flux de données que votre requête de travail analysera. 

1. Dans le portail Azure, dans votre Hub IoT, accédez à **IoT Edge** et ouvrez la page de détails de votre appareil IoT Edge.

1. Sélectionnez **Définir des modules**.  

1. Si vous aviez déployé le module tempSensor sur cet appareil, il peut se remplir automatiquement. Sinon, ajoutez le module en procédant comme suit :

   1. Cliquez sur **Ajouter** et sélectionnez **Module IoT Edge**.
   1. Pour le nom, tapez **tempSensor**.
   1. Pour l’URI de l’image, entrez **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Laissez les autres paramètres inchangés et sélectionnez **Enregistrer**.

1. Ajoutez votre travail Azure Stream Analytics Edge en suivant les étapes :

   1. Cliquez sur **Ajouter** et sélectionnez **Module Azure Stream Analytics**.
   1. Sélectionnez votre abonnement et le travail Azure Stream Analytics Edge que vous avez créé. 
   1. Sélectionnez **Enregistrer**.

1. Lorsque votre travail Stream Analytics est publié sur le conteneur de stockage que vous avez créé, cliquez sur le nom du module pour voir comment un module Stream Analytics est structuré. 

   L’URI d’image pointe vers une image Azure Stream Analytics standard. La même image est utilisée pour chaque travail déployé sur un appareil IoT Edge. 

   Le jumeau de module est configuré avec une propriété souhaitée nommée **ASAJobInfo**. La valeur de cette propriété pointe vers la définition du travail dans votre conteneur de stockage. Cette propriété correspond à la configuration de l’image Stream Analytics avec les informations de votre travail spécifique. 

1. Fermez la page du module.

1. Notez le nom de votre module Stream Analytics car vous aurez besoin dans l’étape suivante, puis sélectionnez **Suivant** pour continuer.

1. Remplacez le code par défaut dans **Itinéraires** par le code suivant : Remplacez les trois instances de _{moduleName}_ par le nom de votre module Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   Les itinéraires que vous déclarez ici définissent le flux de données via l’appareil IoT Edge. Les données de télémétrie de tempSensor sont envoyées à IoT Hub et à l’entrée **temperature** qui a été configurée dans le travail Stream Analytics. Les messages de sortie d’**alerte** sont envoyés à IoT Hub et au module tempSensor pour déclencher la commande de réinitialisation. 

1. Sélectionnez **Suivant**.

1. À l’étape **Vérifier le déploiement**, sélectionnez **Envoyer**.

1. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**.  

    Vous devez voir le nouveau module Stream Analytics en cours d’exécution avec le module Agent IoT Edge et IoT Edge Hub.

    ![tempSensor et module ASA signalés par l'appareil](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>Afficher les données

Vous pouvez désormais aller sur votre appareil IoT Edge pour consulter les interactions entre les modules Azure Stream Analytics et tempSensor.

1. Vérifiez que tous les modules sont en cours d’exécution dans Docker :

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Affichez tous les journaux d’activité système et les données des métriques. Utilisez le nom du module Stream Analytics :

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Vous devriez voir la température de l’ordinateur augmenter progressivement jusqu’à ce qu’elle atteigne 70 degrés pendant 30 secondes. Le module Stream Analytics déclenche alors une réinitialisation qui fait redescendre la température de l’ordinateur à 21. 

   ![Réinitialiser la sortie de commande dans les journaux d’activité du module](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Supprimer des ressources 

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un travail Azure Stream Analytics pour qu’il analyse les données de votre appareil IoT Edge. Ensuite, vous avez chargé ce module Azure Stream Analytics sur votre appareil IoT Edge pour traiter et réagir localement à l’augmentation de température, et pour envoyer le flux de données agrégées dans le cloud. Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver d’autres solutions pour votre entreprise.

> [!div class="nextstepaction"] 
> [Déployer un modèle Azure Machine Learning en tant que module](tutorial-deploy-machine-learning.md)
