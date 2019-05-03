---
title: Importer des données dans un index de recherche à l’aide du portail Azure - Recherche Azure
description: Découvrez comment utiliser l’Assistant Importer des données dans le portail Azure pour explorer des données Azure à partir de Cosmos DB, de Stockage Blob, de Stockage Table, de SQL Database et de SQL Server sur des machines virtuelles Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a0eefe38fdffd04bb95826f960771bd6430ea687
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024774"
---
# <a name="import-data-wizard-for-azure-search"></a>Assistant Importation de données pour Recherche Azure

Le portail Azure propose, sur le tableau de bord Azure Search, un Assistant **Importer des données** qui vous permet de charger des données dans un index. En coulisses, l’Assistant configure et appelle une *source de données*, un *index* et un *indexeur*, ce qui permet d’automatiser plusieurs étapes du processus d’indexation : 

* Connexion à une source de données externe dans le même abonnement Azure.
* Intégration, si vous le souhaitez, du traitement de reconnaissance optique de caractères ou de langage naturel pour l’extraction de texte à partir de données non structurées.
* Génération d’un index basé sur l’échantillonnage des données et les métadonnées de la source de données externe.
* Analyse de la source de données à la recherche de contenu pouvant faire l’objet d’une recherche, sérialisant et chargeant des documents JSON dans l’index.

L’Assistant ne peut pas se connecter à un index prédéfini ou exécuter un indexeur existant, mais dans l’Assistant, vous pouvez configurer un nouvel index ou indexeur pour prendre en charge la structure et les comportements dont vous avez besoin.

Vous découvrez Azure Search ? Parcourez le [Guide de démarrage rapide : Importer, indexer et interroger à l’aide des outils du portail](search-get-started-portal.md) d’essayer l’importation et l’indexation à l’aide de **importer des données** et le jeu de données d’exemple immobilier intégrés.

## <a name="start-importing-data"></a>Commencer l’importation de données

