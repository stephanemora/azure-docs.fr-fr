---
title: Prévisions météo à l’aide d’Azure Machine Learning avec des données IoT Hub
description: Utilisez Azure Machine Learning pour prédire le risque de pluie sur la base des données de température et d’humidité que votre IoT Hub collecte à partir d’un capteur.
author: robinsh
manager: philmea
keywords: Prévisions météo avec Machine Learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122216"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Prévision météo utilisant les données de capteur de votre IoT Hub dans Azure Machine Learning

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

L’apprentissage automatique (Machine Learning) utilise des ordinateurs pour exécuter des modèles prédictifs qui apprennent à partir de données existantes afin de prévoir les tendances, résultats et comportements futurs. Azure Machine Learning est un service d’analyse prédictive sur le cloud qui permet de créer et de déployer rapidement des modèles prédictifs sous forme de solutions d’analyse.

## <a name="what-you-learn"></a>Contenu

Vous apprenez à utiliser Azure Machine Learning pour effectuer des prévisions (de chances de pluie) en utilisant les données de température et d’humidité de votre Azure IoT Hub. Les chances de sortie sont la sortie d’un modèle de prévision météo préparé. Le modèle est basé sur des données historiques pour prévoir les chances de pluie en fonction de la température et de l’humidité.

## <a name="what-you-do"></a>Procédure

- Déployez le modèle de prévision météo comme un service web.
- Préparation de votre instance IoT Hub pour l’accès aux données via l’ajout d’un groupe de consommateurs.
- Créez un travail Stream Analytics et configurez-le pour :
  - Lire les données de température et d’humidité en temps réel à partir de votre IoT Hub.
  - Appelez le service web pour obtenir les chances de pluie.
  - Enregistrer les résultats dans un stockage d'objets blob Azure.
