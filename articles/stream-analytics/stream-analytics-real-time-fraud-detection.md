---
title: 'Tutoriel : Analyser les données d’appels frauduleux avec Azure Stream Analytics et visualiser les résultats dans un tableau de bord Power BI'
description: Ce didacticiel démontre de bout en bout comment utiliser Azure Stream Analytics pour analyser les appels frauduleux dans un flux d’appel téléphonique.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018868"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Tutoriel : Analyser les données d’appels frauduleux avec Stream Analytics et visualiser les résultats dans un tableau de bord Power BI

Ce tutoriel montre comment analyser les données d’appel téléphonique à l’aide d’Azure Stream Analytics. Les données d’appel téléphonique, générées par une application cliente, contiennent des appels frauduleux qui sont filtrés par le travail Stream Analytics. Vous pouvez appliquer les techniques décrites dans ce tutoriel pour d’autres types de détection de fraude, tels que la fraude à la carte de crédit ou l’usurpation d’identité.

Dans ce tutoriel, vous allez découvrir comment :

> [!div class="checklist"]
> * Générer des exemples de données d’appel téléphonique et les envoyer à Azure Event Hubs.
> * Créez une tâche Stream Analytics.
> * Configurer les entrées et sorties de travail.
> * Définir une requête pour filtrer les appels frauduleux.
> * Tester et démarrer le travail.
> * Visualiser les résultats dans Power BI.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez avoir suivi les étapes ci-dessous :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).
* Téléchargez l’application de génération d’événements d’appel téléphonique [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) depuis le Centre de téléchargement Microsoft, ou obtenez le code source à partir de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Il vous faut un compte Power BI.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Création d'un hub d'événements Azure

Avant que Stream Analytics puisse analyser le flux de données d’appels frauduleux, les données doivent être envoyées à Azure. Dans ce didacticiel, vous envoyez des données à Azure à l’aide d’[Azure Event Hubs](../event-hubs/event-hubs-about.md).