Cette section explique comment démarrer l’Assistant et fournit une vue d’ensemble globale de chaque étape.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page du service de recherche à partir du tableau de bord, ou [recherchez votre service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans la liste.

2. En haut de la page de présentation du service, cliquez sur **Importer des données**.

   ![Commande Importer des données dans le portail](./media/search-import-data-portal/import-data-cmd2.png "Démarrer l’Assistant Importation de données")

   > [!NOTE]
   > Vous pouvez lancer l’**Importation de données** à partir d’autres services Azure, notamment Azure Cosmos DB, Azure SQL Database et Stockage Blob Azure. Recherchez **Ajouter Recherche Azure** dans le volet de navigation de gauche de la page de présentation du service.

3. L’Assistant s’ouvre sur **Se connecter à vos données**, où vous pouvez choisir une source de données externe à utiliser pour cette importation. Il y a plusieurs choses à savoir concernant cette étape, ainsi veillez à lire la section [Entrées de la source de données](#data-source-inputs) pour plus de détails.

   ![Assistant Importation de données dans le portail](./media/search-import-data-portal/import-data-wizard-startup.png "Assistant Importation de données pour Recherche Azure")

4. Ensuite, vient **Ajouter la recherche cognitive**, au cas où vous souhaiteriez inclure la reconnaissance optique de caractères (OCR) de texte dans des fichiers image, ou l’analyse de texte dans des données non structurées. Les algorithmes d’intelligence artificielle de Cognitive Services sont extraits pour cette tâche. Cette étape comporte deux parties :
  
   Tout d’abord, [attachez une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) à un ensemble de compétences Recherche Azure.
  
   Choisissez ensuite les enrichissements de l’intelligence artificielle à inclure dans l’ensemble des compétences. Pour une démonstration de la procédure pas à pas, consultez ce [Guide de démarrage rapide](cognitive-search-quickstart-blob.md).

   Si vous souhaitez simplement importer des données, ignorez cette étape et passez directement à la définition d’index.

5. Ensuite, vient **Personnaliser l’index cible**, où vous pouvez accepter ou modifier le schéma d’index présenté dans l’Assistant. L’Assistant déduit les champs et les types de données en échantillonnant les données et en lisant les métadonnées à partir de la source de données externe.

   Au niveau de chaque champ, [cochez les attributs d’index](#index-definition) pour activer des comportements spécifiques. Si vous ne sélectionnez aucun attribut, votre index est inutilisable. 

6. Ensuite, vient **Créer un indexeur**, qui est un produit de cet Assistant. Un indexeur est un robot qui extrait les données et métadonnées pouvant faire l’objet d’une recherche à partir d’une source de données Azure externe. En sélectionnant la source de données et en y attachant des ensembles de compétences (facultatif) et un index, vous avez configuré un indexeur en suivant chaque étape de l’Assistant.

   Donnez un nom à l’indexeur, puis cliquez sur **Envoyer** pour commencer le processus d’importation. 

Vous pouvez superviser l’indexation dans le portail en cliquant sur l’indexeur dans la liste **Indexeurs**. À mesure que les documents sont chargés, le nombre de documents augmente pour l’index que vous avez défini. Il suffit parfois de quelques minutes pour que la page du portail sélectionne les mises à jour les plus récentes.

Dès le premier document chargé, l’index est prêt pour les requêtes. Vous pouvez utiliser l’[Explorateur de recherche](search-explorer.md) pour cette tâche.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Entrées de la source de données

L’Assistant **Importation de données** crée un objet de source de données persistant en spécifiant les informations de connexion à une source de données externe. L’objet de source de données est utilisé exclusivement avec des [indexeurs](search-indexer-overview.md) et peut être créé pour les sources de données suivantes : 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
* [Stockage Table Azure](cognitive-search-concept-intro.md) (non pris en charge pour les pipelines de [recherche cognitive](search-howto-indexing-azure-tables.md).

Vous pouvez uniquement importer à partir d’une seule table, une vue de base de données ou une structure de données équivalent, mais la structure peut inclure des sous-structures hiérarchiques ou imbriquées. Pour plus d’informations, consultez [comment modéliser des types complexes](search-howto-complex-data-types.md).

Vous devez créer cette structure de données avant d’exécuter l’Assistant, et il doit contenir le contenu. N’exécutez pas le **importer des données** Assistant sur une source de données vide.

|  Sélection | Description |
| ---------- | ----------- |
| **Source de données existante** |Si des indexeurs sont déjà définis dans votre service de recherche, vous pouvez sélectionner une définition de source de données existante pour une autre importation. Dans Recherche Azure, les objets de source de données sont uniquement utilisés par les indexeurs. Vous pouvez créer un objet de source de données programmatiquement ou par le biais de l’Assistant **Importation de données**.|
| **Exemples**| Recherche Azure héberge une gratuit globale Azure SQL database que vous pouvez utiliser pour en savoir plus sur les demandes de requête et de l’importation dans Azure Search. Consultez [Démarrage rapide : Importer, indexer et interroger à l’aide des outils du portail](search-get-started-portal.md) pour une procédure pas à pas. |
| **Base de données SQL Azure** |Le nom du service, les informations d’identification d’un utilisateur de base de données avec autorisation de lecture, ainsi que le nom de la base de données peuvent être spécifiés sur la page ou par le biais d’une chaîne de connexion ADO.NET. Choisissez l’option de chaîne de connexion permettant d’afficher ou de personnaliser les propriétés. <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être spécifiée sur la page. Cette option s’affiche une fois que la connexion aboutit : vous pouvez alors faire votre choix dans une liste déroulante. |
| **SQL Server dans les machines virtuelles Azure** |Spécifiez un nom de service complet, un ID d’utilisateur et un mot de passe, ainsi qu’une base de données pour la chaîne de connexion. Afin d’utiliser cette source de données, vous devez avoir préalablement installé un certificat dans le magasin local pour chiffrer la connexion. Pour obtenir des instructions, reportez-vous à [Connexion de machines virtuelles SQL à Recherche Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être spécifiée sur la page. Cette option s’affiche une fois que la connexion aboutit : vous pouvez alors faire votre choix dans une liste déroulante. |
| **Cosmos DB** |La configuration requise inclut le compte, la base de données et la collection. Tous les documents de la collection seront inclus dans l’index. Vous pouvez définir une requête pour aplatir ou filtrer l’ensemble de lignes, ou laisser la requête vide. Une requête n’est pas nécessaire dans cet Assistant.|
| **Stockage Blob Azure** |La configuration requise inclut le compte de stockage et un conteneur. Si les noms d’objets blob suivent une convention d’affectation de noms virtuelle à des fins de regroupement, vous pouvez indiquer la partie de répertoire virtuel du nom comme dossier sous le conteneur. Consultez la page [Indexation de Stockage Blob](search-howto-indexing-azure-blob-storage.md) pour plus d’informations. |
| **Stockage de tables Azure** |La configuration requise inclut le compte de stockage et un nom de table. Vous pouvez également spécifier une requête pour extraire un sous-ensemble des tables. Consultez la page [Indexation de Stockage Table](search-howto-indexing-azure-tables.md) pour plus d’informations. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Attributs d’index

L’Assistant **Importation de données** génère un index qui sera rempli avec les documents obtenus à partir de la source de données d’entrée. 

Pour un index fonctionnel, assurez-vous que les éléments suivants sont bien définis.

1. Un champ doit être marqué en tant que **Clé**, ce qui permet d’identifier de façon unique chaque document. La **Clé** doit être *Edm.String*. 

   Si des valeurs de champ comportent des espaces ou des tirets, vous devez définir l’option **Clé d’encodage en base-64** dans l’étape **Créer un indexeur**, sous **Options avancées**, pour supprimer le vérification de la validation pour ces caractères.

1. Définissez les attributs d’index de chaque champ. Si vous ne sélectionnez aucun attribut, l’index est pratiquement vide, à l’exception du champ de clé obligatoire. Au minimum, choisissez un ou plusieurs de ces attributs pour chaque champ.
   
   + **Récupérable** retourne le champ dans les résultats de la recherche. Chaque champ qui fournit du contenu aux résultats de recherche doit avoir cet attribut. La définition de ce champ n’a pas d’incidence notable sur la taille de l’index.
   + **Filtrable** permet de référencer le champ dans les expressions de filtre. Chaque champ utilisé dans une expression **$filter** doit avoir cet attribut. Les expressions de filtre sont des correspondances exactes. Les chaînes de texte demeurant intactes, un stockage supplémentaire est nécessaire pour recevoir le contenu textuel.
   + **Possibilité de recherche** permet une recherche en texte intégral. Chaque champ utilisé dans les requêtes de forme libre ou dans les expressions de requête doit avoir cet attribut. Les index inversés sont créés pour chaque champ que vous marquez comme **Possibilité de recherche**.

1. Si vous le souhaitez, définissez ces attributs selon vos besoins :

   + **Triable** permet d’utiliser le champ dans un tri. Chaque champ utilisé dans une expression **$Orderby** doit avoir cet attribut.
   + **À choix multiples** active le champ pour la navigation par facettes. Seuls les champs également marqués comme **Filtrables** peuvent être marqués comme **À choix multiples**.

1. Définissez un **Analyseur** si vous souhaitez une indexation et une interrogation dans une langue améliorée. La valeur par défaut est *Standard Lucene*, mais vous pouvez choisir *Microsoft Anglais* si vous souhaitez utiliser l’analyseur de Microsoft pour le traitement lexical avancé, tel que la résolution des formes verbales et nominales irrégulières.

   + Sélectionnez **Possibilité de recherche** pour activer la liste des **Analyseurs**.
   + Choisissez un analyseur proposé dans la liste. 
   
   Seuls les analyseurs de langage peuvent être spécifiés pour l’instant. L’utilisation d’un analyseur personnalisé ou d’un analyseur non dédié au langage, comme Keyword, Pattern, etc., nécessite du code. Pour plus d’informations sur les analyseurs, consultez [Création d’un index de documents en plusieurs langues](search-language-support.md).

1. Cochez la case **Suggesteur** pour activer les suggestions de requête en saisie semi-automatique sur les champs sélectionnés.


## <a name="next-steps"></a>Étapes suivantes
Suivez ces liens pour en savoir plus sur les indexeurs :

* [Indexation d’Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexation d’Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexation de Stockage Blob](search-howto-indexing-azure-blob-storage.md)
* [Indexation de Stockage Table](search-howto-indexing-azure-tables.md)