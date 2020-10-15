---
title: Diffuser en continu des données de la supervision à distance à Data Lake Store – Azure | Microsoft Docs
description: Découvrez comment intégrer la solution de supervision à distance à Azure Data Lake Store à l’aide d’une tâche Azure Stream Analytics.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: bbd895686b52ae7d1ced00b635d4d33e93c970a7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069843"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Intégrer la solution de supervision à distance à Azure Data Lake Store

La solution de supervision à distance ne répond peut-être pas à tous vos besoins en matière d’analytique avancée. Azure Data Lake Store est idéal pour cette application, car il peut stocker des données provenant de jeux de données volumineux et variés, et s’intégrer à Azure Data Lake Analytics pour fournir un service d’analytique à la demande.

Dans cette procédure, vous allez utiliser une tâche Azure Stream Analytics pour transmettre des données du hub IoT dans votre solution de supervision à distance à Azure Data Lake Store.

## <a name="prerequisites"></a>Conditions préalables requises

Pour réaliser cette procédure, vous avez besoin des éléments suivants :

* [Déployer l’accélérateur de solution de supervision à distance](quickstart-remote-monitoring-deploy.md).
  * La solution de supervision à distance déploie le hub IoT et la tâche Azure Stream Analytics utilisés dans cet article dans votre abonnement Azure.
