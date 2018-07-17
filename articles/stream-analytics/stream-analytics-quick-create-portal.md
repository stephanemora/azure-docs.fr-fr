---
title: Créer un travail Stream Analytics à l’aide du portail Azure | Microsoft Docs
description: Ce guide de démarrage rapide vous explique comment créer un travail Stream Analytics, configurer des entrées et des sorties, et définir une requête.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/11/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 830b3d4226440a68c7de62170d2ffc28082315c2
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902902"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Démarrage rapide : Créer un travail Stream Analytics à l’aide du portail Azure

Ce guide de démarrage rapide vous explique comment créer un travail Stream Analytics. Dans ce guide de démarrage rapide, vous allez configurer un travail Stream Analytics qui lit des exemples de données de capteur et filtre les lignes qui contiennent une température moyenne supérieure à 100 toutes les 30 secondes. Dans cet article, vous allez lire les données à partir du stockage Blob, transformer les données et les réécrire dans un autre conteneur au sein du même stockage Blob. Le fichier de données d’entrée utilisé dans ce démarrage rapide contient des données statiques à des fins d’illustration uniquement. Dans un scénario réel, vous utilisez la diffusion en continu des données d’entrée pour un travail Stream Analytics.

## <a name="before-you-begin"></a>Avant de commencer

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Préparer les données d’entrée

Avant de définir le travail Stream Analytics, vous devez préparer les données qui seront configurées en tant qu’entrée pour le travail. Pour préparer les données d’entrée requises pour le travail, exécutez les opérations suivantes :

1. Téléchargez les [exemples de données de capteur](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) à partir de GitHub. Les exemples de données contiennent des informations de capteur au format JSON suivant :  

   ```json
   {
     "time": "2018-01-26T21:18:52.0000000",
     "dspl": "sensorC",
     "temp": 87,
     "hmdt": 44
   }
   ```
