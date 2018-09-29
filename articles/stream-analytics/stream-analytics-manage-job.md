---
title: 'Didacticiel : créer et gérer un travail Stream Analytics à l’aide du portail Azure | Microsoft Docs'
description: Ce didacticiel illustre de bout en bout l’utilisation d’Azure Stream Analytics pour analyser les appels frauduleux dans un flux d’appel téléphonique.
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 1955fc033e0351be9da89bbee11dc41d6281a63a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433988"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Créer un travail Stream Analytics pour analyser les données d’appel téléphonique et visualiser les résultats dans un tableau de bord Power BI
 
Ce didacticiel montre comment utiliser Azure Stream Analytics pour analyser un exemple d’appel téléphonique qui est généré par une application cliente. Les données d’appel téléphonique générées par l’application cliente contiennent des appels frauduleux et nous allons définir un travail Stream Analytics pour filtrer ces appels.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Générer des exemples de données d’appel téléphonique et les envoyer par téléphone aux Azure Event Hubs  
> * Création d’un travail Stream Analytics   
> * Configurer l’entrée et la sortie du travail  
> * Définir une requête pour filtrer les appels frauduleux  
> * Tester et démarrer le travail  
> * Visualiser les résultats dans Power BI 

## <a name="prerequisites"></a>Prérequis

