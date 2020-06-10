---
title: Configurer des modules IoT Edge dans Azure SQL Edge
description: Dans la deuxième partie de ce tutoriel Azure SQL Edge en trois parties consacré à la prédiction des impuretés contenues dans le minerai de fer, vous allez configurer les modules et les connexions IoT Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235035"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Configurer les modules et les connexions IoT Edge

Dans la deuxième partie de ce tutoriel en trois parties consacré à la prédiction des impuretés contenues dans le minerai de fer dans Azure SQL Edge, vous allez configurer les modules IoT Edge suivants :

- Azure SQL Edge
- Module IoT Edge de génération de données

## <a name="create-azure-stream-analytics-module"></a>Créer un module Azure Stream Analytics

Créez le module Azure Stream Analytics à utiliser dans ce tutoriel. Pour en savoir plus sur l’utilisation de tâches de streaming avec SQL Edge, consultez [Utilisation de tâches de streaming avec SQL Edge](stream-analytics.md).

Une fois la tâche Azure Stream Analytics créée et l'environnement d'hébergement défini sur Edge, configurez les entrées et les sorties pour le tutoriel.

1. Pour créer l'**entrée**, cliquez sur **+ Ajouter une entrée de flux**. Complétez la section des détails à l'aide des informations suivantes :

   Champ|Valeur
   -----|-----
   Format de sérialisation de l'événement|JSON
   Encodage|UTF-8
   Type de compression d’événement|None

2. Pour créer la **sortie**, cliquez sur **+ Ajouter** et choisissez SQL Database. Complétez la section des détails à l'aide des informations suivantes.

   > [!NOTE]
   > Le mot de passe entré dans cette section doit être spécifié en tant que mot de passe SQL SA lors du déploiement du module SQL Edge dans la section **« Déployer le module Azure SQL Edge »** .

   Champ|Valeur
   -----|-----
   Base de données|IronOreSilicaPrediction
   Nom du serveur|tcp:.,1433
   Nom d’utilisateur|SA
   Mot de passe|Spécifiez un mot de passe fort.
   Table de charge de travail|IronOreMeasurements1

3. Accédez à la section **Requête** et configurez la requête comme suit :

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. Sous **Configurer**, sélectionnez **Publier**, puis cliquez sur le bouton **Publier**. Enregistrez l’URI SAS à utiliser avec le module SQL Database Edge.

## <a name="specify-container-registry-credentials"></a>Spécifier les informations d'identification au registre de conteneurs

Les informations d'identification aux registres de conteneurs hébergeant les images de module doivent être spécifiées. Celles-ci se trouvent dans le registre de conteneurs qui a été créé dans votre groupe de ressources. Accédez à la section **Clés d'accès**. Prenez note des champs suivants :

- Nom du registre
- Serveur de connexion
- Nom d’utilisateur
- Mot de passe

À présent, spécifiez les informations d'identification au conteneur dans le module IoT Edge.

1. Accédez au hub IoT qui a été créé dans votre groupe de ressources.

2. Dans la section **IoT Edge**, sous **Gestion automatique des appareils**, cliquez sur **ID d'appareil**. Pour ce tutoriel, l'ID est `IronOrePredictionDevice`.

3. Sélectionnez la section **Définir des modules**.

4. Sous **Informations d'identification au registre de conteneurs**, entrez les valeurs suivantes :

   _Champ_|_Valeur_
   -------|-------
   Nom|Nom du registre
   Adresse|Serveur de connexion
   User Name|Nom d’utilisateur
   Mot de passe|Mot de passe
  
## <a name="deploy-the-data-generator-module"></a>Déployer le module de génération de données

1. Dans la section **Modules IoT Edge**, cliquez sur **+ AJOUTER** et sélectionnez **Module IoT Edge**.

2. Fournissez un nom de module IoT Edge et l'URI de l'image.
   L'URI de l'image se trouve dans le registre de conteneurs du groupe de ressources. Sélectionnez la section **Référentiels** sous **Services**. Pour ce tutoriel, choisissez le référentiel `silicaprediction`. Sélectionnez la balise appropriée. L'URI de l'image sera au format :

   *serveur de connexion du registre de conteneurs*/*nom du référentiel*:*nom de la balise*

   Par exemple :

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Cliquez sur **Ajouter**.

## <a name="deploy-the-azure-sql-edge-module"></a>Déployer le module Azure SQL Edge

1. Déployez le module Azure SQL Edge en suivant les étapes listées dans [Déployer Azure SQL Edge (préversion)](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

2. Sur la page **Spécifier l'itinéraire** de la page **Définir les modules**, spécifiez les itinéraires de communication entre le module et le hub IoT Edge comme suit. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Par exemple :

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Dans les paramètres du **jumeau de module**, assurez-vous que SQLPackage et ASAJonInfo ont été mis à jour avec les URL SAS que vous avez précédemment enregistrées dans le tutoriel.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Déployer un modèle Machine Learning sur Azure SQL Edge à l'aide d'ONNX](tutorial-run-ml-model-on-sql-edge.md)