* [Déployer un service Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Data Lake Store doit être déployé sur la même région que votre solution de supervision à distance.
  * [Créez un dossier](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) nommé « streaming » dans votre compte.

## <a name="create-a-consumer-group"></a>Créer un groupe de consommateurs

Créez un groupe de consommateurs dédié dans le hub IoT de votre solution de supervision à distance. Il sera utilisé par la tâche Stream Analytics pour le streaming des données dans Data Lake Store.

> [!NOTE]
> Les groupes de consommateurs sont utilisés par les applications pour extraire des données de l’instance Azure IoT Hub. Vous devez créer un autre groupe de consommateurs tous les cinq consommateurs de sortie. Vous pouvez créer jusqu’à 32 groupes de consommateurs.

1. Connectez-vous au portail Azure.

1. Dans le portail Azure, cliquez sur le bouton **Cloud Shell**.

    ![Icône de lancement de portail](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Exécutez la commande suivante pour créer un groupe de consommateurs :

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Utilisez les noms de groupe de ressources et de hub IoT issus de votre solution de supervision à distance.

## <a name="create-stream-analytics-job"></a>Créer une tâche Stream Analytics

Créez une tâche Azure Stream Analytics pour transmettre les données du hub IoT à Azure Data Lake Store.

1. Cliquez sur **Créer une ressource**, sélectionnez Internet des objets à partir de la Place de marché, puis cliquez sur **Tâche Stream Analytics**.

    ![Nouvelle tâche Stream Analytics](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Entrez un nom de tâche et sélectionnez le groupe de ressources ainsi que l’abonnement appropriés.

1. Sélectionnez un emplacement proche ou dans la même région que Data Lake Store. Nous utilisons ici USA Est.

1. Veillez à conserver l’environnement d’hébergement **Cloud** par défaut.

1. Cliquez sur **Créer**.

    ![Créer une tâche Stream Analytics](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configurer la tâche Stream Analytics

1. Accédez à **Tâche Stream Analytics** dans votre groupe de ressources de solution de supervision à distance.

1. Dans la page Vue d’ensemble, cliquez sur **Entrées**.

    ![Page Vue d’ensemble](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Cliquez sur **Ajouter une entrée de flux** et sélectionnez **Hub IoT** dans la liste déroulante.

    ![Ajouter une entrée](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Sous l’onglet Nouvelle entrée, entrez un alias d’entrée d’**IoTHub**.

1. Dans la liste déroulante Groupe de consommateurs, sélectionnez le groupe de consommateurs que vous avez créé précédemment. Nous utilisons ici **streamanalyticsjob**.

    ![Sélectionner l’entrée](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Cliquez sur **Enregistrer**.

1. Dans la page Vue d’ensemble, cliquez sur **Sorties**.

    ![Ajouter Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Cliquez sur **Ajouter** et sélectionnez **Data Lake Store** dans la liste déroulante.

    ![Ajouter une sortie](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Sous l’onglet Nouvelle sortie, entrez un alias de sortie de **DataLakeStore**.

1. Sélectionnez le compte Data Lake Store que vous avez créé dans les étapes précédentes et indiquez une structure de dossiers pour transmettre les données au magasin.

1. Dans le champ Format de date, entrez **/streaming/{date}/{time}** . Laissez le format de date YYYY/MM/DD et le format d’heure HH par défaut.

    ![Indiquer une structure de dossiers](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Cliquez sur **Autoriser**.

    Vous devez avoir des autorisations sur Data Lake Store pour accorder à la tâche Stream Analytics un accès en écriture au système de fichiers.

    ![Autoriser Stream Analytics sur Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Une fenêtre contextuelle s’affiche et, quand elle se ferme, le bouton Autoriser est grisé une fois que l’autorisation a pris fin.

    > [!NOTE]
    > Si vous voyez une erreur dans la fenêtre contextuelle, ouvrez une nouvelle fenêtre de navigateur en mode incognito, puis réessayez.

1. Cliquez sur **Enregistrer**.

## <a name="edit-the-stream-analytics-query"></a>Modifier la requête Stream Analytics

Azure Stream Analytics utilise un langage de requête de type SQL pour spécifier une source d’entrée qui transmet des données, transformer ces données selon les besoins et les externaliser vers diverses destinations de stockage ou de traitement.

1. Sous l’onglet Vue d’ensemble, cliquez sur **Modifier la requête**.

    ![Modifier la requête](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. Dans l’éditeur de requête, remplacez les espaces réservés [YourOutputAlias] et [YourInputAlias] par les valeurs précédemment définies.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Requête Stream Analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Cliquez sur **Enregistrer**.
1. Cliquez sur **Oui** pour accepter les modifications.

## <a name="start-the-stream-analytics-job"></a>Démarrage de la tâche Stream Analytics

1. Sous l’onglet Vue d’ensemble, cliquez sur **Démarrer**.

    ![Démarre une tâche Stream Analytics.](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Sous l’onglet Démarrer le travail, cliquez sur **Personnalisé**.

1. Définissez une heure personnalisée pour revenir en arrière de quelques heures et récupérer les données à partir du moment où votre appareil a démarré le streaming.

1. Cliquez sur **Start**.

    ![Choisir une date personnalisée](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Attendez que le travail soit en cours d’exécution. Si vous constatez des erreurs, elles peuvent provenir de votre requête ; vérifiez que la syntaxe est correcte.

    ![Travail en cours d’exécution](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Le travail de streaming commence à lire les données de votre hub IoT et stocke les données dans Data Lake Store. Quelques minutes peuvent s’écouler avant que les données ne commencent à apparaître dans Data Lake Store.

## <a name="explore-the-streaming-data"></a>Explorer les données de streaming

1. Accédez à Data Lake Store.

1. Sous l’onglet Vue d’ensemble, cliquez sur **Explorateur de données**.

1. Dans l’Explorateur de données, descendez dans la hiérarchie jusqu’au dossier **/streaming**. Vous verrez des dossiers créés avec le format YYYY/MM/DD/HH.

    ![Explorer les données de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Vous verrez des fichiers json avec un fichier par heure.

    ![Explorer les données de streaming](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Étapes suivantes

Azure Data Lake Analytics peut être utilisé pour effectuer une analyse de big data sur vos jeux de données Data Lake Store. Explorez la [Documentation Data Lake Analytics](../data-lake-analytics/index.yml) pour en savoir plus.