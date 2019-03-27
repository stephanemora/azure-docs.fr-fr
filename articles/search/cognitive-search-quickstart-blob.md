---
title: Générer un pipeline de recherche cognitive pour l’indexation basée sur l’intelligence artificielle dans le portail Azure - Recherche Azure
description: Exemple de compétences en matière d’extraction de données, de langage naturel et de traitement d’image dans le portail Azure à l’aide d’exemples de données.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 03/17/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f00df841f81ea5c7aa1fd53309b00487602e5143
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200621"
---
# <a name="quickstart-create-a-cognitive-search-pipeline-using-skills-and-sample-data"></a>Démarrage rapide : Créer un pipeline de recherche cognitive à l’aide de compétences et d’exemples de données

La recherche cognitive (version préliminaire) ajoute des compétences en matière d’extraction de données, de traitement en langage naturel (NLP) et de traitement d’image à un pipeline d’indexation Recherche Azure pour faciliter la recherche dans les contenus non structurés ou dans lesquels il est impossible d’effectuer des recherches. 

Un pipeline de recherche cognitive intègre des [ressources Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), telles que l’[OCR](cognitive-search-skill-ocr.md), la [détection de la langue](cognitive-search-skill-language-detection.md) et la [reconnaissance d’entité](cognitive-search-skill-entity-recognition.md), dans un processus d’indexation. Les algorithmes d’intelligence artificielle de Cognitive Services sont utilisés pour détecter des modèles, des fonctionnalités et des caractéristiques dans les données sources, en renvoyant des structures et du contenu textuel qui peuvent être utilisés dans les solutions de recherche en texte intégral reposant sur la Recherche Azure.