- Utilisez Microsoft Azure Storage Explorer pour afficher les prévisions météorologiques.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Suivre le tutoriel [Simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou un des tutoriels de l’appareil, par exemple [Raspberry Pi avec node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ceux-ci couvrent les exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- Un compte [Azure Machine Learning Studio (classique)](https://studio.azureml.net/).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Déployer le modèle de prévision météo comme un service web

Dans cette section, vous obtenez le modèle de prédiction météorologique de la bibliothèque Azure AI. Vous ajoutez ensuite un module R-script au modèle pour nettoyer les données de température et d’humidité. Enfin, vous déployez le modèle en tant que service web prédictif.

### <a name="get-the-weather-prediction-model"></a>Obtenir le modèle de prédiction météorologique

Dans cette section, vous obtenez le modèle de prédiction météorologique d’Azure AI Gallery, et vous l’ouvrez dans Azure Machine Learning Studio (classique).

1. Accédez à la [page de modèle de prévision météo](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Ouvrir la page du modèle de prédiction météorologique dans Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Cliquez sur **Ouvrir dans Studio (classique)** pour ouvrir le modèle dans Microsoft Azure Machine Learning Studio (classique).

   ![Ouvrir le modèle de prédiction météorologique dans Azure Machine Learning Studio (classique)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Ajouter un module de script R pour nettoyer les données de température et d’humidité

Pour que le modèle se comporte correctement, les données de température et d’humidité doivent être convertibles en données numériques. Dans cette section, vous ajoutez un module de script R au modèle de prédiction météorologique pour supprimer toutes les lignes contenant des valeurs de données de température ou d’humidité non convertibles en valeurs numériques.

1. Sur le côté gauche de la fenêtre Azure Machine Learning Studio, cliquez sur la flèche pour développer le panneau Outils. Entrez « Exécuter » dans la zone de recherche. Sélectionnez le module **Exécuter un script R**.

   ![Sélectionner le module Exécuter un script R](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Faites glisser le module **Exécuter un script R** près du module **Clean Missing Data** (Nettoyer les données manquantes) et du module **Exécuter un script R** existant sur le diagramme. Supprimez la connexion entre les modules **Clean Missing Data** et **Exécuter un script R**, puis connectez les entrées et les sorties du nouveau module comme indiqué.

   ![Ajouter un module Exécuter un script R](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Sélectionnez le nouveau module **Exécuter un script R** pour ouvrir sa fenêtre Propriétés. Copiez le code suivant, puis collez-le dans la zone **Script R**.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Une fois que vous avez terminé, la fenêtre Propriétés doit ressembler à ce qui suit :

   ![Ajouter du code au module Exécuter un script R](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Déployer un service web prédictif

Dans cette section, vous validez le modèle, vous configurez un service web prédictif basé sur ce modèle, puis vous déployez le service web.

1. Cliquez sur **Exécuter** pour valider les étapes du modèle. Cette étape peut prendre quelques minutes.

   ![Exécuter l’expérience pour valider les étapes](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Cliquez sur **CONFIGURER LE SERVICE WEB** > **Service web prédictif**. Le diagramme d’expérience prédictive s’ouvre.

   ![Déployer le modèle de prédiction météorologique dans Azure Machine Learning Studio (classique)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Dans le diagramme d’expérience prédictive, supprimez la connexion entre le module **Web service input** (Entrée du service web) et le module **Weather Dataset** (Jeu de données météorologiques) en haut. Faites glisser ensuite le module **Web service input** près du module **Score Model** (Modèle de score), et connectez-le comme indiqué :

   ![Connecter deux modules dans Azure Machine Learning Studio (classique)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Cliquez sur **EXÉCUTER** pour valider les étapes du modèle.

1. Cliquez sur **DÉPLOYER LE SERVICE WEB** pour déployer le modèle en tant que service web.

1. Sur le tableau de bord du modèle, téléchargez le classeur **Excel 2010 ou version antérieure**  pour **DEMANDE/RÉPONSE**.

   > [!Note]
   > Veillez à télécharger le **classeur Excel 2010 ou version antérieure**, même si vous exécutez une version ultérieure d’Excel sur votre ordinateur.

   ![Téléchargement du classeur Excel pour le point de terminaison DEMANDE/RÉPONSE](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Ouvrez le classeur Excel, notez **l’URL DU SERVICE WEB** et la **CLÉ D’ACCÈS**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Créer, configurer et exécuter un travail Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Internet des objets** > **Tâche Stream Analytics**.
1. Saisissez les informations ci-après concernant le travail.

   **Nom du travail** : nom du travail. Le nom doit être globalement unique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   **Emplacement** : utilisez le même emplacement que votre groupe de ressources.

   **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

   ![Créer un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Ajouter une entrée à la tâche Stream Analytics

1. Ouvrez le travail Stream Analytics.
1. Sous **Topologie de la tâche**, cliquez sur **Entrées**.
1. Dans le volet **Entrées**, cliquez sur **Ajouter**, puis saisissez les informations suivantes :

   **Alias d’entrée** : alias unique de l’entrée.

   **Source** : sélectionnez **IoT Hub**.

   **Groupe de consommateurs** : sélectionnez le groupe de consommateurs que vous avez créé.

   ![Ajouter une entrée à un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Ajouter une sortie à la tâche Stream Analytics

1. Sous **Topologie de la tâche**, cliquez sur **Sorties**.
1. Dans le volet **Sorties**, cliquez sur **Ajouter**, puis saisissez les informations suivantes :

   **Alias de sortie** : alias unique de la sortie.

   **Sink** : sélectionnez **Stockage d’objets blob**.

   **Compte de stockage** : Le compte de stockage pour votre stockage d’objets blob. Vous pouvez utiliser un compte de stockage existant ou en créer un nouveau.

   **Conteneur** : le conteneur dans lequel l’objet blob est enregistré. Vous pouvez créer un conteneur ou utiliser un conteneur existant.

   **Format de sérialisation de l’événement** : Sélectionnez **CSV**.

   ![Ajouter une sortie à un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Ajouter une fonction au travail Stream Analytics pour appeler le service web que vous avez déployé

1. Sous **Topologie du travail**, cliquez sur **Fonctions** > **Ajouter**.
1. Entrez les informations suivantes :

   **Alias de la fonction** : entrez `machinelearning`.

   **Type de fonction** : sélectionnez **Azure ML**.

   **Option d’importation** : sélectionnez **Importer à partir d’un autre abonnement**.

   **URL** : entrez l’URL DU SERVICE WEB que vous avez notée à partir du classeur Excel.

   **Clé** : entrez la CLÉ D’ACCÈS que vous avez notée à partir du classeur Excel.

   ![Ajouter une requête à un travail Stream Analytics dans Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Cliquez sur **Créer**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurer la requête de la tâche Stream Analytics

1. Sous **Topologie de la tâche**, cliquez sur **Requête**.
1. Remplacez le code existant par ce qui suit :

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Remplacez `[YourInputAlias]` par l’alias d’entrée du travail.

   Remplacez `[YourOutputAlias]` par l’alias de sortie du travail.

1. Cliquez sur **Enregistrer**.

### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

Dans le travail Stream Analytics, cliquez sur **Démarrer** > **Maintenant** > **Démarrer**. Une fois la tâche lancée, l’état correspondant passe de **Arrêté** à **Exécution**.

![Exécuter la tâche Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Utiliser Microsoft Azure Storage Explorer pour afficher les prévisions météorologiques

Exécutez l’application cliente pour commencer la collecte et l’envoi de données de température et d’humidité à votre IoT Hub. Pour chaque message que votre IoT Hub reçoit, le travail Stream Analytics appelle le service web de prévision météorologique pour produire les chances de pluie. Le résultat est ensuite enregistré dans votre stockage d’objets blob Azure. Azure Storage Explorer est un outil que vous pouvez utiliser pour afficher le résultat.

1. [Téléchargez et installez Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Ouvrez l’Explorateur de stockage Azure.
1. Connectez-vous à votre compte Azure.
1. Sélectionnez votre abonnement.
1. Cliquez sur votre abonnement **Comptes de stockage** > votre compte de stockage > **Conteneurs d’objets blob** > Votre conteneur.
1. Téléchargez un fichier .csv pour voir le résultat. La dernière colonne enregistre les chances de pluie.

   ![Obtenir des résultats de prévisions météo avec Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Résumé

Vous avez correctement utilisé Azure Machine Learning pour produire le risque de pluie sur la base des données de température et d’humidité que votre IoT Hub reçoit.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]