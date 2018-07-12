---
title: Didacticiel - Déployer les travaux ASA sur des appareils Azure IoT Edge | Microsoft Docs
description: Déployer Azure Stream Analytics en tant que module dans un appareil IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0790f504c978b4302812cffc9b655e817c156da3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38540170"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Didacticiel : Déployer Azure Stream Analytics en tant que module IoT Edge - version préliminaire

De nombreuses solutions IoT utilisent les services d’analytiques pour obtenir des informations sur les données envoyées par les appareils IoT au fur et à mesure de leur arrivée dans le Cloud. Avec Azure IoT Edge, vous pouvez utiliser cette logique [Azure Stream Analytics] [ azure-stream] et la transposer à l’appareil lui-même. Lors du traitement des flux de données de télémétrie en périphérie, vous pouvez réduire la quantité de données chargées et réduire le temps nécessaire pour réagir aux informations actionnables.

Azure IoT Edge et Azure Stream Analytics sont intégrés pour que vous puissiez créer un travail Azure Stream Analytics dans le portail Azure et ensuite le déployer comme module IoT Edge sans code supplémentaire.  

Azure Stream Analytics offre une syntaxe de requête très structurée pour l’analyse des données dans le cloud et sur les appareils IoT Edge. Pour plus d’informations, consultez sur Azure Stream Analytics sur IoT Edge, consultez la [documentation Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Le module Stream Analytics de ce didacticiel calcule la température moyenne sur une fenêtre propagée de 30 secondes. Lorsque cette moyenne atteint 70, le module envoie les données et alerte l’appareil afin de prendre des mesures. Dans ce cas, cette mesure consiste à réinitialiser le capteur de température simulée. Dans un environnement de production, vous pouvez utiliser cette fonctionnalité pour arrêter un ordinateur ou prendre des mesures préventives quand la température atteint des niveaux dangereux. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un travail Azure Stream Analytics pour traiter les données à la périphérie.
> * Connecter le nouveau travail Azure Stream Analytics à d’autres modules IoT Edge.
> * Déployer le travail Azure Stream Analytics sur un appareil IoT Edge depuis le portail Azure.

>[!NOTE]
>Les modules Azure Stream Analytics pour IoT Edge sont en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Un hub IoT
* L’appareil Azure IoT Edge que vous avez créé dans le guide de démarrage rapide pour les appareils [Windows][lnk-quickstart-win] ou [Linux][lnk-quickstart-lin]. 

## <a name="create-an-azure-stream-analytics-job"></a>Créer un travail Azure Stream Analytics

Dans cette section, vous créez un travail Azure Stream Analytics pour extraire des données de votre IoT Hub, demander les données de télémétries envoyées depuis votre appareil, puis envoyer les résultats à un conteneur de stockage Azure Blob. Pour plus d’informations, consultez la section « Vue d’ensemble » de la [documentation Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Il vous faut un compte de Stockage Azure pour votre travail Azure Stream Analytics afin de servir de point de terminaison pour la sortie. L’exemple dans cette section utilise un type de stockage Blob. Pour plus d’informations, consultez la section « Objets blob » de la [documentation sur le stockage Azure][azure-storage].

1. Dans le portail Azure, accédez à **Créer une ressource**, entrez **Compte de stockage** dans la zone de recherche, puis sélectionnez **Compte de stockage : blob, fichier, table, file d’attente**.

2. Dans le volet **Créer un compte de stockage**, entrez un nom pour votre compte de stockage, sélectionnez le même emplacement que celui où votre IoT Hub est stocké, sélectionnez le même groupe de ressources que celui de votre IoT Hub, puis sélectionnez **Créer**. Notez le nom. Il sera utile plus tard.

    ![Créez un compte de stockage.][1]


### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le portail Azure, accédez à **Créer une ressource** > **Internet des objets** et sélectionnez **Travail Stream Analytics**.

2. Dans le volet **Nouveau travail Stream Analytics**, procédez comme suit :

   1. Dans la zone **Nom du travail**, entrez un nom pour le travail.
   
   2. Utilisez le même **groupe de ressources** et **emplacement** que votre hub IoT. 

      > [!NOTE]
      > Actuellement, les travaux Azure Stream Analytics sur IoT Edge ne sont pas pris en charge dans la région Ouest des États-Unis 2. 

   3. Sous **Environnement d’hébergement**, sélectionnez **Edge**.
    
3. Sélectionnez **Créer**.

4. Dans le travail créé, sous **Topologie du travail**, ouvrez **Entrées**.

   ![Entrée Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Sélectionnez **Ajouter une entrée de flux**, puis sélectionnez **Edge Hub**.

6. Dans le volet **Nouvelle entrée**, saisissez **température** comme alias d’entrée. 

7. Sélectionnez **Enregistrer**.

8. Sous **Topologie du travail**, ouvrez **Sorties**.

   ![Sortie Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

9. Sélectionnez **Ajouter**, puis sélectionnez **Edge Hub**.

10. Dans le volet **Nouvelle sortie**, saisissez **alerte** comme alias de sortie. 

11. Sélectionnez **Enregistrer**.

12. Sous **Topologie de travail**, sélectionnez **Requête**, puis remplacez le texte par défaut par la requête suivante qui crée une alerte si la température moyenne de la machine lors d’une fenêtre de 30 secondes atteint 70 degrés :

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

13. Sélectionnez **Enregistrer**.

14. Sous **Configurer**, sélectionnez **Paramètres IoT Edge**.

15. Sélectionnez votre **Compte de stockage** dans la liste déroulante.

16. Pour le champ **Conteneur**, sélectionnez **Créer nouveau** et fournissez un nom pour le conteneur de stockage. 

17. Sélectionnez **Enregistrer**. 


## <a name="deploy-the-job"></a>Déployer la tâche

Vous êtes désormais prêt à déployer le travail Azure Stream Analytics sur votre appareil IoT Edge.

1. Dans le portail Azure, dans votre Hub IoT, accédez à **IoT Edge** et ouvrez la page de détails de votre appareil IoT Edge.

2. Sélectionnez **Définir des modules**.  

   Si vous aviez déployé le module tempSensor sur cet appareil, il peut se remplir automatiquement. Sinon, ajoutez le module en procédant comme suit :

   1. Cliquez sur **Ajouter** et sélectionnez **Module IoT Edge**.
   2. Pour le nom, tapez **tempSensor**.
   3. Pour l’URI de l’image, entrez **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   4. Laissez les autres paramètres inchangés.
   5. Sélectionnez **Enregistrer**.

3. Ajoutez votre travail Azure Stream Analytics Edge en suivant les étapes :

   1. Cliquez sur **Ajouter** et sélectionnez **Module Azure Stream Analytics**.
   2. Sélectionnez votre abonnement et le travail Azure Stream Analytics Edge que vous avez créé. 
   3. Sélectionnez **Enregistrer**.

4. Sélectionnez **Suivant**.

5. Remplacez le code par défaut dans **Itinéraires** par le code suivant : Remplacez _{moduleName}_ par le nom de votre module Azure Stream Analytics. Le module doit avoir le même nom que le travail à partir duquel il a été créé. 

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

6. Sélectionnez **Suivant**.

7. À l’étape **Vérifier le déploiement**, sélectionnez **Envoyer**.

8. Revenez à la page de détails de l’appareil et sélectionnez **Actualiser**.  

    Vous devez voir le nouveau module Stream Analytics en cours d’exécution avec le module Agent IoT Edge et IoT Edge Hub.

    ![Sortie de module][7]

## <a name="view-data"></a>Afficher les données

Vous pouvez désormais aller sur votre appareil IoT Edge pour consulter les interactions entre les modules Azure Stream Analytics et tempSensor.

1. Vérifiez que tous les modules sont en cours d’exécution dans Docker :

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
2. Affichez tous les journaux système et les données des métriques. Utilisez le nom du module Stream Analytics :

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Vous devriez voir la température de l’ordinateur augmenter progressivement jusqu’à ce qu’elle atteigne 70 degrés pendant 30 secondes. Le module Stream Analytics déclenche alors une réinitialisation qui fait redescendre la température de l’ordinateur à 21. 

   ![Journal de docker][9]

## <a name="clean-up-resources"></a>Supprimer les ressources 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations déjà créées afin de les réutiliser.

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales créées dans cet article pour éviter des frais. 

> [!IMPORTANT]
> La suppression de ressources et de groupe de ressources Azure est irréversible. Une fois supprimés, le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé l’IoT Hub à l’intérieur d’un groupe de ressources existant qui concerne des ressources que vous souhaitez conserver, supprimer uniquement la ressource de l’ IoT Hub au lieu de supprimer le groupe de ressources.
>

Pour supprimer l’IoT Hub uniquement, exécutez la commande suivante en utilisant le nom de votre hub et le nom du groupe de ressources :

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Pour supprimer un groupe de ressources entier par nom :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom...**, saisissez le nom du groupe de ressources contenant votre IoT Hub. 

3. À droite de votre groupe de ressources dans la liste des résultats, cliquez sur **...**, puis sur **Supprimer le groupe de ressources**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. Il vous sera demandé de confirmer la suppression du groupe de ressources. Saisissez de nouveau le nom de votre groupe de ressources pour confirmer, puis cliquez sur **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un travail Azure Stream Analytics pour qu’il analyse les données de votre appareil IoT Edge. Ensuite, vous avez chargé ce module Azure Stream Analytics sur votre appareil IoT Edge pour traiter et réagir localement à l’augmentation de température, et pour envoyer le flux de données agrégées dans le cloud. Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver d’autres solutions pour votre entreprise.

> [!div class="nextstepaction"] 
> [Déployer un modèle Azure Machine Learning en tant que module][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

