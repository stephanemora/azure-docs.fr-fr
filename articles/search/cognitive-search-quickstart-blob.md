---
title: 'Démarrage rapide : Créer un ensemble de compétences dans le portail Azure'
titleSuffix: Azure Cognitive Search
description: Dans ce guide de démarrage rapide du portail, découvrez comment utiliser l’Assistant Importation de données pour ajouter des compétences cognitives à un pipeline d’indexation dans Recherche cognitive Azure. Les compétences incluent l’OCR (reconnaissance optique de caractères) et le traitement en langage naturel.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 5e891627b337a0a3a15d0ebfa2b9cc95f27feca4
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533116"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Démarrage rapide : Créer un ensemble de compétences cognitives pour la Recherche cognitive Azure dans le portail Azure

Un ensemble de compétences est une fonctionnalité IA (intelligence artificielle) qui extrait des informations et une structure à partir de fichiers texte ou de fichiers image non différenciés et volumineux. L’ensemble de compétences les rend indexables et offre des possibilités de recherche à l’aide de requêtes de recherche en texte intégral dans la Recherche cognitive Azure. 

Dans ce guide de démarrage rapide, vous allez combiner les services et les données du cloud Azure pour créer l’ensemble de compétences. Une fois tout en place, vous allez exécuter l’Assistant **Importation de données** dans le portail pour tout rassembler. Le résultat final est un index de recherche qui contient des données créées par le traitement IA, que vous pouvez interroger dans le portail ([Explorateur de recherche](search-explorer.md)).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-services-and-load-data"></a>Créer des services et charger des données

