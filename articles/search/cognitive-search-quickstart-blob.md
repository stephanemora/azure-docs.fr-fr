---
title: "Démarrage rapide : Créer un index enrichi par l'IA sur le portail Azure - Recherche Azure"
description: Compétences en extraction de données, en langage naturel et en traitement d’images sur le portail d’indexation de la Recherche Azure, à l’aide du portail Azure et d’exemples de données.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8f3a1dadaddb423a83f4c3691a4b5747a5196d2a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795328"
---
# <a name="quickstart-create-an-ai-indexing-pipeline-using-cognitive-skills-in-azure-search"></a>Démarrage rapide : Créer un pipeline d'indexation basé sur l'IA à l'aide de compétences cognitives dans Recherche Azure

Grâce à son intégration à [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), la Recherche Azure apporte au pipeline d’indexation des compétences en extraction des données, en traitement en langage naturel (NLP) et en traitement des images. Ainsi, il est plus facile d’effectuer des recherches dans le contenu non structuré et dans le contenu dont les éléments sont impossibles à rechercher. 

De nombreuses ressources Cognitive Services, telles que la [reconnaissance optique de caractères ](cognitive-search-skill-ocr.md), la [détection de la langue](cognitive-search-skill-language-detection.md) et la [reconnaissance d’entité](cognitive-search-skill-entity-recognition.md), peuvent être attachées à un processus d’indexation. Les algorithmes d’intelligence artificielle de Cognitive Services sont utilisés pour détecter des modèles, des fonctionnalités et des caractéristiques dans les données sources, en renvoyant des structures et du contenu textuel qui peuvent être utilisés dans les solutions de recherche en texte intégral reposant sur la Recherche Azure.

Dans ce guide de démarrage rapide, créez votre premier pipeline d’enrichissement dans le [portail Azure](https://portal.azure.com) avant d’écrire une seule ligne de code :

> [!div class="checklist"]
> * Commencer avec les exemples de données dans le stockage d’objets blob Azure
> * Configurer l’Assistant [**Importer des données**](search-import-data-portal.md) pour l’indexation et l’enrichissement cognitifs 
> * Exécutez l’Assistant (une compétence d’entité détecte les personnes, l’emplacement et les organisations)
> * Utiliser l’[**Explorateur de recherche**](search-explorer.md) pour interroger les données enrichies

Ce guide de démarrage rapide est exécuté dans le service gratuit. Par conséquent, le nombre de transactions gratuites par jour est limité à 20 documents. Si vous souhaitez exécuter ce guide de démarrage rapide plusieurs fois par jour, utilisez un ensemble de fichiers plus petit pour pouvoir intégrer davantage d'exécutions.

> [!NOTE]
> Si vous élargissez le champ en augmentant la fréquence des traitements, en ajoutant des documents supplémentaires ou en ajoutant des algorithmes d'IA, vous devrez [joindre une ressource Cognitive Services facturable](cognitive-search-attach-cognitive-services.md). Des frais sont applicables durant l’appel des API dans Cognitive Services ainsi que pour l’extraction d’images durant la phase d’extraction du contenu des documents du service Recherche Azure. L’extraction de texte à partir des documents est gratuite.
>
> L'exécution des compétences intégrées est facturée au prix actuel du [paiement à l'utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les prix appliqués pour l'extraction d'images sont présentés sur la [page de tarification du service Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

[Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide.

[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) fournit des fonctionnalités d’intelligence artificielle. Ce guide de démarrage rapide inclut les étapes permettant d’ajouter ces ressources en ligne, lorsque vous spécifiez le pipeline. Il n’est pas nécessaire de configurer des comptes à l’avance.

Les services Azure doivent fournir les entrées au pipeline d’indexation. Vous pouvez utiliser n’importe quelle source de données prise en charge par les [indexeurs de la Recherche Azure](search-indexer-overview.md), à l’exception du stockage Table Azure, qui n’est pas pris en charge pour l’indexation basée sur l’intelligence artificielle. Ce guide de démarrage rapide utilise le [stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) comme conteneur pour les fichiers de données sources. 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Configurer le service Blob Azure et charger les données d’exemple

1. [Téléchargez les exemples de données](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistant en un petit ensemble de fichiers de types différents. 

1. [Inscrivez-vous au stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), créez un compte de stockage, ouvrez les pages des services Blob et créez un conteneur.  Créez le compte de stockage dans la même région que la Recherche Azure.

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

## <a name="clean-up"></a>Nettoyer

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

Selon la façon dont vous avez approvisionné la ressource Cognitive Services, vous pouvez expérimenter l’indexation et l’enrichissement en réexécutant l’Assistant avec diverses compétences et différents champs de données source. Pour répéter les étapes, supprimez l’index et un indexeur, puis recréez l’indexeur avec un nouveau mélange de sélections.

+ Dans **Vue d’ensemble** > **Index**, sélectionnez l’index que vous avez créé, puis cliquez sur **Supprimer**.

+ Dans **Vue d’ensemble**, double-cliquez sur la vignette **Indexeurs**. Recherchez l’indexeur créé et supprimez-le.

Vous pouvez également réutiliser les exemples de données et de services créés et apprendre à effectuer les mêmes tâches par programme dans le tutoriel suivant. 

> [!div class="nextstepaction"]
> [Tutoriel : Découvrir les API REST de la recherche cognitive](cognitive-search-tutorial-blob.md)
