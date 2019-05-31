---
title: Tâches de périphérie de boîte de données Analytique de Stream dans les outils Azure Stream Analytique pour Visual Studio
description: Cet article décrit comment créer, déboguer et créer des travaux Stream Analytique données boîte Edge avec les outils d’Analytique de Stream pour Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 242fb2daebfe9eb6e5a0c73c2c4c0e91a3131032
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304155"
---
# <a name="develop-stream-analytics-data-box-edge-jobs-using-visual-studio-tools"></a>Développer des travaux Stream Analytique données boîte Edge à l’aide des outils de Visual Studio

Dans ce didacticiel, vous allez apprendre à utiliser les outils d’Analytique de Stream pour Visual Studio. Vous allez apprendre à créer, déboguer et créer des travaux Stream Analytique données boîte Edge. Après avoir créé et testé le travail, vous pourrez accéder au portail Azure pour le déployer sur vos appareils. 

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous avez besoin des prérequis suivants :

* Installer [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), ou [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge. L’édition Express n’est pas prise en charge.  

* Suivez les [instructions d’installation](stream-analytics-tools-for-visual-studio-edge-jobs.md) pour installer les outils Stream Analytics pour Visual Studio.
 
## <a name="create-a-stream-analytics-data-box-edge-project"></a>Créer un projet Stream Analytique données boîte Edge 

Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**. Accédez à la liste **Modèles** à gauche > développez **Azure Stream Analytics** > **Stream Analytics Edge** > **Application Azure Stream Analytics Edge**. Entrez le nom, l’emplacement et le nom de la solution pour votre projet, puis sélectionnez **OK**.

![Nouveau projet Stream Analytique données boîte Edge dans Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Une fois que vous avez créé le projet, accédez à **l’Explorateur de solutions** pour afficher l’arborescence des dossiers.

![Vue Explorateur de solutions de tâche Stream Analytique données boîte Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Choisir le bon abonnement

1. Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de serveurs**.  

2. Cliquez avec le bouton droit sur **Azure** > Sélectionnez **Se connecter à un abonnement Microsoft Azure** > et connectez-vous avec votre compte Azure.

## <a name="define-inputs"></a>Définir les entrées

1. Dans **l’Explorateur de solutions**, développez le nœud **Entrées** pour afficher l’entrée **EdgeInput.json**. Double-cliquez sur cette sortie pour afficher ses paramètres.  

2. Définissez le type de source sur **Flux de données**. Ensuite, définissez la source sur **Edge Hub**, le format de sérialisation d’événement sur **Json**et l’encodage sur **UTF8**. Pour les besoins de cet exemple, laissez l’alias indiqué dans **Alias d’entrée**. Cet alias peut toutefois être renommé. Si vous renommez l’alias d’entrée, utilisez le nom que vous avez spécifié au moment de la définition de la requête. Sélectionnez **Enregistrer** pour enregistrer les paramètres.  
   ![Configuration d’une entrée de travail Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Définir les sorties

1. Dans **l’Explorateur de solutions**, développez le nœud **Sorties** pour afficher la sortie **EdgeOutput.json**. Double-cliquez sur cette sortie pour afficher ses paramètres.  

2. Veillez à définir le récepteur pour sélectionner **Edge Hub**, définir le Format de sérialisation d’événement **Json**, affectez la valeur d’encodage à **UTF8**et définissez le Format **tableau**. Pour les besoins de cet exemple, laissez l’alias indiqué dans **Alias de sortie**. Cet alias peut toutefois être renommé. Si vous renommez l’alias de sortie, utilisez le nom que vous avez spécifié au moment de la définition de la requête. Sélectionnez **Enregistrer** pour enregistrer les paramètres. 
   ![Configuration d’une sortie de travail Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Définir la requête de transformation

Travaux d’Analytique de Stream déployés dans les environnements Stream Analytique données boîte Edge prennent en charge la plupart des [référence du langage de requête Analytique Stream](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Toutefois, les opérations suivantes ne sont pas encore pris en charge les tâches de périphérie de zone Stream Analytique données : 


|**Catégorie**  | **Commande**  |
|---------|---------|
|Opérateurs géospatiaux |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Autres opérateurs | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Paramètre d’expression dans l’opérateur COUNT</li><li>Microseconde dans les fonctions DATE et TIME</li><li>UDA JavaScript (fonctionnalité actuellement en préversion pour les travaux déployés dans le cloud)</li></ul>   |

Lorsque vous créez un travail Stream Analytique données boîte Edge dans le portail, le compilateur affiche un avertissement si vous n’utilisez pas un opérateur pris en charge.

Dans Visual Studio, définissez la requête de transformation suivante dans l’éditeur de requête (**fichier script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Tester le travail localement

Pour tester la requête localement, vous devez charger les exemples de données. Pour obtenir les exemples de données, téléchargez les données d’inscription à partir du [référentiel GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json). Enregistrez-les sur votre ordinateur local. 

1. Pour charger les exemples de données, cliquez avec le bouton droit sur le fichier **EdgeInput.json** et choisissez **Ajouter une entrée locale**  

2. Dans la fenêtre indépendante qui s’affiche > sélectionnez **Parcourir** pour accéder aux exemples de données à partir de votre chemin local > sélectionnez **Enregistrer**.
   ![Configuration d’une entrée locale dans Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Un fichier nommé **local_EdgeInput.json** est automatiquement ajouté à votre dossier d’entrées.  
4. Vous pouvez exécuter localement ou envoyer sur Azure. Pour tester la requête, sélectionnez **exécution locale**.  
   ![Options d’exécution de travail Stream Analytics dans Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. La fenêtre d’invite de commandes affiche l’état du travail. Quand le travail s’exécute correctement, un dossier du type « 2018-02-23-11-31-42 » est créé dans le chemin du dossier de projet « Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42 ». Accédez à l’emplacement du dossier pour afficher les résultats dans le dossier local :

   Vous pouvez également vous connecter au portail Azure pour vérifier que le travail a bien été créé. 

   ![Dossier de résultat du travail Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Envoyer le travail sur Azure

1. Avant d’envoyer le travail sur Azure, vous devez vous connecter à votre abonnement Azure. Ouvrez **l’Explorateur de serveurs** > cliquez avec le bouton droit sur **Azure** > **Se connecter à un abonnement Microsoft Azure** > connectez-vous à votre abonnement Azure.  

2. Pour envoyer le travail sur Azure, accédez à l’éditeur de requête > sélectionnez **Envoyer sur Azure**.  

3. Une fenêtre contextuelle s’ouvre. Choisir de mettre à jour un travail Stream Analytique données boîte Edge existant ou créez-en un. Lorsque vous mettez à jour un travail existant, il remplacera toute la configuration de travail, dans ce scénario, vous allez publier une nouvelle tâche. Sélectionnez **Créer un travail Azure Stream Analytics** > entrez un nom pour votre travail (par exemple, **MyASAEdgeJob**) > choisissez les paramètres **Abonnement**, **Groupe de ressources** et **Emplacement** appropriés > sélectionnez **Envoyer**.

   ![Envoyer votre travail Stream Analytics sur Azure à partir de Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Désormais, votre travail Stream Analytique données boîte Edge a été créé. Vous pouvez faire référence à la [exécuter des tâches sur le didacticiel de IoT Edge](stream-analytics-edge.md) pour apprendre à déployer sur vos appareils. 

## <a name="manage-the-job"></a>Gérer le travail 

Vous pouvez afficher l’état et le diagramme du travail à partir de l’Explorateur de serveurs. À partir de **Stream Analytique** dans **Explorateur de serveurs**, développez l’abonnement et le groupe de ressources où vous avez déployé le travail Stream Analytique données boîte Edge. Vous pouvez voir le travail MyASAEdgejob avec l’état **Créé**. Développez le nœud du travail et double-cliquez dessus pour afficher la vue du travail.

![Options de gestion du travail dans l’Explorateur de serveurs](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
Dans la fenêtre de vue du travail, vous pouvez effectuer des opérations comme actualiser le travail, le supprimer et l’ouvrir à partir du portail Azure.

![Diagramme du travail et autres options dans Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Étapes suivantes

* [Plus d’informations sur Azure Iot Edge](../iot-edge/about-iot-edge.md)
* [Didacticiel pour ASA sur IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Envoyer des commentaires à l’équipe à l’aide de ce questionnaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
