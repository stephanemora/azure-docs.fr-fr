---
title: Tutoriel - Créer et gérer un travail Stream Analytics à l’aide du portail Azure
description: Ce didacticiel démontre de bout en bout comment utiliser Azure Stream Analytics pour analyser les appels frauduleux dans un flux d’appel téléphonique.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: a265bc2ed131dc0bb69d89f767ab60225d30ee8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89612048"
---
# <a name="tutorial-analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Tutoriel : Analyser les données d’appel téléphonique avec Stream Analytics et visualiser les résultats dans un tableau de bord Power BI

Ce didacticiel vous apprend à analyser les données d’appel téléphonique à l’aide d’Azure Stream Analytics. Les données d’appel téléphonique, générées par une application cliente, contiennent des appels frauduleux qui seront filtrés par le travail Stream Analytics.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Générer des exemples de données d’appel téléphonique et les envoyer aux Azure Event Hubs
> * Création d’un travail Stream Analytics
> * Configurer les entrées et sorties du travail
> * Définir une requête pour filtrer les appels frauduleux
> * Tester et démarrer le travail
> * Visualiser les résultats dans Power BI

## <a name="prerequisites"></a>Prérequis

Avant de commencer, effectuez les étapes suivantes :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).
* Connectez-vous au [portail Azure](https://portal.azure.com/).
* Téléchargez l’application de génération d’événements d’appel téléphonique [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) depuis le Centre de téléchargement Microsoft, ou obtenez le code source à partir de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Il vous faut un compte Power BI.

## <a name="create-an-azure-event-hub"></a>Création d'un hub d'événements Azure

Avant que Stream Analytics puisse analyser le flux de données d’appels frauduleux, les données doivent être envoyées à Azure. Dans ce didacticiel, vous envoyez des données à Azure à l’aide d’[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Suivez les étapes ci-dessous pour créer un Event Hub et envoyer les données d’appel à celui-ci :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Créer une ressource** > **Internet des objets** > **Event Hubs**.

   ![Créer un hub Azure Event Hub dans le portail](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Remplissez le volet **Créer un espace de noms** avec les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée** |**Description**  |
   |---------|---------|---------|
   |Nom     | myEventHubsNS        |  Nom unique pour identifier l’espace de noms du hub d’événements.       |
   |Abonnement     |   \<Your subscription\>      |   Sélectionnez un abonnement Azure dans lequel vous souhaitez créer le hub d’événements.      |
   |Resource group     |   MyASADemoRG      |  Sélectionnez **Créer** et saisissez le nom du nouveau groupe de ressources pour votre compte.       |
   |Emplacement     |   USA Ouest 2      |    Emplacement où l’espace de noms du hub d’événements peut être déployé.     |

4. Utilisez les options par défaut pour les autres paramètres et sélectionnez **Créer**.

   ![Créer un espace de noms Event Hub dans le Portail Azure](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Lorsque le déploiement de l’espace de noms est terminé, accédez à **Toutes les ressources** et recherchez *myEventHubNS* dans la liste des ressources Azure. Sélectionnez *myEventHubsNS* pour l’ouvrir.
6. Ensuite, sélectionnez **+Event Hub**, et saisissez *MyEventHub* dans le champ **Nom** ou un autre nom de votre choix. Utilisez les options par défaut pour les autres paramètres et sélectionnez **Créer**. Ensuite, attendez que le déploiement se termine.

   ![Configuration d’Event Hub dans le Portail Azure](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Accorder l’accès au concentrateur Event Hub et obtenir une chaîne de connexion

Pour qu’une application puisse envoyer des données à Azure Event Hubs, le hub d’événements doit disposer d’une stratégie autorisant un accès approprié. La stratégie d’accès génère une chaîne de connexion qui inclut des informations d’autorisation.

1. Sélectionnez le hub d’événements que vous avez créé à l’étape précédente, MyEventHub*. Sélectionnez **Stratégies d’accès partagé** > **Paramètres** > **+ Ajouter**.

2. Nommez la stratégie **MyPolicy**, et vérifiez que la case **Gérer** est cochée. Sélectionnez ensuite **Créer**.

   ![Créer une stratégie d’accès partagé de hub d’événements](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Une fois que la stratégie est créée, sélectionnez-la pour l’ouvrir, puis recherchez la **clé primaire de la chaîne de connexion**. Sélectionnez le bouton bleu **copier** à côté de la chaîne de connexion.

   ![Enregistrer la chaîne de connexion de stratégie accès partagé](media/stream-analytics-manage-job/save-connection-string.png)

4. Collez la chaîne de connexion dans un éditeur de texte. Vous avez besoin de cette chaîne de connexion dans la section suivante.

   La chaîne de connexion ressemble à ceci :

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Notez que la chaîne de connexion contient plusieurs paires clé-valeur, séparées par des points-virgules : **Point de terminaison**, **SharedAccessKeyName**, **SharedAccessKey** et **EntityPath**.

## <a name="start-the-event-generator-application"></a>Démarrer l’application de génération d’événements

Avant de démarrer l’application TelcoGenerator, vous devez la configurer pour envoyer des données aux Azure Event Hubs que vous avez créés précédemment.

1. Extrayez le contenu du fichier [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Ouvrez le fichier `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` dans l’éditeur de texte de votre choix. (Comme il existe plusieurs fichiers .config, veillez à ouvrir celui qui convient.)

3. Mettez à jour l’élément `<appSettings>` dans le fichier de configuration avec les détails suivants :

   * Définissez la valeur de la clé *EventHubName* sur la valeur de la paire EntityPath dans la chaîne de connexion.
   * Définissez la valeur de la clé *Microsoft.ServiceBus.ConnectionString* sur la chaîne de connexion sans la valeur EntityPath (n’oubliez pas de supprimer le point-virgule qui la précède).

4. Enregistrez le fichier .
5. Ensuite, ouvrez une fenêtre de commandes et accédez au dossier dans lequel l’application TelcoGenerator est décompressée. Puis, entrez la commande suivante :

   ```cmd
   telcodatagen.exe 1000 0.2 2
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

2. Sélectionnez **Créer une ressource** > **Internet des objets** > **Travail Stream Analytics**.

3. Remplissez le volet **Nouveau travail Stream Analytics** avec les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Nom du travail     |  ASATutorial       |   Nom unique pour identifier l’espace de noms du hub d’événements.      |
   |Abonnement    |  \<Your subscription\>   |   Sélectionnez un abonnement Azure dans lequel vous souhaitez créer le travail.       |
   |Resource group   |   MyASADemoRG      |   Sélectionnez **Use existing** (Utiliser existant) et saisissez le nom du nouveau groupe de ressources pour votre compte.      |
   |Emplacement   |    USA Ouest 2     |      Emplacement où le travail peut être déployé. Il est recommandé de placer le travail et le hub d’événements dans la même région afin d’optimiser les performances. Ce faisant, vous ne payez pas pour transférer des données entre les régions.      |
   |Environnement d’hébergement    | Cloud        |     Les travaux Stream Analytics peuvent être déployés dans le cloud ou sur des appareils Edge. L’option Cloud vous permet de déployer votre travail dans le cloud Azure, et l’option Edge sur un appareil IoT Edge.    |
   |Unités de diffusion en continu     |    1       |      Les unités de streaming sont les ressources de calcul requises pour exécuter un travail. Par défaut, cette valeur est définie sur 1. Pour en savoir plus sur la mise à l’échelle des unités de streaming, consultez [Understanding and adjusting streaming units](stream-analytics-streaming-unit-consumption.md) (Présentation et réglage des unités de streaming).      |

4. Utilisez les options par défaut pour les autres paramètres, sélectionnez **Créer** et attendez que le déploiement réussisse.

   ![Créer un travail Azure Stream Analytics](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Configurer les entrées du travail

L’étape suivante consiste à définir une source d’entrée pour le travail, afin de pouvoir lire les données à l’aide de l’Event Hub que vous avez créé dans la section précédente.

1. Dans le portail Azure, ouvrez le volet **Toutes les ressources** et recherchez le travail Stream Analytics *ASATutorial*.

2. Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Entrées**.

3. Cliquez sur **+ Ajouter une entrée de flux**, puis sur **Event Hub**. Dans le volet, renseignez les valeurs suivantes :

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Alias d’entrée     |  CallStream       |  Fournissez un nom convivial pour identifier votre entrée. L’alias d’entrée peut contenir uniquement des caractères alphanumériques, des traits d’union et des traits de soulignement, et doit avoir entre 3 et 63 caractères.       |
   |Abonnement    |   \<Your subscription\>      |   Sélectionnez l’abonnement Azure dans lequel vous avez créé le hub d’événements. Le hub d’événements peut se trouver dans le même abonnement ou dans un autre abonnement que le travail Stream Analytics.       |
   |Espace de noms du hub d’événements    |  myEventHubsNS       |  Sélectionnez l’espace de noms du hub d’événements que vous avez créé dans la section précédente. Tous les espaces de noms du hub d’événements disponibles dans votre abonnement actuel sont répertoriés dans la liste déroulante.       |
   |Nom du hub d’événements    |   MyEventHub      |  Sélectionnez le hub d’événements que vous avez créé dans la section précédente. Tous les hubs d’événements disponibles dans votre abonnement actuel sont répertoriés dans la liste déroulante.       |
   |Nom de la stratégie du hub d’événements   |  MyPolicy       |  Sélectionnez la stratégie d’accès partagé du hub d’événements que vous avez créée dans la section précédente. Toutes les stratégies de hub d’événements disponibles dans votre abonnement actuel sont répertoriées dans la liste déroulante.       |

4. Utilisez les options par défaut pour les autres paramètres, puis sélectionnez **Créer**.

   ![Configurer une entrée Azure Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configurer la sortie du travail

La dernière étape consiste à définir un récepteur de sortie pour le travail dans lequel il peut écrire les données transformées. Dans ce didacticiel, vous générez et visualisez des données avec Power BI.

1. Dans le portail Azure, ouvrez le volet **Toutes les ressources** et recherchez le travail Stream Analytics *ASATutorial*.

2. Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Sorties**.

3. Sélectionnez **+ Ajouter** > **Power BI**. Remplissez ensuite le formulaire avec les détails suivants, puis sélectionnez **Autoriser** :

   |**Paramètre**  |**Valeur suggérée**  |
   |---------|---------|
   |Alias de sortie  |  MyPBIoutput  |
   |Nom du jeu de données  |   ASAdataset  |
   |Nom de la table |  ASATable  |

   ![Configurer une sortie Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Lorsque vous sélectionnez **Autoriser**, une fenêtre contextuelle s’ouvre et vous invite à fournir des informations d’identification pour vous authentifier auprès de votre compte Power BI. Une fois que l’autorisation a réussi, **enregistrez** les paramètres. Pour utiliser l’identité managée, consultez [Utiliser une identité managée pour authentifier votre tâche Azure Stream Analytics dans Power BI](powerbi-output-managed-identity.md).

## <a name="define-a-query-to-analyze-input-data"></a>Définir une requête pour analyser les données d’entrée

L’étape suivante consiste à créer une transformation qui analyse les données en temps réel. Vous définissez la requête de transformation à l’aide du [Langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). La requête utilisée dans ce didacticiel détecte les appels frauduleux à partir des données téléphoniques.

Dans cet exemple, les appels frauduleux sont effectués par le même utilisateur dans un délai de cinq secondes, mais dans des emplacements distincts. Par exemple, un même utilisateur ne peut pas légitimement passer simultanément un appel depuis les États-Unis et l’Australie. Pour définir la requête de transformation pour votre travail Stream Analytics, procédez comme suit :

1. Dans le portail Azure, ouvrez le volet **Toutes les ressources** et accédez au travail Stream Analytics **ASATutorial** que vous avez créé précédemment.

2. Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Requête**. La fenêtre de requête répertorie les entrées et sorties qui sont configurées pour le travail, et vous permet de créer une requête pour transformer le flux d’entrée.

3. Remplacez la requête existante dans l’éditeur par la suivante. La nouvelle requête effectue une jointure réflexive à un intervalle de 5 secondes de données d’appel :

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Pour rechercher des appels frauduleux, vous pouvez effectuer une jointure réflexive des données de diffusion en continu sur la valeur `CallRecTime`. Vous pouvez ensuite rechercher les enregistrements d’appels où la valeur `CallingIMSI` (numéro d’origine) est identique, mais où la valeur `SwitchNum` (pays/région d’origine) est différente. Si vous utilisez une opération JOIN avec des données de diffusion en continu, la jointure doit indiquer certaines limites relatives à l’intervalle pouvant séparer des lignes correspondantes dans le temps. Les données de diffusion en continu étant infinies, les limites de temps de la relation sont spécifiées dans la clause **ON** de la jointure, à l’aide de la fonction [DATEDIFF](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics).

   Cette requête est identique à une jointure SQL normale à l’exception de la fonction **DATEDIFF**. La fonction **DATEDIFF** utilisée dans cette requête est spécifique à Stream Analytics, et doit apparaître dans la clause `ON...BETWEEN`.

4. **Enregistrez** la requête.

   ![Définir une requête Stream Analytics dans le portail](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Tester votre requête

Vous pouvez tester une requête à partir de l’éditeur de requête en utilisant des exemples de données. Procédez comme suit pour tester la requête :

1. Assurez-vous que l’application TelcoGenerator s’exécute et qu’elle produit des enregistrements d’appels téléphoniques.

2. Dans le volet **Requête**, sélectionnez les points situés en regard de l’entrée *CallStream*, puis sélectionnez **Exemple de données de l’entrée**.

3. Définissez **Minutes** sur 3 et sélectionnez **OK**. Trois minutes de données du flux d’entrée sont alors échantillonnées, et vous êtes informé lorsque l’exemple de données est prêt. Vous pouvez visualiser l’état d’échantillonnage dans la barre de notification.

   L’exemple de données est stocké temporairement et disponible tant que la fenêtre de requête est ouverte. Si vous fermez la fenêtre de requête, l’exemple de données est abandonné, et vous devez en créer un autre pour tester la requête. Vous pouvez également vous servir d’un fichier d’exemple de données JSON venant de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), puis charger ce fichier afin de l’utiliser comme exemple de données pour l’entrée *CallStream*.

   ![Visuel montrant comment échantillonner des données d’entrée pour Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Sélectionnez **Tester** pour tester la requête. Les résultats suivants doivent s’afficher :

   ![Résultat du test de requête Stream Analytics](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Démarrer le travail et visualiser la sortie

1. Pour démarrer le travail, accédez au volet **Vue d’ensemble** de votre travail, puis sélectionnez **Démarrer**.

2. Sélectionnez **Maintenant** pour l’heure de début de sortie du travail, puis sélectionnez **Démarrer**. Vous pouvez voir l’état du travail dans la barre de notification.

3. Lorsque le travail est terminé, accédez à [Power BI](https://powerbi.com/), puis connectez-vous avec votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, le jeu de données *ASAdataset* que vous avez créé doit s’afficher dans l’onglet **Jeux de données**.

4. À partir de votre espace de travail Power BI, sélectionnez **+ Créer** pour créer un nouveau tableau de bord intitulé *Appels frauduleux*.

5. En haut de la fenêtre, sélectionnez **Ajouter une vignette**. Ensuite, sélectionnez **Données de streaming personnalisées**, puis **Suivant**. Choisissez **ASAdataset** dans **Vos jeux de données**. Sélectionnez **Carte** dans la liste déroulante **Type de visualisation**, puis ajoutez **fraudulentcalls** à **Champs**. Sélectionnez **Suivant** afin de saisir un nom pour la vignette, puis **Appliquer** pour créer la vignette.

   ![Créer des vignettes de tableau de bord Power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Effectuez à nouveau l’étape 5, avec les options suivantes :
   * Lorsque vous accédez à Type de visualisation, sélectionnez Graphique en courbes.
   * Ajoutez un axe, puis sélectionnez **windowend**.
   * Ajoutez une valeur, puis sélectionnez **fraudulentcalls**.
   * Pour **Fenêtre de temps à afficher**, sélectionnez les 10 dernières minutes.

7. Une fois que les deux vignettes ont été ajoutées, votre tableau de bord devrait ressembler à l’exemple ci-dessous. Si l’application émettrice Event Hub et l’application Stream Analytics sont en cours d’exécution, vous verrez que votre tableau de bord Power BI est régulièrement mis à jour avec l’arrivée de nouvelles données.

   ![Afficher les résultats dans le tableau de bord Power BI](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Incorporer votre tableau de bord Power BI dans une application web

Pour cette partie du tutoriel, vous allez utiliser un exemple d’application web [ASP.NET](https://asp.net/) créé par l’équipe Power BI pour intégrer votre tableau de bord. Pour plus d’informations sur l’intégration de tableaux de bord, consultez l’article [Power BI en mode intégration](https://docs.microsoft.com/power-bi/developer/embedding).

Pour configurer l’application, accédez au dépôt GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) et suivez les instructions situées sous la section **User Owns Data** (L’utilisateur est propriétaire des données) (utilisez les URL de redirection et de page d’accueil de la sous-section **integrate-web-app**). Étant donné que nous utilisons l’exemple Dashboard (tableau de bord), utilisez l’exemple de code **integrate-web-app** qui se trouve dans le [dépôt GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/integrate-web-app).
Lorsque l’application est en cours d’exécution dans votre navigateur, procédez comme suit pour intégrer le tableau de bord que vous avez créé précédemment dans la page web :

1. Sélectionnez **Se connecter à Power BI**, pour accorder à l’application un accès aux tableaux de bord dans votre compte Power BI.

2. Sélectionnez le bouton **Get Dashboards** (Obtenir les tableaux de bord) qui affiche dans un tableau les tableaux de bord de votre compte. Recherchez le nom du tableau de bord vous avez créé précédemment (**powerbi-embedded-dashboard**) et copiez la **EmbedUrl** correspondante.

3. Pour finir, collez la **EmbedUrl** dans le champ de texte correspondant et sélectionnez **Embed Dashboard** (Intégrer le tableau de bord). Vous pouvez maintenant voir ce tableau de bord intégré dans une application web.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé un travail Stream Analytics simple, analysé les données entrantes et affiché les résultats dans un tableau de bord Power BI. Pour en savoir plus sur les travaux Stream Analytics, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Exécuter Azure Functions dans des travaux Stream Analytics](stream-analytics-with-azure-functions.md)