Suivez les étapes ci-dessous pour créer un Event Hub et envoyer les données d’appel à celui-ci :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource** > **Internet des objets** > **Event Hubs**.

   ![Créer un hub Azure Event Hub dans le portail](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Remplissez le volet **Créer un espace de noms** avec les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée** |**Description**  |
   |---------|---------|---------|
   |Nom     | asaTutorialEventHub        |  Nom unique pour identifier l’espace de noms du hub d’événements.       |
   |Abonnement     |   \<Your subscription\>      |   Sélectionnez un abonnement Azure dans lequel vous souhaitez créer le hub d’événements.      |
   |Resource group     |   MyASADemoRG      |  Sélectionnez **Créer** et saisissez le nom du nouveau groupe de ressources pour votre compte.       |
   |Emplacement     |   USA Ouest 2      |    Emplacement où l’espace de noms du hub d’événements peut être déployé.     |

4. Utilisez les options par défaut pour les autres paramètres, puis sélectionnez **Vérifier + créer**. Ensuite, sélectionnez **Créer** pour démarrer le déploiement.

   ![Créer un espace de noms Event Hub dans le Portail Azure](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Lorsque le déploiement de l’espace de noms est terminé, accédez à **Toutes les ressources**, puis recherchez *asaTutorialEventHub* dans la liste des ressources Azure. Sélectionnez *asaTutorialEventHub* pour l’ouvrir.

6. Ensuite, sélectionnez **+ Hub d’événements** et entrez un **Nom** pour le hub d’événements. Affectez la valeur 2 au **Nombre de partitions**.  Utilisez les options par défaut pour les autres paramètres, puis sélectionnez **Créer**. Ensuite, attendez que le déploiement se termine.

   ![Configuration d’Event Hub dans le Portail Azure](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Accorder l’accès au concentrateur Event Hub et obtenir une chaîne de connexion

Pour qu’une application puisse envoyer des données à Azure Event Hubs, le hub d’événements doit disposer d’une stratégie autorisant l’accès. La stratégie d’accès génère une chaîne de connexion qui inclut des informations d’autorisation.

1. Sélectionnez le hub d’événements que vous avez créé à l’étape précédente : *MyEventHub*. Sélectionnez **Stratégies d’accès partagé** > **Paramètres** > **+ Ajouter**.

2. Nommez la stratégie **MyPolicy**, et vérifiez que la case **Gérer** est cochée. Sélectionnez ensuite **Créer**.

   ![Créer une stratégie d’accès partagé de hub d’événements](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Une fois la stratégie créée, sélectionnez son nom pour l’ouvrir. Recherchez **Chaîne de connexion - Clé primaire**. Sélectionnez le bouton de **copie** situé à côté de la chaîne de connexion.

   ![Enregistrer la chaîne de connexion de stratégie accès partagé](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Collez la chaîne de connexion dans un éditeur de texte. Vous avez besoin de cette chaîne de connexion dans la section suivante.

   La chaîne de connexion ressemble à ceci :

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Notez que la chaîne de connexion contient plusieurs paires clé-valeur, séparées par des points-virgules : **Point de terminaison**, **SharedAccessKeyName**, **SharedAccessKey** et **EntityPath**.

## <a name="start-the-event-generator-application"></a>Démarrer l’application de génération d’événements

Avant de démarrer l’application TelcoGenerator, vous devez la configurer pour envoyer des données aux Azure Event Hubs que vous avez créés précédemment.

1. Extrayez le contenu du fichier [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Ouvrez le fichier `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` dans l’éditeur de texte de votre choix (comme il existe plusieurs fichiers `.config`, veillez à ouvrir celui qui convient).

3. Mettez à jour l’élément `<appSettings>` dans le fichier de configuration avec les détails suivants :

   * Définissez la valeur de la clé *EventHubName* sur la valeur de la paire EntityPath dans la chaîne de connexion.
   * Définissez la valeur de la clé *Microsoft.ServiceBus.ConnectionString* sur la chaîne de connexion sans la valeur EntityPath. N’oubliez pas de supprimer le point-virgule qui précède la valeur EntityPath.

4. Enregistrez le fichier .

5. Ensuite, ouvrez une fenêtre de commandes et accédez au dossier dans lequel l’application TelcoGenerator est décompressée. Puis, entrez la commande suivante :

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Cette commande utilise les paramètres suivants :
   * Nombre d’enregistrements de données d’appel par heure.
   * Pourcentage de probabilité de fraude, qui correspond à la fréquence à laquelle l’application doit simuler un appel frauduleux. La valeur 0,2 signifie qu’environ 20 % des enregistrements d’appels semblent frauduleux.
   * Durée en heures, qui correspond au nombre d’heures pendant lesquelles l’application doit s’exécuter. Vous pouvez également arrêter l’application à tout moment en terminant le processus (**Ctrl+C**) sur la ligne de commande.

   Après quelques secondes, l’application commence à afficher des enregistrements des appels téléphoniques à l’écran à mesure qu’elle les envoie au concentrateur Event Hub. Les données d’appel téléphonique contiennent les champs suivants :

   |**Enregistrement**  |**Définition**  |
   |---------|---------|
   |CallrecTime    |  Horodatage de l’heure de début d’appel.       |
   |SwitchNum     |  Commutateur téléphonique utilisé pour connecter l’appel. Pour cet exemple, les commutateurs sont des chaînes qui représentent le pays/la région d’origine (États-Unis, Chine, Royaume-Uni, Allemagne ou Australie).       |
   |CallingNum     |  Numéro de téléphone de l’appelant.       |
   |CallingIMSI     |  Identité de l’abonné mobile international (IMSI). Il s’agit d’un identificateur unique de l’appelant.       |
   |CalledNum     |   Numéro de téléphone du destinataire de l’appel.      |
   |CalledIMSI     |  Identité de l'abonné mobile international (IMSI). Il s’agit d’un identificateur unique du destinataire de l’appel.       |

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

Maintenant que vous disposez d’un flux d’événements d’appel, vous pouvez créer un travail Stream Analytics qui lit des données à partir du hub d’événements.

1. Pour créer un travail Stream Analytics, accédez au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Créer une ressource**, puis recherchez **Tâche Stream Analytics**. Sélectionnez la vignette **Tâche Stream Analytics**, puis sélectionnez *Créer**.

3. Remplissez le formulaire **Nouvelle tâche Stream Analytics** avec les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Nom du travail     |  ASATutorial       |   Nom unique pour identifier l’espace de noms du hub d’événements.      |
   |Abonnement    |  \<Your subscription\>   |   Sélectionnez un abonnement Azure dans lequel vous souhaitez créer le travail.       |
   |Resource group   |   MyASADemoRG      |   Sélectionnez **Use existing** (Utiliser existant) et saisissez le nom du nouveau groupe de ressources pour votre compte.      |
   |Emplacement   |    USA Ouest 2     |      Emplacement où le travail peut être déployé. Il est recommandé de placer le travail et le hub d’événements dans la même région afin d’optimiser les performances. Ce faisant, vous ne payez pas pour transférer des données entre les régions.      |
   |Environnement d’hébergement    | Cloud        |     Les travaux Stream Analytics peuvent être déployés dans le cloud ou sur des appareils Edge. L’option Cloud vous permet de déployer votre travail dans le cloud Azure, et l’option Edge sur un appareil IoT Edge.    |
   |Unités de diffusion en continu     |    1       |      Les unités de streaming sont les ressources de calcul requises pour exécuter un travail. Par défaut, cette valeur est définie sur 1. Pour en savoir plus sur la mise à l’échelle des unités de streaming, consultez [Understanding and adjusting streaming units](stream-analytics-streaming-unit-consumption.md) (Présentation et réglage des unités de streaming).      |

4. Utilisez les options par défaut pour les autres paramètres, sélectionnez **Créer** et attendez que le déploiement réussisse.

   ![Créer un travail Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Configurer les entrées du travail

L’étape suivante consiste à définir une source d’entrée pour le travail, afin de pouvoir lire les données à l’aide de l’Event Hub que vous avez créé dans la section précédente.

1. Dans le portail Azure, ouvrez la page **Toutes les ressources**, puis recherchez la tâche Stream Analytics *ASATutorial*.

2. Dans la section **Topologie de la tâche** de la tâche Stream Analytics, sélectionnez **Entrées**.

3. Cliquez sur **+ Ajouter une entrée de flux**, puis sur **Event Hub**. Remplissez le formulaire d’entrée avec les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Alias d’entrée     |  CallStream       |  Fournissez un nom convivial pour identifier votre entrée. L’alias d’entrée peut contenir uniquement des caractères alphanumériques, des traits d’union et des traits de soulignement, et doit avoir entre 3 et 63 caractères.       |
   |Abonnement    |   \<Your subscription\>      |   Sélectionnez l’abonnement Azure dans lequel vous avez créé le hub d’événements. Le hub d’événements peut se trouver dans le même abonnement ou dans un autre abonnement que le travail Stream Analytics.       |
   |Espace de noms du hub d’événements    |  asaTutorialEventHub       |  Sélectionnez l’espace de noms du hub d’événements que vous avez créé dans la section précédente. Tous les espaces de noms du hub d’événements disponibles dans votre abonnement actuel sont répertoriés dans la liste déroulante.       |
   |Nom du hub d’événements    |   MyEventHub      |  Sélectionnez le hub d’événements que vous avez créé dans la section précédente. Tous les hubs d’événements disponibles dans votre abonnement actuel sont répertoriés dans la liste déroulante.       |
   |Nom de la stratégie du hub d’événements   |  MyPolicy       |  Sélectionnez la stratégie d’accès partagé du hub d’événements que vous avez créée dans la section précédente. Toutes les stratégies de hub d’événements disponibles dans votre abonnement actuel sont répertoriées dans la liste déroulante.       |

4. Utilisez les options par défaut pour les autres paramètres, puis sélectionnez **Créer**.

   ![Configurer une entrée Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configurer la sortie du travail

La dernière étape consiste à définir un récepteur de sortie dans lequel la tâche peut écrire les données transformées. Dans ce didacticiel, vous générez et visualisez des données avec Power BI.

1. Dans le portail Azure, ouvrez **Toutes les ressources**, puis sélectionnez la tâche Stream Analytics *ASATutorial*.

2. Dans la section **Topologie de la tâche** de la tâche Stream Analytics, sélectionnez l’option **Sorties**.

3. Sélectionnez **+ Ajouter** > **Power BI**. Ensuite, sélectionnez **Autoriser** et suivez les invites pour authentifier Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Bouton Autoriser pour Power BI":::

4. Remplissez le formulaire de sortie avec les valeurs suivantes, puis sélectionnez **Enregistrer** :

   |**Paramètre**  |**Valeur suggérée**  |
   |---------|---------|
   |Alias de sortie  |  MyPBIoutput  |
   |Espace de travail de groupe| Mon espace de travail |
   |Nom du jeu de données  |   ASAdataset  |
   |Nom de la table |  ASATable  |
   | Mode d'authentification | Jeton utilisateur |

   ![Configurer une sortie Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   Ce tutoriel utilise le mode d’authentification par *jeton utilisateur*. Pour utiliser l’identité managée, consultez [Utiliser une identité managée pour authentifier votre tâche Azure Stream Analytics dans Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Créer des requêtes pour transformer des données en temps réel

À ce stade, vous disposez d’un travail Stream Analytics configuré pour lire un flux de données entrantes. L’étape suivante consiste à créer une requête qui analysera les données en temps réel. Les requêtes utilisent un langage de type SQL dont certaines extensions sont propres à Stream Analytics.

Dans cette section du didacticiel, vous allez créer et tester plusieurs requêtes pour découvrir quelques méthodes via lesquelles vous pouvez transformer un flux d’entrée pour l’analyse. 

Les requêtes que vous créez ici affichent uniquement les données transformées à l’écran. Dans une section ultérieure, vous écrirez les données transformées dans Power BI.

Pour plus d’informations sur ce langage, consultez la page [Références sur le langage des requêtes d’Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Procéder à un test à l’aide d’une requête directe

Si vous voulez archiver tous les événements, vous pouvez utiliser une requête directe pour lire tous les champs dans la charge utile de l’événement.

1. Accédez à votre travail Stream Analytics dans le portail Azure, puis sélectionnez **Requête** sous *Topologie de la tâche*. 

2. Dans la fenêtre de requête, entrez la requête suivante :

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Comme dans SQL, les mots clés ne respectent pas la casse, et les espaces blancs ne sont pas significatifs.

    Dans cette requête, `CallStream` est l’alias que vous avez spécifié lorsque vous avez créé l’entrée. Si vous en avez utilisé un autre, utilisez plutôt ce nom.

3. Sélectionnez **Tester la requête**.

    Le travail Stream Analytics exécute la requête par rapport à l’exemple de données fourni en entrée, et affiche la sortie au bas de la fenêtre. Les résultats indiquent que le concentrateur Event Hub et le travail Stream Analytics sont correctement configurés.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Exemple de sortie d’une requête de test":::

    Le nombre exact d’enregistrements que vous pouvez observer dépend du nombre d’enregistrements qui ont été capturés dans l’échantillon.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Réduire le nombre de champs à l’aide d’une projection de colonne

Dans de nombreux cas, il n’est pas nécessaire d’insérer toutes les colonnes du flux d’entrée pour l’analyse. Vous pouvez utiliser une requête pour projeter moins de champs retournés que dans la requête directe.

Exécutez la requête suivante et observez la sortie.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Nombre d’appels entrants par région : Fenêtre bascule avec agrégation

Supposons que vous souhaitiez compter le nombre d’appels entrants par région. Dans les données de flux, lorsque vous souhaitez effectuer des fonctions d’agrégation telles que le comptage, vous devez segmenter le flux en unités temporelles, puisque le flux de données proprement dit est en réalité un nombre infini. Pour ce faire, utilisez une [fonction de fenêtre](stream-analytics-window-functions.md) Stream Analytics. Vous pouvez alors utiliser les données contenues dans cette fenêtre comme unité.

Pour cette transformation, vous souhaitez une séquence de fenêtres temporelles ne se chevauchant pas ; chaque fenêtre contient un ensemble distinct de données que vous pouvez regrouper et agréger. Ce type de fenêtre est appelé *fenêtre bascule*. Dans la fenêtre bascule, vous pouvez obtenir le nombre des appels entrants, regroupés par `SwitchNum`, qui représente le pays/région d’origine de l’appel.

1. Collez la requête ci-après dans l’éditeur de requête :

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Cette requête utilise le mot-clé `Timestamp By` dans la clause `FROM` pour spécifier le champ d’horodatage à utiliser dans le flux d’entrée pour définir la fenêtre bascule. Dans ce cas, la fenêtre divise les données en segments en fonction du champ `CallRecTime` dans chaque enregistrement. (Si aucun champ n’est spécifié, l’opération de fenêtrage utilise l’heure d’arrivée de chaque événement dans le concentrateur Event Hub.) Voir « Heure d’arrivée par rapport à l’heure de l’application » dans [Informations de référence sur le langage de requête Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

    La projection inclut `System.Timestamp`, qui retourne un horodatage pour la fin de chaque fenêtre. 

    Pour spécifier que vous voulez utiliser une fenêtre bascule, vous utilisez la fonction [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) dans la clause `GROUP BY`. Dans la fonction, indiquez une unité de temps (d’une microseconde à un jour) et une taille de fenêtre (nombre d’unités). Dans cet exemple, comme la fenêtre bascule est constituée d’intervalles de 5 secondes, vous obtenez un nombre d’appels par pays/région pour chaque période de 5 secondes.

2. Sélectionnez **Tester la requête**. Dans les résultats, notez que les horodatages indiqués sous **WindowEnd** sont exprimés par incréments de 5 secondes.

### <a name="detect-sim-fraud-using-a-self-join"></a>Détecter une fraude à la carte SIM à l’aide d’une jointure réflexive

Pour cet exemple, envisagez l’utilisation frauduleuse suivante : appels provenant d’un même utilisateur, mais à différents lieux, espacés de 5 secondes à chaque fois. Par exemple, un même utilisateur ne peut pas légitimement passer simultanément un appel depuis les États-Unis et l’Australie.

Pour ces cas, vous pouvez utiliser une jointure réflexive des données de flux pour joindre le flux à lui-même en fonction de la valeur `CallRecTime`. Vous pouvez ensuite rechercher les enregistrements d’appels où la valeur de `CallingIMSI` (le numéro d’origine) est identique, mais où la valeur de `SwitchNum` (pays/région d’origine) n’est pas la même.

Si vous utilisez une jointure avec des données de flux, la jointure doit indiquer certaines limites relatives à l’intervalle pouvant séparer des lignes correspondantes dans le temps. Comme indiqué précédemment, les données de flux sont effectivement sans fin. Les limites temporelles de la relation sont spécifiées dans la clause `ON` de la jointure, à l’aide de la fonction `DATEDIFF`. Dans ce cas, la jointure est basée sur un intervalle de 5 secondes des données d’appels.

1. Collez la requête ci-après dans l’éditeur de requête :

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Cette requête est semblable aux jointures SQL excepté pour la fonction `DATEDIFF` de la jointure. Cette version de `DATEDIFF` est propre à Stream Analytics et doit apparaître dans la clause `ON...BETWEEN`. Les paramètres sont une unité de temps (des secondes dans cet exemple) et les alias des deux sources pour la jointure. Cela diffère de la fonction `DATEDIFF` SQL standard.

    La clause `WHERE` inclut la condition qui marque l’appel frauduleux : les commutateurs d’origine ne sont pas identiques.

2. Sélectionnez **Tester la requête**. Passez en revue la sortie, puis sélectionnez **Enregistrer la requête**.

## <a name="start-the-job-and-visualize-output"></a>Démarrer le travail et visualiser la sortie

1. Pour démarrer la tâche, accédez à la tâche **Vue d’ensemble**, puis sélectionnez **Démarrer**.

2. Sélectionnez **Maintenant** pour l’heure de début de sortie du travail, puis sélectionnez **Démarrer**. Vous pouvez voir l’état du travail dans la barre de notification.

3. Lorsque le travail est terminé, accédez à [Power BI](https://powerbi.com/), puis connectez-vous avec votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, le jeu de données *ASAdataset* que vous avez créé doit s’afficher dans l’onglet **Jeux de données**.

4. À partir de votre espace de travail Power BI, sélectionnez **+ Créer** pour créer un nouveau tableau de bord intitulé *Appels frauduleux*.

5. En haut de la fenêtre, sélectionnez **Modifier** puis **Ajouter une vignette**. Ensuite, sélectionnez **Données de streaming personnalisées**, puis **Suivant**. Choisissez **ASAdataset** dans **Vos jeux de données**. Sélectionnez **Carte** dans la liste déroulante **Type de visualisation**, puis ajoutez **fraudulentcalls** à **Champs**. Sélectionnez **Suivant** afin de saisir un nom pour la vignette, puis **Appliquer** pour créer la vignette.

   ![Créer des vignettes de tableau de bord Power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Effectuez à nouveau l’étape 5, avec les options suivantes :
   * Lorsque vous accédez à Type de visualisation, sélectionnez Graphique en courbes.
   * Ajoutez un axe, puis sélectionnez **windowend**.
   * Ajoutez une valeur, puis sélectionnez **fraudulentcalls**.
   * Pour **Fenêtre de temps à afficher**, sélectionnez les 10 dernières minutes.

7. Une fois que les deux vignettes ont été ajoutées, votre tableau de bord devrait ressembler à l’exemple ci-dessous. Si l’application émettrice Event Hub et l’application Stream Analytics sont en cours d’exécution, vous verrez que votre tableau de bord Power BI est régulièrement mis à jour avec l’arrivée de nouvelles données.

   ![Afficher les résultats dans le tableau de bord Power BI](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Incorporer votre tableau de bord Power BI dans une application web

Pour cette partie du tutoriel, vous allez utiliser un exemple d’application web [ASP.NET](https://asp.net/) créé par l’équipe Power BI pour intégrer votre tableau de bord. Pour plus d’informations sur l’intégration de tableaux de bord, consultez l’article [Power BI en mode intégration](/power-bi/developer/embedding).

Pour configurer l’application, accédez au dépôt GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) et suivez les instructions situées sous la section **User Owns Data** (L’utilisateur est propriétaire des données) (utilisez les URL de redirection et de page d’accueil de la sous-section **integrate-web-app**). Étant donné que nous utilisons l’exemple Dashboard (tableau de bord), utilisez l’exemple de code **integrate-web-app** qui se trouve dans le [dépôt GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Lorsque l’application est en cours d’exécution dans votre navigateur, procédez comme suit pour intégrer le tableau de bord que vous avez créé précédemment dans la page web :

1. Sélectionnez **Se connecter à Power BI**, pour accorder à l’application un accès aux tableaux de bord dans votre compte Power BI.

2. Sélectionnez le bouton **Get Dashboards** (Obtenir les tableaux de bord) qui affiche dans un tableau les tableaux de bord de votre compte. Recherchez le nom du tableau de bord vous avez créé précédemment (**powerbi-embedded-dashboard**) et copiez la **EmbedUrl** correspondante.

3. Pour finir, collez la **EmbedUrl** dans le champ de texte correspondant et sélectionnez **Embed Dashboard** (Intégrer le tableau de bord). Vous pouvez maintenant voir ce tableau de bord intégré dans une application web.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un travail Stream Analytics simple, analysé les données entrantes et affiché les résultats dans un tableau de bord Power BI. Pour en savoir plus sur les travaux Stream Analytics, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Exécuter Azure Functions dans des travaux Stream Analytics](stream-analytics-with-azure-functions.md)