Avant de commencer, veillez à disposer des éléments qui suivent :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).  
* Connectez-vous au [portail Azure](https://portal.azure.com/).  
* Téléchargez l’application de génération d’événements d’appel téléphonique [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) depuis le Centre de téléchargement Microsoft, vous pouvez également obtenir le code source à partir de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Création d'un hub d'événements Azure 

Avant que Stream Analytics puisse analyse le flux de données d’appels frauduleux, vous devez envoyer les données à Azure. Dans ce didacticiel, vous envoyez des données à Azure à l’aide d’[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs). Pour ce didacticiel, vous créez un hub d’événements et configurez l’application de génération d’événements pour envoyer des données d’appel à ce hub. Procédez comme suit pour créer un hub d’événements :

1. Connectez-vous au portail Azure.  
2. Sélectionnez **Créer une ressource** > **Internet des objets** > **Event Hubs**.  

   ![Rechercher un hub d’événements](media/stream-analytics-manage-job/find-eh.png)
3. Remplissez le volet **Créer un espace de noms** avec les valeurs suivantes :  

   |**Paramètre**  |**Valeur suggérée** |**Description**  |
   |---------|---------|---------|
   |NOM     | myEventHubNS        |  Nom unique pour identifier l’espace de noms du hub d’événements.       |
   |Abonnement     |   \<Votre abonnement\>      |   Sélectionnez un abonnement Azure dans lequel vous souhaitez créer le hub d’événements.      |
   |Groupe de ressources     |   MyASADemoRG      |  Sélectionnez **Créer** et saisissez le nom du nouveau groupe de ressources pour votre compte.       |
   |Lieu     |   Ouest des États-Unis 2      |    Emplacement où l’espace de noms du hub d’événements peut être déployé.     |

4. Utilisez les options par défaut pour les autres paramètres et sélectionnez **Créer**.  

   ![Créer un espace de noms du hub d’événements](media/stream-analytics-manage-job/create-ehns.png)

5. Lorsque le déploiement de l’espace de noms est terminé, accédez à **Toutes les ressources** > recherchez « myEventHubNS » dans la liste des ressources Azure > sélectionnez pour l’ouvrir.  
6. Sélectionnez ensuite **+Event Hub** > **Nom**, le hub d’événements « MyEventHub ». Vous pouvez utiliser un autre nom. Utilisez les options par défaut pour les autres paramètres, sélectionnez **Créer** et attendez que le déploiement réussisse.

   ![Créer un event hub](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Accorder l’accès au concentrateur Event Hub et obtenir une chaîne de connexion

Pour qu’une application puisse envoyer des données à Azure Event Hubs, le hub d’événements doit disposer d’une stratégie autorisant un accès approprié. La stratégie d’accès génère une chaîne de connexion qui inclut des informations d’autorisation.

1. Accédez au concentrateur **Event Hubs** que vous avez créé à l’étape précédente, « MyEventHub », > sélectionnez **Stratégies d’accès partagé** dans le volet du hub d’événements > sélectionnez **+ Ajouter**.  
2. Définissez le nom de la stratégie sur **Mypolicy** > et sélectionnez **Gérer** > puis **Créer**.  

   ![Créer une stratégie d’accès partagé de hub d’événements](media/stream-analytics-manage-job/create-ehpolicy.png)

3. Une fois la stratégie déployée, sélectionnez pour ouvrir la stratégie, recherchez la **Clé primaire de la chaîne de connexion** et sélectionnez **copier** en regard de la chaîne de connexion.  
4. Collez la chaîne de connexion dans un éditeur de texte. Vous avez besoin de cette chaîne de connexion dans la section suivante.  

   La chaîne de connexion ressemble à ceci :

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Notez que la chaîne de connexion contient plusieurs paires clé-valeur, séparées par des points-virgules : Endpoint, SharedAccessKeyName, SharedAccessKey et EntityPath.  

5. Supprimez la paire EntityPath de la chaîne de connexion (n’oubliez pas de supprimer le point-virgule qui la précède).

## <a name="start-the-event-generator-application"></a>Démarrer l’application de génération d’événements

Avant de démarrer l’application TelcoGenerator, vous devez la configurer pour envoyer des données aux Azure Event Hubs que vous avez créés précédemment.

1. Extrayez le contenu du fichier [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Ouvrez le fichier `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` dans l’éditeur de texte de votre choix. (Comme il existe plusieurs fichiers .config, veillez à ouvrir celui qui convient.)  

3. Mettez à jour l’élément <appSettings> dans le fichier de configuration avec les détails suivants :

   * Définissez la valeur de la clé EventHubName sur la valeur de la paire EntityPath dans la chaîne de connexion.  
   * Définissez la valeur de la clé Microsoft.ServiceBus.ConnectionString sur la chaîne de connexion sans la valeur EntityPath que vous avez obtenue à l’étape 5 de la section précédente.

4. Enregistrez le fichier .  
5. Ouvrez ensuite une fenêtre de commande, accédez au dossier où vous avez décompressé l’application TelcoGenerator et entrez la commande suivante :

   ```
   telcodatagen.exe 1000 0.2 2
   ```

   Cette commande utilise les paramètres suivants :
   * **Nombre d’enregistrements de données d’appel par heure**.  
   * **Pourcentage de probabilité de fraude** : fréquence à laquelle l’application doit simuler un appel frauduleux. La valeur 0,2 signifie qu’environ 20 % des enregistrements d’appels semblent frauduleux.  
   * **Durée en heures** : nombre d’heures pendant lesquelles l’application doit s’exécuter. Vous pouvez également arrêter l’application à tout moment en terminant le processus (Ctrl+C) au niveau de la ligne de commande.

   Après quelques secondes, l’application commence à afficher des enregistrements des appels téléphoniques à l’écran à mesure qu’elle les envoie au concentrateur Event Hub. Les données d’appel téléphonique contiennent les champs suivants :

   |**Enregistrement**  |**Définition**  |
   |---------|---------|
   |CallrecTime    |  Horodatage de l’heure de début d’appel.       |
   |SwitchNum     |  Commutateur téléphonique utilisé pour connecter l’appel. Pour cet exemple, les commutateurs sont des chaînes qui représentent le pays d’origine (États-Unis, Chine, Royaume-Uni, Allemagne ou Australie).       |
   |CallingNum     |  Numéro de téléphone de l’appelant.       |
   |CallingIMSI     |  Identité de l’abonné mobile international (IMSI). Il s’agit d’un identificateur unique de l’appelant.       |
   |CalledNum     |   Numéro de téléphone du destinataire de l’appel.      |
   |CalledIMSI     |  Identité de l'abonné mobile international (IMSI). Il s’agit d’un identificateur unique du destinataire de l’appel.       |

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics 

Maintenant que vous disposez d’un flux d’événements d’appel, vous pouvez créer un travail Stream Analytics qui lit des données à partir du hub d’événements.

1. Pour créer un travail Stream Analytics, accédez au portail Azure  

2. Sélectionnez **Créer une ressource** > **Internet des objets** > **Travail Stream Analytics**.  

3. Remplissez le volet **Nouveau travail Stream Analytics** avec les valeurs suivantes :  

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Nom du travail     |  ASATutorial       |   Nom unique pour identifier l’espace de noms du hub d’événements.      |
   |Abonnement    |  \<Votre abonnement\>   |   Sélectionnez un abonnement Azure dans lequel vous souhaitez créer le travail.       |
   |Groupe de ressources   |   MyASADemoRG      |   Sélectionnez **Use existing** (Utiliser existant) et saisissez le nom du nouveau groupe de ressources pour votre compte.      |
   |Lieu   |    Ouest des États-Unis 2     |      Emplacement où le travail peut être déployé. Il est recommandé de placer le travail et le hub d’événements dans la même région afin d’optimiser les performances. Ce faisant, vous ne payez pas pour transférer des données entre les régions.      |
   |Environnement d’hébergement    | Cloud        |     Les travaux Stream Analytics peuvent être déployés dans le cloud ou sur des appareils Edge. L’option Cloud vous permet de déployer votre travail dans le cloud Azure, et l’option Edge sur un appareil IoT Edge.    |
   |Unités de diffusion en continu     |    1       |      Les unités de streaming sont les ressources de calcul requises pour exécuter un travail. Par défaut, cette valeur est définie sur 1. Pour en savoir plus sur la mise à l’échelle des unités de streaming, consultez [Understanding and adjusting streaming units](stream-analytics-streaming-unit-consumption.md) (Présentation et réglage des unités de streaming).      |

   ![Création d’un travail](media/stream-analytics-manage-job/create-a-job.png)   

4. Utilisez les options par défaut pour les autres paramètres, sélectionnez **Créer** et attendez que le déploiement réussisse.

## <a name="configure-job-input"></a>Configurer les entrées du travail

L’étape suivante consiste à définir une source d’entrée du travail pour lire les données. Pour ce didacticiel, vous allez utiliser le hub d’événements que vous avez créé dans la section précédente en tant qu’entrée. Procédez comme suit pour configurer l’entrée de votre travail :

1. Dans le portail Azure, ouvrez le volet **Toutes les ressources** et recherchez le travail Stream Analytics ASATutorial.  

2. Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Entrées**.  

3. Sélectionnez **+Add stream input** (+ Ajouter une entrée de flux) (l’entrée de référence correspond à des données de recherche statiques que vous n’utiliserez pas dans ce didacticiel), **Hub d’événements**, puis remplissez le volet avec les valeurs suivantes :  

   |**Paramètre**  |**Valeur suggérée**  |**Description**  |
   |---------|---------|---------|
   |Alias d’entrée     |  CallStream       |  Fournissez un nom convivial pour identifier votre entrée. L’alias d’entrée peut contenir uniquement des caractères alphanumériques, des traits d’union et des traits de soulignement, et doit avoir entre 3 et 63 caractères.       |
   |Abonnement    |   \<Votre abonnement\>      |   Sélectionnez l’abonnement Azure dans lequel vous avez créé le hub d’événements. Le hub d’événements peut se trouver dans le même abonnement ou dans un autre abonnement que le travail Stream Analytics.       |
   |Espace de noms du hub d’événements    |  MyEventHubNS       |  Sélectionnez l’espace de noms du hub d’événements que vous avez créé dans la section précédente. Tous les espaces de noms du hub d’événements disponibles dans votre abonnement actuel sont répertoriés dans la liste déroulante.       |
   |Nom de l’Event Hub    |   MyEventHub      |  Sélectionnez le hub d’événements que vous avez créé dans la section précédente. Tous les hubs d’événements disponibles dans votre abonnement actuel sont répertoriés dans la liste déroulante.       |
   |Nom de la stratégie du hub d’événements   |  MyPolicy       |  Sélectionnez la stratégie d’accès partagé du hub d’événements que vous avez créée dans la section précédente. Toutes les stratégies de hub d’événements disponibles dans votre abonnement actuel sont répertoriées dans la liste déroulante.       |

   ![Configurer l’entrée](media/stream-analytics-manage-job/configure-input.png) 

4. Utilisez les options par défaut pour les autres paramètres, sélectionnez **Enregistrer** et attendez que le déploiement réussisse.

## <a name="configure-job-output"></a>Configurer la sortie du travail 

La dernière étape consiste à définir un récepteur de sortie pour le travail dans lequel il peut écrire les données transformées. Pour ce didacticiel, vous allez produire les résultats dans Power BI et visualiser la date. Procédez comme suit pour configurer la sortie de votre travail :

1. Dans le portail Azure, ouvrez le volet **Toutes les ressources** et recherchez le travail Stream Analytics ASATutorial.  

2. Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Sorties**.  

3. Sélectionnez **+ Ajouter** > **Power BI**, remplissez le formulaire avec les détails suivants (vous pouvez fournir un nom convivial pour identifier l’alias de sortie, le nom du jeu de données et le nom de la table comme indiqué dans le tableau) et sélectionnez **Autoriser** :  

   |**Paramètre**  |**Valeur suggérée**  |
   |---------|---------|---------|
   |Alias de sortie  |  MyPBIoutput  |
   |Nom du jeu de données  |   ASAdataset  | 
   |Nom de la table |  ASATable  | 

   ![Configurer la sortie](media/stream-analytics-manage-job/configure-output.png)  

4. Lorsque vous sélectionnez **Autoriser**, une fenêtre indépendante s’ouvre et vous êtes invité à fournir des informations d’identification pour vous authentifier auprès de votre compte Power BI. Une fois que l’autorisation a réussi, **enregistrez** les paramètres. 

## <a name="define-a-query-to-analyze-input-data"></a>Définir une requête pour analyser les données d’entrée

Lorsque vous disposez d’un travail Stream Analytics configuré pour lire un flux de données entrantes, l’étape suivante consiste à créer une transformation qui analyse les données en temps réel. Vous définissez la requête de transformation à l’aide du [Langage de requête Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). Dans ce didacticiel, vous définissez une requête qui détecte des appels frauduleux à partir des données téléphoniques. 

Pour cet exemple, nous considérons les appels frauduleux comme provenant du même utilisateur mais à des emplacements distincts, et la durée entre les deux appels est de cinq secondes. Par exemple, un même utilisateur ne peut pas légitimement passer simultanément un appel depuis les États-Unis et l’Australie. Pour définir la requête de transformation pour votre travail Stream Analytics, procédez comme suit :

1. Dans le portail Azure, ouvrez le volet **Toutes les ressources** et ouvrez le travail Stream Analytics **ASATutorial** que vous avez créé précédemment.  

2. Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Requête**. La fenêtre indépendante répertorie les entrées et sorties qui sont configurées pour le travail et vous permet de créer une requête pour transformer le flux d’entrée.  

3. Remplacez ensuite la requête existante dans l’éditeur par les données suivantes. Cette requête effectue une jointure réflexive à un intervalle de 5 secondes de données d’appel :

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

   Pour rechercher des appels frauduleux, vous devez effectuer une jointure réflexive des données de diffusion en continu sur la valeur `CallRecTime`. Vous pouvez ensuite rechercher les enregistrements d’appels où la valeur `CallingIMSI` (numéro d’origine) est identique, mais où la valeur `SwitchNum` (pays d’origine) est différente. Si vous utilisez une opération JOIN avec des données de diffusion en continu, la jointure doit indiquer certaines limites relatives à l’intervalle pouvant séparer des lignes correspondantes dans le temps. Les données de diffusion en continu étant infinies, les limites de temps de la relation sont spécifiées dans la clause ON de la jointure, à l’aide de la fonction [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   Cette requête est identique à une jointure SQL normale à l’exception de la fonction DATEDIFF. La fonction DATEDIFF utilisée dans cette requête est spécifique à Stream Analytics, et doit apparaître dans la clause `ON...BETWEEN`.  

4. **Enregistrez** la requête.  

   ![définir la requête](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>Tester votre requête

Vous pouvez tester une requête à partir de l’éditeur de requête, et que vous avez besoin d’exemples de données pour tester une requête. Pour cette procédure pas à pas, vous allez extraire des exemples de données du flux d’appel téléphonique entrant dans le hub d’événements. Procédez comme suit pour tester la requête :

1. Assurez-vous que l’application TelcoGenerator s’exécute et qu’elle produit des enregistrements d’appels téléphoniques.  

2. Dans le volet **Requête**, sélectionnez les points situés en regard de l’entrée CallStream, puis sélectionnez **Exemple de données de l’entrée**. Cette opération ouvre un volet qui vous permet de spécifier la quantité d’exemples de données à lire à partir du flux d’entrée.  

   ![Exemples de données d’entrée](media/stream-analytics-manage-job/sample-input-data.png)  

3. Définissez **Minutes** sur 3 et sélectionnez **OK**. Trois minutes de données du flux d’entrée sont échantillonnées et vous êtes informé lorsque les exemples de données sont prêts. Vous pouvez visualiser l’état d’échantillonnage dans la barre de notification. 

   L’exemple de données est stocké temporairement et disponible tant que la fenêtre de requête est ouverte. Si vous la fermez, l’exemple de données est abandonné, et vous devez en créer un autre. Vous pouvez également obtenir un fichier .json qui contient des exemples de données de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), puis charger ce fichier .json dont vous pouvez utiliser les exemples de données pour l’entrée CallStream.  

4. Sélectionnez **Test** pour tester la requête. Vous devez voir les résultats de sortie comme présenté dans cette capture d’écran :  

   ![Sortie de test](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Démarrer le travail et visualiser la sortie

1. Pour démarrer le travail, accédez au volet **Vue d’ensemble** de votre travail, puis sélectionnez **Démarrer**.  

2. Sélectionnez **Maintenant** pour l’heure de début de sortie du travail, puis sélectionnez **Démarrer**. Le travail démarre en quelques minutes et vous pouvez visualiser l’état dans la barre de notification.  

3. Lorsque le travail aboutit, accédez à [Powerbi.com](https://powerbi.com/), puis connectez-vous avec votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, vous voyez que votre jeu de données est déjà créé. Accédez à l’onglet **Jeux de données**, où vous pouvez voir un jeu de données nommé « ASAdataset ».  

4. Dans votre espace de travail, sélectionnez **+ Créer**. Créez un tableau de bord et nommez-le Fraudulent Calls. Vous allez ajouter deux vignettes à ce tableau de bord : une vignette permet d’afficher le nombre d’appels frauduleux à une instance donnée et l’autre vignette contient une visualisation de graphique en courbes.  

5. En haut de la fenêtre, sélectionnez **Ajouter une vignette** > et sélectionnez **Données de streaming personnalisées** > Suivant > choisissez **ASAdataset** > pour le type de visualisation, sélectionnez **Carte** > et des champs comme **fraudulentcalls**. Sélectionnez **Suivant** > entrez un nom pour la vignette, puis sélectionnez **Appliquer**.  

   ![Créer des vignettes](media/stream-analytics-manage-job/create-tiles.png)

6. Effectuez à nouveau l’étape 4, avec les options suivantes :
   * Lorsque vous accédez à Type de visualisation, sélectionnez Graphique en courbes.  
   * Ajoutez un axe, puis sélectionnez **windowend**.  
   * Ajoutez une valeur, puis sélectionnez **fraudulentcalls**.  
   * Pour **Fenêtre de temps à afficher**, sélectionnez les 10 dernières minutes.  

7. Votre tableau de bord ressemble à la capture d’écran suivante lorsque les deux vignettes ont été ajoutées. Vous constaterez que, si votre application d’expédition de hub d’événements et l’application Stream Analytics sont en cours d’exécution, votre tableau de bord Power BI est régulièrement mis à jour à mesure que de nouvelles données arrivent.  

   ![Résultats Power BI](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Intégrer votre tableau de bord Power BI dans une application web

Pour cette partie du didacticiel, vous allez utiliser un exemple d’application web [ASP.NET](http://asp.net/) créé par l’équipe Power BI pour intégrer votre tableau de bord. Pour plus d’informations sur l’intégration de tableaux de bord, consultez l’article [Power BI en mode intégration](https://docs.microsoft.com/power-bi/developer/embedding).

Dans ce didacticiel, nous allons suivre les étapes pour l’utilisateur propriétaire de l’application de données. Pour configurer l’application, accédez au référentiel GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) et suivez les instructions situées sous la section **User Owns Data** (L’utilisateur détient les données) (utilisez les URL de redirection et de page d’accueil de la sous-section **integrate-dashboard-web-app**). Étant donné que nous utilisons l’exemple de tableau de bord, utilisez l’exemple de code integrate-dashboard-web-app situé dans le [référentiel GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Lorsque l’application est en cours d’exécution dans votre navigateur, procédez comme suit pour intégrer le tableau de bord que vous avez créé précédemment dans la page web :

1. Sélectionnez **Se connecter à Power BI**, qui accorde à l’application un accès aux tableaux de bord dans votre compte Power BI.  

2. Sélectionnez le bouton **Get Dashboards** (Obtenir les tableaux de bord) qui affiche dans un tableau les tableaux de bord de votre compte. Recherchez le nom du tableau de bord vous avez créé précédemment (powerbi-embedded-dashboard) et copiez la **EmbedUrl** correspondante.  

3. Pour finir, collez la **EmbedUrl** dans le champ de texte correspondant et sélectionnez **Embed Dashboard** (Intégrer le tableau de bord). Vous pouvez maintenant voir ce tableau de bord intégré dans une application web.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez avoir créé un travail Stream Analytics simple, analysé les données entrantes et affiché les résultats dans un tableau de bord Power BI. Pour en savoir plus sur les travaux Stream Analytics, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Exécuter Azure Functions dans des travaux Stream Analytics](stream-analytics-with-azure-functions.md)