Dans ce guide de démarrage rapide, créez votre premier pipeline d’enrichissement dans le [portail Azure](https://portal.azure.com) avant d’écrire une seule ligne de code :

> [!div class="checklist"]
> * Commencer avec les exemples de données dans le stockage d’objets blob Azure
> * Configurer l’Assistant [**Importer des données**](search-import-data-portal.md) pour l’indexation et l’enrichissement cognitifs 
> * Exécutez l’Assistant (une compétence d’entité détecte les personnes, l’emplacement et les organisations)
> * Utiliser l’[**Explorateur de recherche**](search-explorer.md) pour interroger les données enrichies

## <a name="supported-regions"></a> Régions prises en charge

Une indexation agrémentée d’IA par le biais de Cognitive Services est disponible dans toutes les régions de Recherche Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> À compter du 21 décembre 2018, vous pouvez associer une ressource Cognitive Services à un ensemble de qualifications Recherche Azure. Cela nous permet de commencer la facturation pour l’exécution des ensembles de qualifications. Ce jour-là, nous commencerons également à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents continuera d’être offerte sans frais supplémentaires.
>
> L’exécution des compétences intégrées sera facturée au prix actuel du [paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les tarifs de l’extraction d’images sont ceux de la préversion. Ils sont décrits dans la page [Tarification de Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400). En savoir [plus](cognitive-search-attach-cognitive-services.md).

## <a name="prerequisites"></a>Prérequis

[« Qu’est-ce que la recherche cognitive ? »](cognitive-search-concept-intro.md) Présente l’architecture d’enrichissement et ses composants. 

Les services Azure sont utilisés exclusivement dans ce scénario. La création des services dont vous avez besoin fait partie de la préparation.

+ Le [stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) fournit les données source.
+ [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) fournit l’intelligence artificielle (vous pouvez créer ces ressources en ligne, lorsque vous spécifiez le pipeline).
+ La [Recherche Azure](https://azure.microsoft.com/services/search/) fournit le pipeline d’indexation enrichi et une expérience de recherche de texte enrichi de forme libre pour une utilisation dans des applications personnalisées.

### <a name="set-up-azure-search"></a>Configurer Recherche Azure

Tout d’abord, inscrivez-vous au service Recherche Azure. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

1. Cliquez sur **Créer une ressource**, recherchez Recherche Azure et cliquez sur **Créer**. Consultez [Créer un service Recherche Azure dans le portail](search-create-service-portal.md) si vous configurez un service de recherche pour la première fois et avez besoin d’aide supplémentaire.

   ![Portail de tableau de bord](./media/cognitive-search-tutorial-blob/create-search-service-full-portal.png "Créer un service Recherche Azure dans le portail")

1. Pour Groupe de ressources, créez un groupe de ressources qui contiendra toutes les ressources que vous créerez dans ce guide de démarrage rapide. Cela facilite le nettoyage des ressources lorsque vous avez terminé le guide de démarrage rapide.

1. Concernant l’emplacement, choisissez l’une des [régions prises en charge](#supported-regions) pour la recherche cognitive.

1. Pour le niveau tarifaire, vous pouvez créer un service **Gratuit** pour effectuer les tutoriels et les guides de démarrage rapide. Pour un examen plus approfondi à l’aide de vos propres données, créez un [service payant](https://azure.microsoft.com/pricing/details/search/), tel qu’un service **De base** ou **Standard**. 

   Un service gratuit est limité à 3 index, à une taille maximale d’objet blob de 16 Mo et à 2 minutes d’indexation, ce qui est insuffisant pour exercer toutes les fonctionnalités de recherche cognitive. Pour passer en revue les limites des différents niveaux, consultez [Limites du service](search-limits-quotas-capacity.md).

   ![Page de définition de service dans le portail](./media/cognitive-search-tutorial-blob/create-search-service2.png "Page de définition de service dans le portail")

   > [!NOTE]
   > La recherche cognitive est disponible en préversion publique. L’exécution des compétences est actuellement disponible dans tous les niveaux, y compris le niveau gratuit. Vous serez en mesure d’effectuer un nombre limité d’enrichissements sans associer une ressource Cognitive Services payante. [En savoir plus](cognitive-search-attach-cognitive-services.md).

1. Épinglez le service au tableau de bord pour accéder rapidement aux informations du service.

   ![Page de définition de service dans le portail](./media/cognitive-search-tutorial-blob/create-search-service3.png "Page de définition de service dans le portail")

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurer le service Blob Azure et charger les données d’exemple

Le pipeline d’enrichissement procède à l’extraction à partir des sources de données Azure prises en charge par les [indexeurs de recherche Azure](search-indexer-overview.md). Notez que Stockage Table Azure n’est pas pris en charge pour la recherche cognitive. Pour cet exercice, nous utilisons le stockage d’objets blob pour présenter plusieurs types de contenu.

1. [Téléchargez les exemples de données](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistant en un petit ensemble de fichiers de types différents. 

1. Inscrivez-vous au Stockage Blob Azure, créez un compte de stockage, ouvrez les pages des services Blob et créez un conteneur. 

1. Dans le conteneur, définissez le niveau d’accès public sur **Conteneur (accès en lecture anonyme pour les conteneurs et les objets blob)**. Pour plus d’informations, consultez la [section « Créer un conteneur »](../storage/blobs/storage-unstructured-search.md#create-a-container) dans le tutoriel *Rechercher des données non structurées*.

1. Dans le conteneur que vous avez créé, cliquez sur **Charger** pour charger les fichiers d’exemples téléchargés lors d’une étape précédente.

   ![Fichiers source sur le Stockage Blob Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>Créer le pipeline d’enrichissement

Revenez à la page de tableau de bord du service Recherche Azure et cliquez sur **Importer des données** dans la barre de commandes pour configurer l’enrichissement cognitif en quatre étapes.

  ![Commande Importer des données](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

Dans **Se connecter à vos données**, choisissez **Stockage Blob Azure**, puis sélectionnez le compte et le conteneur que vous avez créés. Donnez un nom à la source de données et utilisez les valeurs par défaut pour le reste. 

  ![Configuration d’objets blob Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

Passez à la page suivante.

  ![Bouton de page suivante pour la recherche cognitive](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>Étape 2 : Ajouter des compétences cognitives

Ensuite, ajoutez des étapes d’enrichissement au pipeline d’indexation. Si vous n’avez pas de ressource Cognitive Services, vous pouvez vous inscrire pour une version gratuite qui vous octroie 20 transactions tous les jours. Les exemples de données se composent de 14 fichiers. Votre allocation quotidienne sera donc en grande partie utilisée une fois que vous aurez exécuté cet Assistant.

1. Développez **Attacher Cognitive Services** pour afficher les options de ressources de l’API Cognitive Services. Dans le cadre de ce didacticiel, vous pouvez sélectionner la ressource **Gratuit**.

   ![Attacher Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Développez **Ajouter des enrichissements** et sélectionnez les compétences qui effectuent le traitement en langage naturel. Pour ce guide démarrage rapide, choisissez la reconnaissance d’entité pour les personnes, les organisations et les emplacements.

   ![Attacher Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

   Le portail propose des compétences intégrées pour le traitement OCR et l’analyse de texte. Dans le portail, un ensemble de compétences s’exécute sur un champ source unique. Cela peut sembler être une cible de petite taille, mais pour les objets BLOB Azure le champ `content` contient la plupart du document de l’objet blob (par exemple, un document Word ou un jeu PowerPoint). Par conséquent, ce champ est une entrée idéale, car tous les contenus d’un objet blob s’y trouvent.

3. Passez à la page suivante.

   ![Page suivante : personnaliser l’index](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> Les compétences en matière de traitement en langage naturel fonctionnent sur le contenu de texte dans l’exemple de jeu de données. Étant donné que nous n’avons pas sélectionné l’option OCR, les fichiers JPEG et PNG trouvés dans l’exemple de jeu de données ne seront pas traités dans ce guide de démarrage rapide. 

### <a name="step-3-configure-the-index"></a>Étape 3 : Configurer l’index

L’Assistant peut généralement déduire un index par défaut. À cette étape, vous pouvez afficher le schéma d’index généré et potentiellement modifier les paramètres. Vous trouverez ci-dessous l’index par défaut créé pour le jeu de données d’objets Blob de démonstration.

Pour ce guide de démarrage rapide, l’Assistant effectue un travail de qualité en termes de définition de valeurs par défaut raisonnables : 

+ Le nom par défaut est *azureblob-index* en fonction du type de source de données. 

+ Les champs par défaut sont basés sur le champ des données sources d’origine (`content`), ainsi que sur les champs de sortie (`people`, `organizations`, et `locations`) créés par le pipeline cognitif. Les types de données par défaut sont déduits à partir des échantillonnages de métadonnées et de données.

+ La clé par défaut est *metadata_storage_path* (ce champ contient des valeurs uniques).

+ Les attributs par défaut sont **Récupérable** et **PossibilitéRecherche** pour ces champs. **Possibilité de recherche** indique qu’un champ peut faire l’objet d’une recherche. **Récupérable** signifie qu’il peut être renvoyé dans les résultats. L’Assistant suppose que vous souhaitez ces champs récupérables et interrogeables, car vous les avez créés par l’intermédiaire de compétences.

  ![Champs d’index](media/cognitive-search-quickstart-blob/index-fields.png)

Remarquez la zone barrée et le point d’interrogation sur l’attribut **Récupérable** près du champ `content`. Pour les documents d’objets blob comportant beaucoup de texte, le champ `content` contient la majeure partie du fichier qui peut atteindre des milliers de lignes. Si vous devez transmettre le contenu du fichier au code client, assurez-vous que **Récupérable** reste sélectionné. Sinon, pensez à décocher cet attribut sur `content` si les éléments extraits (`people`, `organizations`, et `locations`) sont suffisants pour vos besoins.

Marquer un champ comme étant **Récupérable** ne signifie pas que le champ *doit* être présent dans les résultats de recherche. Vous pouvez contrôler avec précision la composition des résultats de recherche à l’aide du paramètre de requête **$select** pour spécifier les champs à inclure. Pour les champs comportant beaucoup de texte, tels que `content`, le paramètre **$select** constitue votre solution pour fournir aux utilisateurs humains de votre application des résultats de recherche faciles à gérer, tout en garantissant au code client l’accès à toutes les informations dont il a besoin via l’attribut **Récupérable**.
  
Passez à la page suivante.

  ![Page suivante : créer un indexeur](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>Étape 4 : Configurer l’indexeur

L’indexeur est une ressource de niveau supérieur qui gère le processus d’indexation. Il spécifie le nom de la source de données, un index cible et la fréquence d’exécution. Le résultat final de l’Assistant **Importer des données** est toujours un indexeur que vous pouvez exécuter à plusieurs reprises.

Sur la page **Indexeur**, vous pouvez accepter le nom par défaut et utiliser l’option de programmation **Exécuter une fois** pour lancer l’exécution immédiatement. 

  ![Définition de l’indexeur](media/cognitive-search-quickstart-blob/indexer-def.png)

Cliquez sur **Envoyer** pour créer et exécuter simultanément l’indexeur.

## <a name="monitor-indexing"></a>Surveiller l’indexation

Les étapes d’enrichissement prennent plus de temps que l’indexation standard basée sur du texte. L’Assistant doit ouvrir la liste de l’indexeur sur la page Vue d’ensemble afin que vous puissiez suivre la progression. Pour une navigation automatique, accédez à la page Vue d’ensemble et cliquez sur **Indexeurs**.

L’avertissement se produit parce que les fichiers JPG et PNG sont des fichiers image, et nous avons omis les compétences OCR dans ce pipeline. Vous trouverez également des notifications de troncation. La Recherche Azure limite l’extraction à 32 000 caractères au niveau Gratuit.

  ![Notification Recherche Azure](./media/cognitive-search-quickstart-blob/indexer-notification.png)

L’indexation et l’enrichissement peuvent prendre de temps, c’est pourquoi l’utilisation de jeux de données de plus petite taille est recommandée pour l’exploration anticipée. 

## <a name="query-in-search-explorer"></a>Requête dans l’Explorateur de recherche

Une fois l’index créé, vous pouvez soumettre des requêtes pour renvoyer des documents à partir de l’index. Dans le portail, utilisez **l’Explorateur de recherche** pour exécuter des requêtes et afficher les résultats. 

1. Sur la page de tableau de bord du service de recherche, cliquez sur **Explorateur de recherche** sur la barre de commandes.

1. Cliquez sur **Modifier l’index** en haut pour sélectionner l’index créé.

1. Entrez une chaîne de recherche pour interroger l’index, telle que `search=Microsoft&searchFields=organizations`.

Les résultats sont renvoyés au format JSON, qui peut être long et difficile à lire, en particulier dans des documents volumineux provenant d’objets BLOB Azure. Si vous ne parvenez pas à analyser facilement les résultats, utilisez CTRL + F pour effectuer une recherche dans les documents. Pour cette requête, vous pourriez rechercher dans le document JSON des termes spécifiques. 

CTRL + F peut également vous permettre de déterminer le nombre de documents figurant dans un jeu de résultats donné. Pour les objets BLOB Azure, le portail choisit « metadata_storage_path » comme clé, car chaque valeur est unique pour le document. À l’aide de CTRL + F, recherchez « metadata_storage_path » pour obtenir le nombre de documents. 

  ![Exemple de l’explorateur de recherche](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Éléments importants à retenir

Vous avez maintenant terminé votre premier exercice d’indexation enrichie cognitive. Ce guide de démarrage rapide visait à présenter des concepts importants et à vouq faire utiliser l’Assistant pour que vous puissiez rapidement développer un prototype de recherche cognitive à l’aide de vos propres données.

Parmi les concepts clés, que nous espérons que vous avez compris, figurent la dépendance à l’égard des sources de données Azure. L’enrichissement de la recherche cognitive est lié à des indexeurs, qui sont spécifiques d’Azure et de la source. Bien que ce guide de démarrage rapide utilise le stockage Blob Azure, d’autres sources de données Azure sont possibles. Pour plus d’informations, consultez [Indexeurs dans Recherche Azure](search-indexer-overview.md).

Un autre concept important réside dans le fait que les compétences fonctionnent sur les champs d’entrée. Dans le portail, vous devez choisir un champ source unique pour toutes les compétences. Dans le code, les entrées peuvent être d’autres champs, ou la sortie d’une compétence en amont.

 Les entrées relatives à une compétence sont mappées sur un champ de sortie dans un index. En interne, le portail configure les [annotations](cognitive-search-concept-annotations-syntax.md) et définit un [ensemble de compétences](cognitive-search-defining-skillset.md), en établissant l’ordre des opérations et le flux général. Ces étapes sont masquées dans le portail, mais lorsque vous démarrez l’écriture de code, ces concepts deviennent importants.

Enfin, vous avez appris que l’affichage des résultats est effectué en interrogeant l’index. Au final, Recherche Azure fournit un index dans lequel il est possible d’effectuer une recherche et que vous pouvez interroger à l’aide de la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou [entièrement étendue](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Un index contenant des champs enrichis ressemble à n’importe quel autre index. Si vous souhaitez intégrer des [analyseurs personnalisés](search-analyzers.md) ou standard, des [profils de notation](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), des [synonymes](search-synonyms.md), des [filtres à facettes](search-filters-facets.md), une recherche géographique ou tout autre fonctionnalité Recherche Azure, vous pouvez certainement le faire.

## <a name="clean-up-resources"></a>Supprimer des ressources

Le moyen le plus rapide pour nettoyer après une exploration consiste à supprimer le groupe de ressources contenant le service Recherche Azure et le service Blob Azure.  

En supposant que vous avez placé ces deux services dans le même groupe, supprimez maintenant le groupe de ressources pour supprimer définitivement tout ce qu’il contient, y compris les services et tout le contenu stocké que vous avez créé pour cet exercice. Dans le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble de chaque service.

## <a name="next-steps"></a>Étapes suivantes

Selon la façon dont vous avez approvisionné la ressource Cognitive Services, vous pouvez expérimenter l’indexation et l’enrichissement en réexécutant l’Assistant avec diverses compétences et différents champs de données source. Pour répéter les étapes, supprimez l’index et un indexeur, puis recréez l’indexeur avec un nouveau mélange de sélections.

+ Dans **Vue d’ensemble** > **Index**, sélectionnez l’index que vous avez créé, puis cliquez sur **Supprimer**.

+ Dans **Vue d’ensemble**, double-cliquez sur la vignette **Indexeurs**. Recherchez l’indexeur créé et supprimez-le.

Vous pouvez également réutiliser les exemples de données et de services créés et apprendre à effectuer les mêmes tâches par programme dans le tutoriel suivant. 

> [!div class="nextstepaction"]
> [Tutoriel : Découvrir les API REST de la recherche cognitive](cognitive-search-tutorial-blob.md)
