---
title: Tutoriel sur l’indexation, l’interrogation et le filtrage dans Recherche Azure à l’aide du portail | Microsoft Docs
description: Dans ce tutoriel, utilisez le portail Azure et des exemples de données prédéfinis pour générer un index dans Recherche Azure. Explorez la recherche en texte intégral, les filtres, les facettes, la recherche partielle, la recherche géographique, et bien davantage.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 124963359d0b2d4050156958de195e47b9331c92
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007982"
---
# <a name="tutorial-use-built-in-tools-for-azure-search-indexing-and-queries"></a>Tutoriel : Utiliser des outils intégrés pour l’indexation et les requêtes dans Recherche Azure

Dans une page de service Recherche Azure du portail Azure, vous pouvez utiliser des outils intégrés pour le test du concept et une expérience pratique en un minimum de temps. Les outils du portail n’offrent pas de parité complète avec les API .NET et REST, mais les assistants et les éditeurs proposent un soutien facile pour les tests de validation techniques rapides. Cette présentation dénuée de code est destinée à vous familiariser avec un petit jeu de données publiées pour vous permettre d’écrire immédiatement des requêtes intéressantes. 

> [!div class="checklist"]
> * Démarrez avec un échantillon de données publiques et générez automatiquement un index Recherche Azure à l’aide de l’Assistant **Importer des données**. 
> * Affichez le schéma d’index et les attributs de n’importe quel index publié dans Recherche Azure.
> * Explorez la recherche en texte intégral, les filtres, les facettes, la recherche partielle et la recherche géographique avec **l’Explorateur de recherche**.  