2. Connectez-vous au [Portail Azure](https://portal.azure.com/).  

3. Dans le coin supérieur gauche du portail Azure, sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage**. Sur la page de travail du compte de stockage, définissez le paramètre **Nom** sur « asaquickstartstorage », le paramètre **Emplacement** sur « Ouest des États-Unis 2 », le paramètre **Groupe de ressources** sur « asaquickstart-resourcegroup » (pour bénéficier de meilleures performances, hébergez le compte de stockage dans le même groupe de ressources que le travail Stream Analytics). Vous pouvez conserver les valeurs par défaut des autres paramètres.  

   ![Créer un compte de stockage](./media/stream-analytics-quick-create-portal/create-a-storage-account.png)

4. À partir de la page **Toutes les ressources**, recherchez le compte de stockage que vous avez créé à l’étape précédente. Ouvrez la page **Vue d’ensemble**, puis la vignette **Objets blob**.  

5. À partir de la page **Service blob**, sélectionnez **Conteneur**, saisissez un **nom** pour votre conteneur, tel que *conteneur1*, définissez le paramètre **Niveau d’accès public** sur Privé (aucun accès anonyme) et sélectionnez **OK**.  

   ![Créez un conteneur.](./media/stream-analytics-quick-create-portal/create-a-storage-container.png)

6. Accédez au conteneur que vous avez créé à l’étape précédente. Sélectionnez **Charger** et chargez les données de capteur que vous avez obtenues à la première étape.  

   ![Charger les exemples de données dans le stockage blob](./media/stream-analytics-quick-create-portal/upload-sample-data-to-blob.png)

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Connectez-vous au portail Azure.

2. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.  

3. Dans la liste des résultats, sélectionnez **Data+Analytics (Données+Analytics)** > **Travail Stream Analytics**.  

4. Renseignez les informations suivantes dans la page du travail Stream Analytics :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Nom du travail   |  myasajob   |   Saisissez un nom pour identifier votre travail Stream Analytics. Le nom d’un travail Stream Analytics peut contenir uniquement des caractères alphanumériques, des traits d’union et des traits de soulignement, et doit avoir entre 3 et 63 caractères. |
   |Abonnement  | \<Votre abonnement\> |  Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour ce travail. |
   |Groupe de ressources   |   asaquickstart-resourcegroup  |   Sélectionnez **Créer** et saisissez le nom du nouveau groupe de ressources pour votre compte. |
   |Emplacement  |  \<Sélectionnez la région la plus proche de vos utilisateurs\> | Sélectionnez l’emplacement géographique où vous pouvez héberger votre travail Stream Analytics. Utilisez l’emplacement le plus proche de vos utilisateurs pour bénéficier de meilleures performances et réduire les coûts de transfert de données. |
   |Unités de diffusion en continu  | 1  |   Les unités de streaming sont les ressources de calcul requises pour exécuter un travail. Par défaut, cette valeur est définie sur 1. Pour en savoir plus sur la mise à l’échelle des unités de streaming, consultez l’article [Understanding and adjusting streaming units](stream-analytics-streaming-unit-consumption.md) (Présentation et réglage des unités de streaming).   |
   |Environnement d’hébergement  |  Cloud  |   Les travaux Stream Analytics peuvent être déployés dans le cloud ou sur des appareils Edge. L’option Cloud vous permet de déployer votre travail dans le cloud Azure, et l’option Edge sur un appareil IoT Edge. |

   ![Créer un travail](./media/stream-analytics-quick-create-portal/create-job.png)

5. Cochez la case **Épingler au tableau de bord** pour placer votre travail sur votre tableau de bord, puis sélectionnez **Créer**.  

6. Vous devriez voir le message « Déploiement en cours... » s’afficher en haut à droite de la fenêtre de votre navigateur. 

## <a name="configure-input-to-the-job"></a>Configurer l’entrée du travail

Dans cette section, vous allez configurer le stockage Blob comme entrée pour le travail Stream Analytics. Avant de configurer l’entrée, créez un compte de stockage Blob.  

### <a name="add-the-input"></a>Ajouter l’entrée 

1. Accédez à votre travail Stream Analytics.  

2. Sélectionnez **Entrées** > **Ajouter une entrée de flux** > **Stockage Blob**.  

3. Indiquez les valeurs suivantes dans la page **Stockage Blob** :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Alias d’entrée  |  BlobInput   |  Saisissez un nom pour identifier l’entrée du travail.   |
   |Abonnement   |  \<Votre abonnement\> |  Sélectionnez l’abonnement Azure contenant le compte de stockage que vous avez créé. Le compte de stockage peut être dans le même abonnement ou dans un abonnement distinct. Cet exemple suppose que vous avez créé le compte de stockage dans le même abonnement. |
   |Compte de stockage  |  myasastorageaccount |  Sélectionnez ou saisissez le nom du compte de stockage. Les noms de compte de stockage sont automatiquement détectés s’ils sont créés dans le même abonnement. |
   |Conteneur  | conteneur1 | Choisissez le nom du conteneur qui contient les exemples de données. Les noms de conteneur sont automatiquement détectés s’ils sont créés dans le même abonnement. |

4. Conservez les valeurs par défaut pour les autres options et sélectionnez **Enregistrer** pour enregistrer les paramètres.  

   ![Configurer les données d’entrée](./media/stream-analytics-quick-create-portal/configure-input.png)
 
## <a name="configure-output-to-the-job"></a>Configurer la sortie du travail

1. Accédez au travail Stream Analytics que vous avez créé précédemment.  

2. Sélectionnez **Sorties > Ajouter > Stockage Blob**.  

3. Indiquez les valeurs suivantes dans la page **Stockage Blob** :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Alias de sortie |   BlobOutput   |   Saisissez un nom pour identifier la sortie du travail. |
   |Abonnement  |  \<Votre abonnement\>  |  Sélectionnez l’abonnement Azure contenant le compte de stockage que vous avez créé. Le compte de stockage peut être dans le même abonnement ou dans un abonnement distinct. Cet exemple suppose que vous avez créé le compte de stockage dans le même abonnement. |
   |Compte de stockage |  asaquickstartstorage |   Sélectionnez ou saisissez le nom du compte de stockage. Les noms de compte de stockage sont automatiquement détectés s’ils sont créés dans le même abonnement.       |
   |Conteneur |   conteneur1  |  Sélectionnez un conteneur existant que vous avez créé dans votre compte de stockage.   |
   |Modèle de chemin d’accès |   sortie  |  Entrez un nom qui servira de chemin d’accès au sein de votre conteneur existant pour la sortie.   |

4. Conservez les valeurs par défaut pour les autres options et sélectionnez **Enregistrer** pour enregistrer les paramètres.  

   ![Configurer la sortie](./media/stream-analytics-quick-create-portal/configure-output.png)
 
## <a name="define-the-transformation-query"></a>Définir la requête de transformation

1. Accédez au travail Stream Analytics que vous avez créé précédemment.  

2. Sélectionnez **Requête** et mettez à jour la requête tel que suit :  

   ```sql
   SELECT 
   System.Timestamp AS OutputTime,
   dspl AS SensorName,
   Avg(temp) AS AvgTemperature
   INTO
     BlobOutput
   FROM
     BlobInput TIMESTAMP BY time
   GROUP BY TumblingWindow(second,30),dspl
   HAVING Avg(temp)>100
   ```

3. Dans cet exemple, la requête lit les données à partir du stockage Blob et les copie dans un nouveau fichier dans le stockage Blob. Sélectionnez **Enregistrer**.  

   ![Configurer la transformation du travail](./media/stream-analytics-quick-create-portal/configure-job-transformation.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Démarrer le travail Stream Analytics et observer le résultat

1. Revenez à la page de vue d’ensemble du travail et sélectionnez **Démarrer**.

2. Sous **Démarrage du travail**, sélectionnez **Personnalisée** pour le champ **Heure de démarrage**. Sélectionnez `2018-01-24` comme date de début, mais ne modifiez pas l’heure. Cette date de début est choisie car elle précède l’horodatage de l’événement à partir de l’échantillon de données. Lorsque vous avez terminé, sélectionnez **Démarrer**.

   ![Démarrage du travail](./media/stream-analytics-quick-create-portal/start-the-job.png)

3. Après quelques minutes, dans le portail, recherchez le compte de stockage et le conteneur que vous avez configuré en tant que sortie pour le travail. Sélectionnez le chemin de sortie. Vous pouvez maintenant voir le fichier de sortie dans le conteneur. La première fois, le travail prend quelques minutes à démarrer. Une fois qu’il a démarré, il continuera à s’exécuter tant que des données arrivent.  

   ![Sortie transformée](./media/stream-analytics-quick-create-portal/transformed-output.png)

## <a name="clean-up-resources"></a>Supprimer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le travail de streaming et toutes les ressources associées. La suppression du travail évite la facturation des unités de streaming consommées par le travail. Si vous envisagez d’utiliser le travail à l’avenir, vous pouvez l’arrêter et le redémarrer plus tard lorsque vous en avez besoin. Si vous ne pensez pas continuer à utiliser ce travail, supprimez toutes les ressources créées pendant ce guide de démarrage rapide en procédant comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée.  

2. Sur la page de votre groupe de ressources, sélectionnez **Supprimer**, saisissez le nom de la ressource à supprimer dans la zone de texte, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un travail Stream Analytics simple. Pour savoir comment configurer d’autres sources d’entrée et effectuer une détection en temps réel, passez à l’article suivant :

> [!div class="nextstepaction"]
> [Détection des fraudes en temps réel à l’aide d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)

