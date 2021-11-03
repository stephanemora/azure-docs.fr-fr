---
title: Visualisation en temps réel des événements d’inférence IA avec Power BI
description: Vous pouvez utiliser Azure Video Analyzer pour un enregistrement vidéo en continu ou un enregistrement basé sur les événements. Ce tutoriel vous guide tout au long des étapes de visualisation en temps réel des événements d’inférence IA depuis IoT Hub dans Microsoft Power BI.
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 09/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9359d51e1767793e1d838777cb76a3af04f30c6d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073684"
---
# <a name="tutorial-real-time-visualization-of-ai-inference-events-with-power-bi"></a>Tutoriel : Visualisation en temps réel des événements d’inférence IA avec Power BI

Azure Video Analyzer offre la possibilité de capturer, enregistrer et analyser des vidéos en direct, ainsi que de publier les résultats d’analyse des vidéos sous forme d’événements d’inférence IA sur le [hub IoT Edge](../../iot-edge/iot-edge-runtime.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub). Ces événements d’inférence IA peuvent ensuite être routés vers d’autres destinations, notamment Visual Studio Code et des services Azure comme Time Series Insights et Event Hubs.

Les tableaux de bord constituent un outil intelligent pour superviser votre activité et visualiser toutes vos métriques importantes en un clin d’œil. Vous pouvez visualiser les événements d’inférence IA générés par Video Analyzer à l’aide de [Microsoft Power BI](https://powerbi.microsoft.com/) par le biais d’[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) de sorte à obtenir rapidement des insights et à partager des tableaux de bord avec vos collègues au sein de votre organisation.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/tutorial-block-diagram.svg" alt-text="Schéma fonctionnel de connexion d’Azure Video Analyzer à Microsoft Power BI par le biais d’Azure Stream Analytics.":::

Ce didacticiel présente les procédures suivantes :

- Créer et exécuter une tâche Stream Analytics pour récupérer des données nécessaires auprès d’IoT Hub et les envoyer à Power BI
- Exécuter un pipeline en direct qui génère des événements d’inférence
- Créer un tableau de bord Power BI pour visualiser les inférences IA

## <a name="suggested-pre-reading"></a>Lecture préalable suggérée

- [Supervision et journalisation](monitor-log-edge.md) dans Video Analyzer
- Lecture [des messages appareil-à-cloud depuis les points de terminaison intégrés IoT Hub](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)
- Introduction aux [tableaux de bord Power BI](/power-bi/create-reports/service-dashboards)

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ce tutoriel repose sur l’utilisation de l’[exemple de franchissement de ligne](use-line-crossing.md) pour détecter le moment où des objets franchissent une ligne virtuelle dans un flux vidéo en direct. Vous pouvez choisir de créer une visualisation pour d’autres pipelines : **un pipeline avec un récepteur de messages IoT Hub est nécessaire**. Vérifiez que le pipeline en direct est créé, mais activez-le uniquement après avoir créé une tâche Stream Analytics.

  > [!TIP]
  >
  > - L’[exemple de franchissement de ligne](use-line-crossing.md) utilise un enregistrement vidéo de 5 minutes. Pour obtenir de meilleurs résultats de visualisation, utilisez l’enregistrement des véhicules sur autoroute d’une durée de 60 minutes disponible sous [Other dataset](https://github.com/Azure/video-analyzer/tree/main/media#other-dataset) (Autre jeu de données).
  > - Consultez la section Configuration et déploiement dans les [questions fréquentes (FAQ)](faq-edge.yml) pour savoir comment ajouter des exemples de fichiers vidéo au simulateur rtsp. Une fois ajoutés, modifiez la valeur `rtspUrl` pour pointer vers le nouveau fichier vidéo.
  > - Si vous avez suivi l’exemple de franchissement de ligne et que vous utilisez le [référentiel d’exemples C# AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp), remplacez le fichier operations.json situé à l’emplacement propriétés-> paramètres-> valeur par `"rtsp://rtspsim:554/media/camera-3600s.mkv"` pour changer la source vidéo par un enregistrement de 60 minutes.

- Un compte [Power BI](https://powerbi.microsoft.com/).

## <a name="create-and-run-a-stream-analytics-job"></a>Créer et exécuter une tâche Stream Analytics

[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) est un service d’analytique en temps réel entièrement managé. Il est conçu pour vous aider à analyser et à traiter des flux de données à grande vitesse. Dans cette section, vous allez créer une tâche Stream Analytics, définir les entrées, les sorties et la requête utilisées pour récupérer les données nécessaires.

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Créer une ressource**. Tapez _Tâche Stream Analytics_ dans la zone de recherche et sélectionnez-la dans la liste déroulante. Dans le volet de vue d’ensemble de la **tâche Stream Analytics**, sélectionnez **Créer**.
2. Entrez les informations suivantes pour la tâche :

   - **Nom du travail** : Nom du travail. Le nom doit être globalement unique.
   - **Abonnement** : Choisissez le même abonnement que celui utilisé pour configurer l’exemple de franchissement de ligne.
   - **Groupe de ressources** : Utilisez le même groupe de ressources que celui utilisé par votre hub IoT Edge dans le cadre de la configuration de l’exemple de franchissement de ligne.
   - **Emplacement** : utilisez le même emplacement que votre groupe de ressources.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-asa-job.png" alt-text="Capture d’écran de la création d’une tâche Stream Analytics.":::

3. Sélectionnez **Create** (Créer).

### <a name="add-an-input-to-the-stream-analytics-job"></a>Ajouter une entrée à la tâche Stream Analytics

1. Ouvrez le travail Stream Analytics.
2. Sous Topologie de la tâche, sélectionnez **Entrées**.
3. Dans le volet Entrées, sélectionnez **Ajouter une entrée de flux**, puis sélectionnez **IoT Hub** dans la liste déroulante. Dans le volet de la nouvelle entrée, indiquez les informations suivantes :

   - **Alias de l’entrée** : Entrez un alias unique pour l’entrée, par exemple « iothubinput ».
   - **Sélectionner un hub IoT dans vos abonnements** : sélectionnez cette case d’option.
   - **Abonnement** : Sélectionnez l’abonnement Azure que vous utilisez pour cet article.
   - **IoT Hub** : Sélectionnez le hub IoT utilisé lors de la configuration de l’exemple de franchissement de ligne.
   - **Nom de la stratégie d'accès partagé** : sélectionnez le nom de la stratégie d’accès partagé que vous souhaitez voir utiliser par le travail Stream Analytics. Pour ce tutoriel, vous pouvez sélectionner iothubowner. Pour plus d’informations, consultez [Contrôle d’accès et autorisations](../../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
   - **Clé de la stratégie d’accès partagé** : Ce champ est automatiquement renseigné en fonction de votre sélection du nom de la stratégie d’accès partagé.

   Laissez tous les autres champs définis sur leurs valeurs par défaut.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-iothub-input.png" alt-text="Capture d’écran de l’ajout d’une entrée IoT Hub à une tâche Stream Analytics.":::

4. Sélectionnez **Enregistrer**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Ajouter une sortie à la tâche Stream Analytics

1. Sous Topologie de la tâche, sélectionnez **Sorties**.
2. Dans le volet Sorties, sélectionnez **Ajouter**, puis sélectionnez **Power BI** dans la liste déroulante.
3. Dans le volet Power BI – Nouvelle sortie :

   - **Alias de sortie** : Entrez un alias unique pour la sortie, par exemple « powerbioutput ».
   - **Espace de travail de groupe** : sélectionnez l’espace de travail de votre groupe cible.
   - **Mode d’authentification** : Laissez la valeur par défaut « Jeton utilisateur » à des fins de test.

   > [!NOTE]
   > Pour les tâches de production, nous vous recommandons d’[utiliser une identité managée pour authentifier votre tâche Stream Analytics auprès de Power BI](../../stream-analytics/powerbi-output-managed-identity.md).

   - **Nom du jeu de données** : entrez un nom de jeu de données.
   - **Nom de la table** : Saisissez le nom de la table.
   - **Autoriser** : Sélectionnez Autoriser et suivez les invites pour vous connecter à votre compte Power BI (le jeton est valide pendant 90 jours).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-pbi-output.png" alt-text="Capture d’écran de l’ajout d’une sortie Power BI à une tâche Stream Analytics.":::

4. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > Pour plus d’informations sur l’utilisation de Power BI comme sortie d’une tâche Stream Analytics, cliquez [ici](../../stream-analytics/power-bi-output.md). Découvrez-en plus sur le [renouvellement de l’authentification](../../stream-analytics/stream-analytics-power-bi-dashboard.md#renew-authorization) pour votre compte Power BI.

### <a name="configure-the-query-for-stream-analytics-job"></a>Configurer la requête pour la tâche Stream Analytics

1. Sous Topologie de la tâche, sélectionnez **Requête**.
2. Apportez les modifications suivantes à votre requête :

   ```SQL
   SELECT
       CAST(InferenceRecords.ArrayValue.event.properties.total AS bigint) as EventTotal,
       CAST(i.EventProcessedUtcTime AS datetime) as EventProcessedUtcTime
   INTO [YourOutputAlias]
   FROM [YourInputAlias] i
   CROSS APPLY GetArrayElements(inferences) AS InferenceRecords
   WHERE InferenceRecords.ArrayValue.subType = 'lineCrossing'
   ```

   > [!NOTE]
   >
   > - Dans la requête ci-dessus, la lettre _i_ de la clause FROM est syntaxiquement nécessaire pour récupérer la valeur de EventProcessedUtcTime qui n’est pas imbriquée dans le tableau des _inférences_.
   >   La requête ci-dessus est personnalisée pour obtenir des inférences IA pour le [tutoriel de franchissement de ligne](use-line-crossing.md).
   > - Si vous exécutez un autre pipeline, veillez à personnaliser la requête en fonction du schéma d’inférence IA du pipeline. Découvrez-en plus sur l’[analyse JSON dans une tâche Stream Analytics](../../stream-analytics/stream-analytics-parsing-json.md).

3. Remplacez [YourOutputAlias] par l’alias de sortie utilisé à l’étape de l’ajout d’une sortie à la tâche Stream Analytics, par exemple « powerbioutput ». Notez la séquence appropriée des alias de sortie et d’entrée.
4. Remplacez [YourInputAlias] par l’alias d’entrée utilisé à l’étape de l’ajout d’une entrée à la tâche Stream Analytics, par exemple « iothubinput ».
5. Votre requête doit ressembler à la capture d’écran suivante. Cliquez sur **Tester la requête** pour vérifier les résultats de test présentés sous la forme d’une table de EventTotal et des valeurs EventProcessedUtcTime correspondantes.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/asa-query.png" alt-text="Capture d’écran du test et de l’enregistrement d’une requête dans la tâche Stream Analytics.":::

6. Sélectionnez **Enregistrer la requête**.

### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

Dans la tâche Stream Analytics, sélectionnez Vue d’ensemble, puis **Démarrer** > **Maintenant** > **Démarrer**. Une fois la tâche lancée, l’état correspondant passe de Créé à **Exécution**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/start-asa.png" alt-text="Capture d’écran du démarrage et de l’exécution d’une tâche Stream Analytics.":::

> [!TIP]
> IoT Hub permet de conserver les données dans les Event Hubs intégrés pendant 1 jour et pendant 7 jours maximum. Vous pouvez définir la durée de conservation lors de la création de votre IoT Hub. La durée de conservation des données dans IoT Hub dépend du niveau et du type d’unité que vous avez choisis. Cliquez [ici](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) pour plus d’informations. Pour conserver les données plus longtemps, utilisez [Stockage Azure comme sortie](../../stream-analytics/blob-storage-azure-data-lake-gen2-output.md), puis connectez Power BI aux fichiers situés dans le compte de stockage.

## <a name="run-a-sample-pipeline"></a>Exécuter un exemple de pipeline

Quand l’état de la tâche Stream Analytics créée à l’étape ci-dessus est **Exécution**, accédez à la section [Exécuter l’exemple de programme](use-line-crossing.md#run-the-sample-program) du tutoriel de franchissement de ligne et activez le pipeline en direct. Le pipeline en direct va commencer à envoyer des résultats d’inférence IA à IoT Hub, qui sont ensuite récupérés par la tâche Stream Analytics.

## <a name="create-a-power-bi-dashboard-to-visualize-ai-events"></a>Créer un tableau de bord Power BI pour visualiser les événements IA

Dans Power BI, vous pouvez visualiser les données de streaming de deux manières :

1. Créez des rapports à partir de la table créée pour le jeu de données de streaming et épinglez-les au tableau de bord.
2. Utilisez une vignette de tableau de bord avec un jeu de données de streaming personnalisé.

   > [!NOTE]
   > Dans cet article, nous allons utiliser la première méthode pour créer des rapports, puis les épingler au tableau de bord. Cette méthode permet de garder les données sur le visuel plus longtemps et les cumule automatiquement quand elles entrent. Pour en savoir plus sur la seconde méthode, consultez [Configurer votre jeu de données de streaming personnalisé dans Power BI](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi).

### <a name="create-and-publish-a-power-bi-report"></a>Créer et publier un rapport Power BI

Les étapes suivantes vous montrent comment créer et publier un rapport à l’aide du service Power BI.

1. Vérifiez que l’exemple de pipeline est activé sur votre appareil (allumé à l’étape précédente pour exécuter un pipeline).
2. Connectez-vous à votre [compte Power BI](https://powerbi.microsoft.com/) et sélectionnez le **service Power BI** dans le menu du haut.
3. Sélectionnez l’espace de travail que vous avez utilisé dans le menu latéral.
4. Sous l’onglet **Tout** ou l’onglet **Jeux de données + flux de données**, vous devez voir le jeu de données que vous avez spécifié à l’étape _Ajouter une sortie à la tâche Stream Analytics_.
5. Pointez sur le nom du jeu de données que vous avez fourni quand vous avez créé la sortie Stream Analytics, sélectionnez le menu **Plus d’options** (les trois points à droite du nom du jeu de données), puis sélectionnez **Créer un rapport**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-report.png" alt-text="Capture d’écran de la création d’un rapport dans Power BI.":::

6. Créez un graphique en courbes pour afficher les événements de franchissement de ligne en temps réel.

   - Dans le volet **Visualisations** de la page de création du rapport, sélectionnez l’icône **Graphique en courbes** pour ajouter un graphique de ce type.
   - Sur le volet **Champs**, développez la table que vous avez indiquée lors de la création de la sortie du travail Stream Analytics.
   - Faites glisser **EventProcessedUtcTime** vers **Axe** dans le volet **Visualisations**.
   - Faites glisser **EventTotal** vers **Valeurs**.
   - Un graphique en courbes est créé. L’axe des abscisses affiche la date et l’heure du fuseau horaire UTC. L’axe des ordonnées présente les valeurs **Somme** automatiquement calculées de EventTotal issues du pipeline en direct. Sous Valeurs, choisissez **Maximum de EventTotal** pour obtenir la valeur la plus récente de EventTotal. Vous pouvez modifier la fonction d’agrégation pour afficher la moyenne, le nombre (distinct), etc.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/powerbi-report.png" alt-text="Capture d’écran du rapport de franchissement de ligne dans Power BI.":::

7. Enregistrez le rapport en cliquant sur **Enregistrer** en haut à droite.

### <a name="pin-visual-to-a-dashboard"></a>Épingler un visuel sur un tableau de bord

Cliquez sur **Épingler sur un tableau de bord** en haut à droite et indiquez si vous voulez épingler ce visuel sur un tableau de bord existant ou sur un nouveau tableau de bord, puis suivez les invites en conséquence.

Éventuellement, vous pouvez aussi [incorporer un widget de lecteur](embed-player-in-power-bi.md) pour visionner la vidéo à côté du rapport.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/pinned-dashboard.png" alt-text="Tableau de bord Power BI avec un rapport épinglé et un widget de lecteur ajouté dans une vignette.":::

> [!NOTE]
> La lecture de la vidéo et la création du rapport ne seront pas synchronisées en raison de la méthode utilisée pour générer les rapports. Pour obtenir une expérience en quasi-temps réel, consultez [Configurer votre jeu de données de streaming personnalisé dans Power BI](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi).

## <a name="interpret-the-dashboard"></a>Interpréter le tableau de bord

Le nœud de processeur de franchissement de ligne détecte le moment où un objet franchit une ligne spécifiée dans la topologie à l’aide du paramètre lineCoordinates. Quand des objets franchissent ces coordonnées, un événement est déclenché avec :

- EventTotal : Nombre total de franchissements de ligne par tout objet dans n’importe quelle direction (sens horaire ou anti-horaire) depuis le début de la vidéo. Découvrez-en plus sur les [inférences d’événement de franchissement de ligne](use-line-crossing.md#line-crossing-events).
- Heure UTC traitée par l’événement : Heure à laquelle chaque événement a été traité par la tâche Stream Analytics. Vous pouvez aussi personnaliser la requête Stream Analytics pour récupérer EventEnqueuedUtcTime, qui reflète l’heure à laquelle l’événement a atteint le hub IoT prêt pour un traitement supplémentaire.

Dans le tableau de bord ci-dessus, vous pouvez voir un nombre croissant de **EventTotal** au fil du temps, car de plus en plus d’objets franchissent la ligne virtuelle. Cette visualisation vous permet de déterminer rapidement que les véhicules qui passent sur l’autoroute le font plus souvent entre 15h52 et 15h53m30s, par exemple. Vous pouvez ensuite utiliser ces insights pour affiner votre analyse sur les causes des embouteillages à certaines heures sur l’autoroute.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez essayer d’autres guides de démarrage rapide ou tutoriels, conservez les ressources que vous avez créées. Sinon, accédez au portail Azure, accédez à vos groupes de ressources, sélectionnez le groupe de ressources dans lequel vous avez exécuté les prérequis pour cet article et créé la tâche Stream Analytics, puis sélectionnez **Supprimer le groupe de ressources**.

Vous avez créé un jeu de données **LineCrossingDataset** dans Power BI. Pour le supprimer, connectez-vous à votre compte [Power BI](https://powerbi.microsoft.com/). Dans le menu de gauche, sous **Espaces de travail**, sélectionnez l’espace de travail que vous avez utilisé. Dans la liste des jeux de données sous l’onglet **Jeux de données + flux de données**, pointez sur le jeu de données. Sélectionnez les trois points verticaux qui s’affichent à droite du nom du jeu de données pour ouvrir le menu **Plus d’options**, puis sélectionnez **Supprimer** et suivez les invites. Lorsque vous supprimez le jeu de données, le rapport est également supprimé.

## <a name="next-steps"></a>Étapes suivantes

- Combiner la lecture vidéo avec des données de télémétrie en [incorporant un widget de lecteur dans Power BI](embed-player-in-power-bi.md)
- En savoir plus sur la [supervision et la journalisation des événements](monitor-log-edge.md)
