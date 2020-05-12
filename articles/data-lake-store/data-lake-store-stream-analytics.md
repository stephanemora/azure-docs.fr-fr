---
title: Diffuser en continu des données de Stream Analytics vers Data Lake Storage Gen1 – Azure
description: Utilisez Azure Stream Analytics pour diffuser des données vers Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: f1740d167bedd20f51ad5bf24a56b7e7e787f754
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690977"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Diffuser des données à partir d’Azure Storage Blob dans Azure Data Lake Storage Gen1 à l’aide d’Azure Stream Analytics
Cet article explique comment utiliser Azure Data Lake Storage Gen1 comme sortie pour une tâche Azure Stream Analytics. Cet article présente un scénario simple qui consiste à lire des données à partir d’un objet blob Azure Storage (entrée) et à écrire les données dans Data Lake Storage Gen1 (sortie).

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Stockage Azure**. Vous allez utiliser un conteneur d’objets blob à partir de ce compte pour entrer des données pour une tâche Stream Analytics. Ce didacticiel part du principe que vous disposez d’un compte de stockage nommé **storageforasa** et d’un conteneur dans ce compte nommé **storageforasacontainer**. Une fois que vous avez créé le conteneur, chargez-y un fichier d’exemples de données. 
  
* **Un compte Data Lake Storage Gen1**. Suivez les instructions de [Prise en main d’Azure Data Lake Storage Gen1 avec le portail Azure](data-lake-store-get-started-portal.md). Imaginons que vous possédiez un compte Data Lake Storage Gen1 appelé **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Créer un objet blob Stream Analytics
Pour commencer, vous allez créez une tâche Stream Analytics qui inclut une source d’entrée et une destination de sortie. Pour les besoins de ce didacticiel, la source est un conteneur d’objets blob Azure et la destination est Lake Data Storage Gen1.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le volet gauche, cliquez sur **Tâches Stream Analytics**, puis sur **Ajouter**.

    ![Création d’un travail Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Création d’un travail Stream Analytics")

    > [!NOTE]
    > Vérifiez que vous créez le travail dans la même région que le compte de stockage ou le transfert des données entre les régions génèrera des coûts supplémentaires.
    >

## <a name="create-a-blob-input-for-the-job"></a>Créer une entrée d’objets Blob pour la tâche

1. Ouvrez la page du travail Stream Analytics et, dans le volet gauche, cliquez sur l’onglet **Entrées**, puis sur **Ajouter**.

    ![Ajout d’une entrée au travail](./media/data-lake-store-stream-analytics/create.input.1.png "Ajouter une entrée à votre tâche")

2. Dans le panneau **Nouvelle entrée**, fournissez les valeurs suivantes.

    ![Ajout d’une entrée au travail](./media/data-lake-store-stream-analytics/create.input.2.png "Ajouter une entrée à votre tâche")

   * Pour **Alias d’entrée**, entrez un nom unique pour l’entrée de travail.
   * Pour **Type de source**, sélectionnez **Flux de données**.
   * Pour **Source**, sélectionnez **Stockage d’objets blob**.
   * Pour **Abonnement**, sélectionnez **Utiliser l'objet blob de stockage de l'abonnement actuel**.
   * Pour **Compte de stockage**, sélectionnez le compte de stockage que vous avez créé dans le cadre des conditions préalables. 
   * Pour **Conteneur**, sélectionnez le conteneur que vous avez créé dans le compte de stockage sélectionné.
   * Pour **Format de sérialisation de l’événement**, sélectionnez **CSV**.
   * Pour **Délimiteur**, sélectionnez **tabulation**.
   * Pour **Encodage**, sélectionnez **UTF-8**.

     Cliquez sur **Créer**. Le portail ajoute désormais l’entrée et teste la connexion à celle-ci.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Créer une sortie Data Lake Storage Gen1 pour la tâche

1. Ouvrez la page du travail Stream Analytics, cliquez sur l’onglet **Sorties**, puis sur **Ajouter** et sélectionnez **Data Lake Storage Gen1**.

    ![Ajout d’une sortie au travail](./media/data-lake-store-stream-analytics/create.output.1.png "Ajouter une sortie à votre tâche")

2. Dans le panneau **Nouvelle sortie**, fournissez les valeurs suivantes.

    ![Ajout d’une sortie au travail](./media/data-lake-store-stream-analytics/create.output.2.png "Ajouter une sortie à votre tâche")

    * Dans la zone **Alias de sortie**, entrez un nom unique pour la sortie de travail. Il s’agit du nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce compte Data Lake Storage Gen1.
    * Vous êtes invité à autoriser l’accès au compte Data Lake Storage Gen1. Cliquez sur **Autoriser**.

3. Dans le panneau **Nouvelle sortie**, continuez à fournir les valeurs suivantes.

    ![Ajout d’une sortie au travail](./media/data-lake-store-stream-analytics/create.output.3.png "Ajouter une sortie à votre tâche")

   * Pour **Nom du compte**, sélectionnez le compte Data Lake Storage Gen1 que vous avez déjà créé et auquel vous voulez envoyer la sortie de travail.
   * Pour **Modèle de préfixe de chemin d’accès**, entrez un chemin où écrire vos fichiers dans le compte Data Lake Storage Gen1 spécifié.
   * Pour **Format de la date**, si vous avez utilisé un jeton de date dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés.
   * Pour **Format de l’heure**, si vous avez utilisé un jeton d’heure dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés.
   * Pour **Format de sérialisation de l’événement**, sélectionnez **CSV**.
   * Pour **Délimiteur**, sélectionnez **tabulation**.
   * Pour **Encodage**, sélectionnez **UTF-8**.
    
     Cliquez sur **Créer**. Le portail ajoute désormais la sortie et teste la connexion à celle-ci.
    
## <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

1. Pour exécuter un travail Stream Analytics, vous devez exécuter une requête à partir de l’onglet **Requête**. Pour ce didacticiel, vous pouvez exécuter l’exemple de requête en remplaçant les espaces réservés par les alias d’entrée et de sortie de la tâche, comme illustré dans la capture d’écran ci-dessous.

    ![Exécution de la requête](./media/data-lake-store-stream-analytics/run.query.png "Exécuter une requête")

2. Cliquez sur **Enregistrer** dans le haut de l’écran, puis, sous l’onglet **Vue d'ensemble**, cliquez sur **Démarrer**. Dans la boîte de dialogue, sélectionnez **Heure personnalisée**, puis définissez l’heure et la date actuelles.

    ![Définition de l’heure du travail](./media/data-lake-store-stream-analytics/run.query.2.png "Définir l’heure de la tâche")

    Cliquez sur **Démarrer** pour démarrer le travail. Le démarrage de la tâche peut prendre quelques minutes.

3. Pour déclencher le travail permettant de sélectionner les données à partir de l’objet blob, copiez un exemple de fichier de données vers le conteneur d’objets blob. Vous pouvez obtenir un fichier d’exemples de données à partir du [référentiel Git d’Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Pour ce didacticiel, nous allons copier le fichier **vehicle1_09142014.csv**. Vous pouvez utiliser différents clients, comme [Explorateur de stockage Azure](https://storageexplorer.com/), pour charger des données dans un conteneur d’objets blob.

4. Sous l’onglet **Vue d’ensemble**, sous **Surveillance**, vérifiez le mode de traitement des données.

    ![Monitoring du travail](./media/data-lake-store-stream-analytics/run.query.3.png "Surveiller la tâche")

5. Enfin, vous pouvez vérifier que les données de sortie du travail sont disponibles dans le compte Data Lake Storage Gen1. 

    ![Vérification de la sortie](./media/data-lake-store-stream-analytics/run.query.4.png "Vérifier la sortie")

    Dans le volet Explorateur de données, notez que la sortie est écrite dans le chemin d’un dossier, comme indiqué dans les paramètres de sortie Data Lake Storage Gen1 (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Voir aussi
* [Créer un cluster HDInsight pour utiliser Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