Ce guide de démarrage rapide utilise la Recherche cognitive Azure, le Stockage Blob Azure et [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pour l’IA. 

En raison de la taille réduite de la charge de travail, Cognitive Services est utilisé en arrière-plan pour traiter gratuitement jusqu’à 20 transactions par jour, quand il est appelé à partir de la Recherche cognitive Azure. Du moment que vous utilisez les exemples de données que nous fournissons, vous pouvez ignorer la création ou l’attachement d’une ressource Cognitive Services.

1. [Téléchargez les exemples de données](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistant en un petit ensemble de fichiers de types différents. Décompressez les fichiers

1. [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) ou [recherchez un compte existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Choisissez la même région que celle de la Recherche cognitive Azure. Choisissez le type de compte StorageV2 (V2 à usage général) si vous souhaitez tester la fonctionnalité de base de connaissances plus tard, au cours d’une autre procédure pas à pas. Sinon, choisissez n’importe quel type.

1. Ouvrez les pages des services BLOB et créez un conteneur. Vous pouvez utiliser le niveau d’accès public par défaut. 

1. Dans le conteneur, cliquez sur **Charger** pour charger les exemples de fichiers que vous avez téléchargés au cours de la première étape. Notez que vous disposez d’un large éventail de types de contenu, notamment des images et des fichiers d’application qui ne peuvent pas faire l’objet de recherches en texte intégral dans leurs formats natifs.

   ![Fichiers source sur le Stockage Blob Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Vous êtes maintenant prêt à passer à l’Assistant Importation de données.

## <a name="run-the-import-data-wizard"></a>Exécuter l’Assistant Importation de données

Dans la page Vue d’ensemble du service Recherche, cliquez dans la barre de commandes sur **Importer des données** pour configurer l’enrichissement cognitif en quatre étapes.

  ![Commande Importer des données](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Étape 1 : Création d'une source de données

1. Dans **Connexion à vos données**, choisissez **Stockage Blob Azure**, sélectionnez le compte de stockage et le conteneur que vous avez créés. Donnez un nom à la source de données et utilisez les valeurs par défaut pour le reste. 

   ![Configuration d’objets blob Azure](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Passez à la page suivante.

### <a name="step-2-add-cognitive-skills"></a>Étape 2 : Ajouter des compétences cognitives

Ajoutez ensuite des compétences cognitives pour appeler le traitement en langage naturel. Les exemples de données se composent de 12 fichiers. L’allocation gratuite de 20 transactions sur Cognitive Services est donc suffisante pour ce guide de démarrage rapide. Dans la mesure où nous n’utilisons pas d’OCR, seuls les fichiers qui ne sont pas des fichiers image sont comptés, craqués et utilisés dans ce processus.

1. Pour ce guide de démarrage rapide, nous utilisons la ressource Cognitive Services au niveau **Gratuit**.

   ![Attacher Cognitive Services](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Développez **Ajouter des compétences**, puis sélectionnez les compétences qui permettent d’effectuer le traitement en langage naturel. Pour ce guide démarrage rapide, choisissez la reconnaissance d’entité pour les personnes, les organisations et les emplacements.

   ![Attacher Cognitive Services](media/cognitive-search-quickstart-blob/skillset.png)

1. Acceptez le champ source par défaut : `content`. Cela semble peut-être une petite cible, mais pour les objets blob Azure, le champ `content` contient la majeure partie du document d’objet blob (par exemple un document Word ou un diaporama PowerPoint), ce qui en fait un bon candidat.

1. Passez à la page suivante.

> [!NOTE]
> Les compétences en matière de traitement en langage naturel fonctionnent sur le contenu de texte dans l’exemple de jeu de données. Étant donné que nous n’avons pas sélectionné l’option OCR, les fichiers JPEG et PNG trouvés dans l’exemple de jeu de données ne seront pas traités dans ce guide de démarrage rapide. 

### <a name="step-3-configure-the-index"></a>Étape 3 : Configurer l’index

Dans la Recherche cognitive Azure, un index contient le contenu pouvant faire l’objet de recherches. L’Assistant **Importation de données** peut généralement créer le schéma à votre place en échantillonnant la source de données. Au cours de cette étape, examinez le schéma généré, puis modifiez éventuellement les paramètres. Vous trouverez ci-dessous le schéma par défaut créé pour le jeu de données d’objets blob de démonstration.

Pour ce guide de démarrage rapide, l’Assistant effectue un travail de qualité en termes de définition de valeurs par défaut raisonnables : 

+ Le nom par défaut est *azureblob-index* en fonction du type de source de données. 

+ Les champs par défaut sont basés sur le champ des données sources d’origine (`content`) ainsi que sur les champs de sortie (`people`, `organizations` et `locations`) créés par les compétences cognitives. Les types de données par défaut sont déduits à partir des échantillonnages de métadonnées et de données.

+ La clé de document par défaut est *metadata_storage_path* (ce champ est sélectionné, car il contient des valeurs uniques).

+ Les attributs par défaut sont **Récupérable** et **PossibilitéRecherche** pour ces champs. **Possibilité de recherche** indique qu’un champ peut faire l’objet d’une recherche. **Récupérable** signifie qu’il peut être renvoyé dans les résultats. L’Assistant suppose que vous souhaitez ces champs récupérables et interrogeables, car vous les avez créés par l’intermédiaire de compétences.

  ![Champs d’index](media/cognitive-search-quickstart-blob/index-fields.png)

Remarquez la zone barrée et le point d’interrogation sur l’attribut **Récupérable** près du champ `content`. Pour les documents d’objets blob comportant beaucoup de texte, le champ `content` contient la majeure partie du fichier qui peut atteindre des milliers de lignes. Si vous devez transmettre le contenu du fichier au code client, assurez-vous que **Récupérable** reste sélectionné. Sinon, pensez à décocher cet attribut sur `content` si les éléments extraits (`people`, `organizations`, et `locations`) sont suffisants pour vos besoins.

Marquer un champ comme étant **Récupérable** ne signifie pas que le champ *doit* être présent dans les résultats de recherche. Vous pouvez contrôler avec précision la composition des résultats de recherche à l’aide du paramètre de requête **$select** pour spécifier les champs à inclure. Pour les champs comportant beaucoup de texte, tels que `content`, le paramètre **$select** constitue votre solution pour fournir aux utilisateurs humains de votre application des résultats de recherche faciles à gérer, tout en garantissant au code client l’accès à toutes les informations dont il a besoin via l’attribut **Récupérable**.
  
Passez à la page suivante.

### <a name="step-4-configure-the-indexer"></a>Étape 4 : Configurer l’indexeur

L’indexeur est une ressource de niveau supérieur qui gère le processus d’indexation. Il spécifie le nom de la source de données, un index cible et la fréquence d’exécution. L’Assistant **Importation de données** crée plusieurs objets, dont un indexeur qui est toujours présent et que vous pouvez exécuter à plusieurs reprises.

1. Dans la page **Indexeur**, vous pouvez accepter le nom par défaut et cliquer sur l’option de planification **Une fois** pour l’exécuter immédiatement. 

   ![Définition de l’indexeur](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Cliquez sur **Envoyer** pour créer et exécuter simultanément l’indexeur.

## <a name="monitor-status"></a>Superviser l’état

L’indexation des compétences cognitives prend plus de temps que l’indexation textuelle classique. Pour superviser la progression, accédez à la page Vue d’ensemble, puis cliquez sur **Indexeurs** au centre de la page.

L’avertissement se produit, dans la mesure où les fichiers image JPG et PNG se trouvent dans la source de données et que nous avons omis la compétence OCR dans ce pipeline. Vous trouverez également des notifications de troncation. L’extraction est limitée à 32 000 caractères au niveau Gratuit.

  ![Notification de la Recherche cognitive Azure](./media/cognitive-search-quickstart-blob/indexer-notification.png)

L’indexation et l’enrichissement peuvent prendre de temps, c’est pourquoi l’utilisation de jeux de données de plus petite taille est recommandée pour l’exploration anticipée. 

Dans le portail Azure, vous pouvez également superviser le journal d’activité Notifications, et rechercher le lien d’état cliquable **Notification Recherche cognitive Azure**. L’exécution peut prendre plusieurs minutes.

## <a name="query-in-search-explorer"></a>Requête dans l’Explorateur de recherche

Une fois l’index créé, vous pouvez soumettre des requêtes pour renvoyer des documents à partir de l’index. Dans le portail, utilisez **l’Explorateur de recherche** pour exécuter des requêtes et afficher les résultats. 

1. Sur la page de tableau de bord du service de recherche, cliquez sur **Explorateur de recherche** sur la barre de commandes.

1. Cliquez sur **Modifier l’index** en haut pour sélectionner l’index créé.

1. Entrez une chaîne de recherche pour interroger l’index, telle que `search=Microsoft&searchFields=Organizations`.

Les résultats sont renvoyés au format JSON, qui peut être long et difficile à lire, en particulier dans des documents volumineux provenant d’objets BLOB Azure. Si vous ne parvenez pas à analyser facilement les résultats, utilisez CTRL + F pour effectuer une recherche dans les documents. Pour cette requête, vous pourriez rechercher dans le document JSON des termes spécifiques. 

CTRL + F peut également vous permettre de déterminer le nombre de documents figurant dans un jeu de résultats donné. Pour les objets BLOB Azure, le portail choisit « metadata_storage_path » comme clé, car chaque valeur est unique pour le document. À l’aide de CTRL + F, recherchez « metadata_storage_path » pour obtenir le nombre de documents. 

  ![Exemple de l’explorateur de recherche](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Éléments importants à retenir

Vous avez créé votre premier ensemble de compétences et appris d’importants concepts utiles pour le prototypage d’une solution de recherche enrichie à l’aide de vos propres données.

Parmi les concepts clés, que nous espérons que vous avez compris, figurent la dépendance à l’égard des sources de données Azure. Un ensemble de compétences est lié à un indexeur. Les indexeurs sont spécifiques à Azure et à la source. Bien que ce guide de démarrage rapide utilise le stockage Blob Azure, d’autres sources de données Azure sont possibles. Pour plus d’informations, consultez [Indexeurs dans la Recherche cognitive Azure](search-indexer-overview.md).

Un autre concept important réside dans le fait que les compétences fonctionnent sur les champs d’entrée. Dans le portail, vous devez choisir un champ source unique pour toutes les compétences. Dans le code, les entrées peuvent être d’autres champs, ou la sortie d’une compétence en amont.

La sortie est dirigée vers un index de recherche. Il existe un mappage entre les paires nom-valeur créées durant l’indexation et les champs individuels de votre index. En interne, le portail configure les [annotations](cognitive-search-concept-annotations-syntax.md) et définit un [ensemble de compétences](cognitive-search-defining-skillset.md), en établissant l’ordre des opérations et le flux général. Ces étapes sont masquées dans le portail, mais lorsque vous démarrez l’écriture de code, ces concepts deviennent importants.

Enfin, vous avez appris que vous pouvez vérifier le contenu en interrogeant l’index. Pour finir, la Recherche cognitive Azure fournit un index offrant des possibilités de recherche, que vous pouvez interroger à l’aide de la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) ou de la [syntaxe de recherche entièrement étendue](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Un index contenant des champs enrichis ressemble à n’importe quel autre index. Si vous souhaitez intégrer des analyseurs standard ou [personnalisés](search-analyzers.md), des [profils de scoring](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), des [synonymes](search-synonyms.md), des [filtres à facettes](search-filters-facets.md), une recherche géographique ou toute autre fonctionnalité liée à la Recherche cognitive Azure, vous pouvez le faire sans problème.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

> [!Tip]
> Si vous souhaitez répéter cet exercice ou essayer une autre procédure pas à pas d’enrichissement par IA, supprimez l’indexeur du portail. La suppression de l’indexeur réinitialise le compteur de transactions quotidiennes gratuites à zéro pour le traitement Cognitive Services.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez créer des ensembles de compétences à l’aide du portail, du kit SDK .NET ou de l’API REST. Pour approfondir vos connaissances, essayez l’API REST avec Postman et d’autres exemples de données.

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter une structure à du « contenu non structuré » avec l’enrichissement par IA](cognitive-search-tutorial-blob.md)