Les outils du portail ne prennent pas en charge la totalité des fonctionnalités de Recherche Azure. Si les outils sont trop limités, envisagez d’utiliser une [introduction à la programmation Azure Search basée sur le code dans .NET](search-howto-dotnet-sdk.md) ou des [outils de test web pour effectuer des appels d’API REST](search-fiddler.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. Vous pouvez également visionner une démonstration de 6 minutes de la procédure décrite dans ce tutoriel à partir de la troisième minute environ de cette [vidéo de présentation du service Recherche Azure](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Prérequis

[Créez un service Recherche Azure](search-create-service-portal.md) ou recherchez un service existant dans votre abonnement actuel. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur le tableau de bord des services de votre service Azure Search. Si vous n’avez pas épinglé la vignette du service à votre tableau de bord, vous pouvez trouver votre service en procédant comme suit : 
   
   * Dans la barre de lancement, cliquez sur **Tous les services** dans le volet de navigation de gauche.
   * Dans la zone de recherche, tapez *recherche* pour obtenir la liste des services de recherche associés à votre abonnement. Cliquez sur **Rechercher des services**. Votre service doit apparaître dans la liste. 

### <a name="check-for-space"></a>Vérifier l’espace disponible
De nombreux clients commencent avec le service gratuit. Cette version est limitée à trois index, trois sources de données et trois indexeurs. Avant de commencer, assurez-vous de disposer d’assez d’espace pour stocker des éléments supplémentaires. Ce didacticiel crée une occurrence de chaque objet. 

> [!TIP] 
> Les vignettes figurant sur le tableau de bord des services indiquent le nombre d’index, d’indexeurs et de sources de données dont vous disposez déjà. La vignette Indexeurs affiche des indicateurs de réussite et d’échec. Cliquez sur cette vignette pour visualiser le nombre d’indexeurs. 
>
> ![Vignettes Indexeurs et Sources de données][1]
>

## <a name="create-index"></a> Créer un index et charger des données
Les requêtes de recherche se répètent sur un [*index*](search-what-is-an-index.md) contenant les données de recherche, les métadonnées et les constructions utilisées pour l’optimisation de certains comportements de recherche.

Pour conserver cette tâche sur le portail, nous utilisons un exemple de jeu de données intégré qui est analysable à l’aide d’un [*indexeur*](search-indexer-overview.md) par le biais de l’Assistant **Importer des données**. Un indexeur est un robot d’indexation spécifique à la source qui peut lire les métadonnées et le contenu des sources de données prises en charge Azure. Dans le code, vous pouvez créer et gérer des indexeurs en tant que ressources indépendantes. Dans le portail, les indexeurs sont exposés via l’Assistant **Importer des données**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Étape 1 : Démarrer l’Assistant Importer des données
1. Dans votre tableau de bord de service Azure Search, cliquez sur **Importer des données** dans la barre de commandes pour lancer un assistant qui crée et remplit un index.
   
    ![Commande Importer des données][2]

2. Dans l’Assistant, cliquez sur **Se connecter aux données** > **Exemples** > **realestate-us-sample**. Cette source de données est préconfigurée avec un nom, un type et des informations de connexion. Une fois créée, elle devient une « source de données existante » qui peut être réutilisée dans d’autres opérations d’importation.

    ![Sélection d’un exemple de jeu de données][9]

3. Cliquez sur **OK** pour utiliser cette source.

#### <a name="skip-cognitive-skills"></a>Ignorer les compétences cognitives

**Importer des données** inclut une étape facultative de compétences cognitives qui ajoute des algorithmes d’intelligence artificielle à l’indexation. Cette fonctionnalité n’est pas abordée dans ce tutoriel. Vous pouvez passer directement à la section **Personnaliser l’index cible**. Si vous êtes intéressé par la nouvelle fonctionnalité de recherche cognitive en préversion dans Recherche Azure, essayez le [démarrage rapide](cognitive-search-quickstart-blob.md) ou le [tutoriel](cognitive-search-tutorial-blob.md) de Recherche cognitive.

   ![Ignorer l’étape des compétences cognitives][11]

#### <a name="step-2-define-the-index"></a>Étape 2 : définir l’index
La création d’un index est généralement manuelle et basée sur du code, mais l’Assistant peut générer un index pour toutes les sources de données qu’il peut analyser. Un index requiert au minimum un nom et une collection de champs, l’un de ces champs étant désigné comme clé du document pour identifier chaque document de manière unique.

Les champs comportent des types de données et des attributs. Les cases à cocher figurant dans la partie supérieure sont des *attributs d’index* qui contrôlent le mode d’utilisation du champ. 

* **Récupérable** signifie que le champ s’affiche dans la liste des résultats de recherche. En décochant cette case, vous pouvez marquer des champs individuels comme hors limites pour les résultats de recherche, par exemple lorsqu’un champ est utilisé uniquement dans les expressions de filtre. 
* Les options **Filtrable**, **Triable** et **À choix multiples** déterminent si le champ peut être utilisé dans un filtre, un tri ou une structure de navigation à facettes. 
* **Possibilité de recherche** signifie que le champ est inclus dans la recherche en texte intégral. Les chaînes sont utilisables dans une recherche. Les champs numériques et booléens sont souvent marqués comme ne pouvant pas faire l’objet d’une recherche. 

Par défaut, l’Assistant analyse la source de données pour y rechercher des identificateurs uniques comme base pour le champ de clé. Les chaînes sont dotées d’attributs les définissant comme récupérables et utilisables dans une recherche. Les entiers présentent des attributs les définissant comme récupérables, filtrables, triables et à choix multiples.

  ![Index généré pour la source realestate][3]

Cliquez sur **OK** pour créer l’index.

#### <a name="step-3-define-the-indexer"></a>Étape 3 : définir l’indexeur
Toujours dans l’Assistant **Importer des données**, cliquez sur **Indexeur** > **Nom**, puis tapez un nom pour l’indexeur. 

Cet objet définit un processus exécutable. Vous pouvez le configurer en planification récurrente, mais pour l’instant, utilisez l’option par défaut qui exécute l’indexeur une seule fois dès que vous cliquez sur **OK**.  

  ![Indexeur de la source realestate][8]

## <a name="check-progress"></a>Vérification de la progression
Pour surveiller le processus d’importation des données, revenez au tableau de bord des services, faites défiler la page vers le bas, puis double-cliquez sur la vignette **Indexeurs** pour ouvrir la liste des indexeurs. L’indexeur que vous venez de créer devrait apparaître dans la liste, avec un état indiquant que l’opération est en cours ou qu’elle a réussi, ainsi que le nombre de documents indexés.

   ![Message de progression de l’indexeur][4]

## <a name="view-the-index"></a>Afficher l’index

Les vignettes du tableau de bord du service fournissent des informations résumées ainsi que qu’un accès aux informations détaillées. Par exemple, dans la vignette **Index**, une liste des index existants doit s’afficher, comprenant l’index *realestate-us-sample* que vous avez créé à l’étape précédente.

Cliquez sur l’index *realestate-us-sample* maintenant pour afficher les options du portail pour sa définition. L’option **Ajouter/Modifier des champs** vous permet de créer et d’attribuer de nouveaux champs. Les champs existants ont une représentation physique dans Recherche Azure et ne sont donc pas modifiables, pas même dans le code. Pour modifier considérablement un champ existant, créez-en un nouveau et supprimez l’original. 

   ![définition de l’index d’exemples][10]

D’autres constructions, telles que des profils de score et des options CORS, peuvent être ajoutées à tout moment. 

Pour comprendre clairement ce que vous pouvez et ne pouvez pas modifier lors de la conception d’index, prenez une minute pour consulter les options de définition d’index. Les options grisées indiquent qu’une valeur ne peut pas être modifiée ou supprimée.

## <a name="query-index"></a> Interroger l’index
Vous devriez maintenant avoir un index de recherche prêt à lancer des requêtes à l’aide de la page de requête [**Explorateur de recherche**](search-explorer.md) intégrée. Il fournit une zone de recherche afin que vous puissiez tester les chaînes de requête arbitraires. 

> [!TIP]
> La procédure qui suit fait l’objet d’une démonstration à 06 min 08 s dans la [vidéo de présentation du service Recherche Azure](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Cliquez sur **Explorateur de recherche** dans la barre de commandes.

   ![Commande Explorateur de recherche][5]

2. Dans la barre de commandes, cliquez sur **Modifier l’index** pour basculer vers la source de données *realestate-us-sample*. Dans la barre de commandes, cliquez sur **Définir l’API de version** pour découvrir les API REST disponibles. Pour les requêtes ci-après, utilisez la version mise à la disposition générale (2017-11-11). 

   ![Commandes d’index et d’API][6]

3. Dans la barre de recherche, entrez les chaînes de recherche ci-dessous, puis cliquez sur **Rechercher**.

    > [!NOTE]
    > L’**Explorateur de recherche** n’est équipé que pour gérer la [demande d’API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). Il accepte à la fois la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) et la [syntaxe de l’analyseur de requêtes complètes Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), ainsi que tous les paramètres de recherche disponibles dans les opérations [Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 


#### <a name="example-string-searchseattle"></a>Exemple (chaîne) : `search=seattle`

+ Le paramètre **recherche** permet d’entrer une recherche par mot clé pour une recherche en texte intégral et, dans ce cas précis, renvoie les entrées du Comté de King, dans l’État de Washington, dont le document contient le terme *Seattle* dans l’un des champs utilisables dans une recherche. 

+ **L’Explorateur de recherche** renvoie les résultats au format JSON, qui est particulièrement détaillé et difficile à lire si les documents présentent une structure dense. Cela est intentionnel ; la visibilité du document entier est un cas d’utilisation important, en particulier lors du test. Pour une meilleure expérience utilisateur, vous devrez écrire le code qui [gère les résultats de recherche](search-pagination-page-layout.md) pour mettre en évidence les éléments importants.

+ Les documents sont composés de tous les champs marqués comme « récupérables » dans l’index. Pour visualiser les attributs d’index dans le portail, cliquez sur *realestate-us-sample* dans la vignette **Index**.

#### <a name="example-parameterized-searchseattlecounttruetop100"></a>Exemple (paramétrable) : `search=seattle&$count=true&$top=100`

+ Le symbole **&** permet d’ajouter des paramètres de recherche, qui peuvent être spécifiés dans n’importe quel ordre. 

+  Le paramètre **$count=true** récupère une valeur indiquant le nombre total de documents renvoyés. Cette valeur s’affiche en haut des résultats de recherche. Vous pouvez vérifier les requêtes de filtre en surveillant les modifications signalées par **$count=true**. Des petits nombres indiquent que votre filtre fonctionne.

+ La chaîne **$top=100** renvoie les 100 documents les mieux classés du nombre total de documents. Par défaut, le service Recherche Azure renvoie les 50 meilleures correspondances. Vous pouvez augmenter ou diminuer ce nombre par le biais du paramètre **$top**.

## <a name="filter-query"></a>Filtrer la requête

Les filtres sont inclus dans les demandes de recherche lorsque vous ajoutez le paramètre **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Exemple (filtré) : `search=seattle&$filter=beds gt 3`

+ Le paramètre **$filter** renvoie les résultats correspondant aux critères que vous avez spécifiés. Dans ce cas précis, la recherche renvoie les entrées présentant un nombre de chambres supérieur à 3. 

+ La syntaxe de filtre est une construction OData. Pour plus d’informations, consultez l’article [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Syntaxe d’expression de filtre OData).

## <a name="facet-query"></a> « Facetter » la requête

Les filtres de facettes sont inclus dans les demandes de recherche. Vous pouvez utiliser le paramètre de facette pour retourner un nombre agrégé des documents qui correspondent à la valeur de facette que vous fournissez. 

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Exemple (par facettes avec une étendue réduite) : `search=*&facet=city&$top=2`

+ **search=*** est une recherche vide. Les recherches vides portent sur tous les éléments. L’un des motifs possibles de l’exécution d’une requête vide est l’application de filtres ou de facettes au jeu complet de documents. Par exemple, vous souhaitez obtenir une structure de navigation par facettes constituée de toutes les villes dans l’index.

+  **facet** renvoie une structure de navigation que vous pouvez transmettre à un contrôle d’interface utilisateur. Il renvoie des catégories ainsi qu’un nombre. Dans ce cas précis, les catégories reposent sur le nombre de villes. Le service Recherche Azure ne propose aucune fonction d’agrégation, mais vous pouvez bénéficier d’une fonction quasiment comparable par le biais du paramètre `facet`, qui renvoie un nombre de documents dans chaque catégorie.

+ **$top=2** renvoie deux documents, illustrant ainsi la possibilité d’utiliser `top` pour réduire ou augmenter les résultats.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Exemple (par facettes sur des valeurs numériques) : `search=seattle&facet=beds`**

+ Cette requête définit une facette correspondant au nombre de chambres dans une recherche de texte portant sur *Seattle*. Le terme *beds* peut être spécifié en tant que facette, car ce champ est désigné comme récupérable, filtrable et à choix multiples dans l’index, et les valeurs qu’il contient (valeurs numériques de 1 à 5) sont adaptées à un classement des entrées en différents groupes (entrées comportant 3 chambres, entrées comportant 4 chambres, etc.). 

+ Seuls les champs filtrables peuvent être désignés comme étant à facettes. Les résultats ne peuvent renvoyer que les champs récupérables.

## <a name="highlight-query"></a> Ajouter la mise en surbrillance

La mise en surbrillance des correspondances fait référence au formatage du texte qui correspond au mot clé, lorsque des correspondances sont trouvées dans un champ spécifique. Si votre terme de recherche est profondément enfoui dans une description, vous pouvez définir une mise en surbrillance des correspondances pour le localiser plus facilement. 

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Exemple (surligneur) : `search=granite countertops&highlight=description`

+ Dans cet exemple, l’expression mise en forme *granite countertops* est plus facile à repérer dans le champ de description.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Exemple (analyse linguistique) : `search=mice&highlight=description`

+ La recherche en texte intégral recherche les formes d’un mot qui présentent une sémantique similaire. Dans ce cas précis, les résultats de la recherche présentent le texte « mouse » en surbrillance en réponse à une recherche portant sur le mot clé « mice ». Les résultats peuvent afficher différentes formes du même mot grâce à l’exécution d’une analyse linguistique. 

+ Le service Recherche Azure prend en charge 56 analyseurs Lucene et Microsoft. Par défaut, le service Recherche Azure utilise l’analyseur Lucene standard. 

## <a name="fuzzy-search"></a> Essayer la recherche partielle

Dans une recherche classique, aucune correspondance n’est renvoyée pour les termes de requête mal orthographiés, comme *samamish* pour le plateau Samammish dans la région de Seattle. L’exemple suivant ne retourne aucun résultat.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Exemple (terme mal orthographié, non pris en charge) : `search=samamish`

Pour gérer les fautes d’orthographe, vous pouvez utiliser une recherche partielle. La recherche partielle est activée lorsque vous utilisez la syntaxe de requête complète Lucene, ce qui arrive lorsque vous effectuer deux actions : définir la requête sur **queryType=full** et ajouter le **~** à la chaîne de recherche. 

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Exemple (terme mal orthographié, pris en charge) : `search=samamish~&queryType=full`

Cet exemple retourne désormais les documents qui contiennent des correspondances pour « Sammamish ».

Lorsque l’élément **queryType** n’est pas spécifié, l’analyseur de requêtes simples par défaut est utilisé. L’analyseur de requêtes simples fonctionne plus rapidement, mais si vous avez besoin d’utiliser des recherches partielles, des expressions régulières, des recherches de proximité ou d’autres types de requêtes avancées, vous devrez recourir à la syntaxe complète. 

La recherche partielle et la recherche par caractères génériques ont des conséquences sur les résultats de la recherche. L’analyse linguistique n’est pas effectuée sur ces formats de requête. Avant d’effectuer la recherche partielle et la recherche par caractères génériques, consultez [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md#stage-2-lexical-analysis) et recherchez la section sur les exceptions dans l’analyse lexicale.

Pour plus d’informations sur les scénarios de requête autorisés par l’analyseur de requêtes complètes, consultez l’article [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Syntaxe de requête Lucene dans le service Recherche Azure).

## <a name="geo-search"></a> Essayez la recherche géospatiale

La recherche géographique est prise en charge par le biais du [type de données edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) sur un champ contenant des coordonnées. La recherche géographique est un type de filtre, spécifié dans l’article [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Syntaxe d’expression de filtre OData). 

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Exemple (filtres géo-coordonnés) : `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

L’exemple de requête ci-dessus filtre tous les résultats sur la base de données positionnelles et renvoie les résultats situés à moins de 5 kilomètres d’un point donné (spécifié sous la forme de coordonnées de latitude et de longitude). L’ajout du paramètre **$count** vous permet de connaître le nombre de résultats renvoyés lorsque vous modifiez la distance ou les coordonnées. 

La recherche géographique est utile si votre application de recherche dispose d’une fonctionnalité « rechercher à proximité » ou qu’elle utilise la navigation dans les cartes. Toutefois, cette fonction de recherche n’est pas disponible en texte intégral. Si vos utilisateurs ont besoin de rechercher des villes ou des pays par leur nom, ajoutez des champs contenant les noms de ville ou de pays, en plus des coordonnées.

## <a name="takeaways"></a>Éléments importants à retenir

Ce tutoriel présente les étapes de base d’utilisation de l’Assistant **Importer des données** et de l’**Explorateur de recherche** dans le portail Azure.

En tant que force motrice de l’Assistant Importer des données, vous avez découvert les [indexeurs](search-indexer-overview.md), ainsi que le flux de travail de base pour la conception d’index, y compris [les modifications prises en charge pour un index publié](ttps://docs.microsoft.com/rest/api/searchservice/update-index). 

Vous avez découvert la syntaxe de requête par le biais d’exemples pratiques illustrant des fonctionnalités clés telles que les filtres, la mise en surbrillance des correspondances, la recherche partielle et la recherche basée sur la géolocalisation.

Enfin, vous avez appris à obtenir des informations relatives aux index, indexeurs et sources de données que vous avez créés dans votre abonnement, en cliquant sur les vignettes du tableau de bord. Plus tard, lorsque vous travaillerez avec vos propres index ou ceux créés par vos collègues, vous pourrez utiliser le portail pour vérifier rapidement une définition de source de données, ou la construction d’une collection de champs, sans avoir à effectuer de recherches dans un code inconnu.

## <a name="clean-up-resources"></a>Supprimer les ressources

Le moyen le plus rapide de procéder à un nettoyage après un tutoriel consiste à supprimer le groupe de ressources contenant le service Recherche Azure. Vous pouvez maintenant supprimer le groupe de ressources pour supprimer définitivement tout ce qu’il contient. Sur le portail, le nom du groupe de ressources figure dans la page Vue d’ensemble du service Recherche Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour une exploration supplémentaire de Recherche Azure basée sur les outils, utilisez un outil de test REST tel que Postman ou Fiddler :

> [!div class="nextstepaction"]
> [Outils de test web pour appeler les API REST de la Recherche Azure](search-fiddler.md)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
[10]: ./media/search-get-started-portal/sample-index-def.png
[11]: ./media/search-get-started-portal/skip-cog-skill-step.png