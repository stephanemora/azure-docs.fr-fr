---
title: 'Didacticiel : Rechercher des données non structurées dans le stockage Blob Azure'
description: 'Didacticiel : Recherche de données non structurées dans le stockage Blob à l’aide de la recherche Azure.'
author: roygara
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 12/13/2018
ms.author: rogarana
ms.custom: mvc
ms.openlocfilehash: 8c11d2e6d9a902707d3fd98f09d3474a5d2a5f64
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150788"
---
# <a name="tutorial-search-unstructured-data-in-cloud-storage"></a>Didacticiel : Rechercher des données non structurées dans le stockage cloud

Dans ce tutoriel, vous apprenez à rechercher des données non structurées à l’aide de la [recherche Azure](../../search/search-what-is-azure-search.md), en utilisant des données stockées dans le stockage Blob Azure. Les données non structurées sont des données qui ne sont pas organisées de manière prédéfinie ou qui n’ont pas de modèle de données. Il s’agit par exemple d’un fichier .txt.

Pour suivre ce tutoriel, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe de ressources
> * Créez un compte de stockage.
> * Créez un conteneur.
> * Télécharger des données vers votre conteneur
> * Créer un service de recherche via le portail
> * Connecter un service de recherche à un compte de stockage
> * Création d'une source de données
> * Configurer l’index
> * Créer un indexeur
> * Utiliser le service de recherche pour rechercher votre conteneur

## <a name="prerequisites"></a>Prérequis

Chaque compte de stockage doit appartenir à un groupe de ressources Azure. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Quand vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant. Ce tutoriel crée un groupe de ressources.

Connectez-vous au [Portail Azure](https://portal.azure.com).

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

Vous allez utiliser un exemple de jeu de données préparé spécialement pour ce tutoriel. Téléchargez [clinical-trials.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials.zip) et décompressez-le dans son propre dossier.

L’exemple comprend des fichiers texte obtenus auprès de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Ce tutoriel utilise ces exemples de fichiers texte pour les recherches effectuées à l’aide des services de recherche Azure.

## <a name="create-a-container"></a>Créez un conteneur.

Les conteneurs s’apparentent à des dossiers et servent à stocker les objets Blob.

Dans ce didacticiel, vous utilisez un seul conteneur pour stocker les fichiers texte obtenus auprès de clinicaltrials.gov.

1. Accédez à votre compte de stockage dans le portail Azure.

2. Sélectionnez **Parcourir les objets blob** sous **Service BLOB.**

3. Ajoutez un nouveau conteneur.

4. Nommez ce conteneur **données** et sélectionnez **Conteneur** pour le niveau d’accès public.

5. Sélectionnez **OK** pour créer le conteneur.

   ![Recherche de données non structurées](media/storage-unstructured-search/storageactinfo.png)

## <a name="upload-the-example-data"></a>Charger l’exemple de données

Maintenant que vous avez un conteneur, vous pouvez y charger votre exemple de données.

1. Sélectionnez votre conteneur, puis **Charger**.

2. Sélectionnez l’icône de dossier bleue à côté du champ **Fichiers** et accédez au dossier local où vous avez extrait l’exemple de données.

3. Sélectionnez tous les fichiers extraits, puis sélectionnez **Ouvrir**.

4. Sélectionnez **Charger** pour lancer le processus de chargement.

   ![Recherche de données non structurées](media/storage-unstructured-search/upload.png)

Le processus de chargement peut durer quelques minutes.

Une fois que ce processus est terminé, accédez à votre conteneur de données pour vérifier que les fichiers texte ont bien été chargés.

  ![Recherche de données non structurées](media/storage-unstructured-search/clinicalfolder.png)

## <a name="create-a-search-service"></a>Créer un service de recherche

La recherche Azure est une solution cloud de service de recherche qui fournit aux développeurs des API et des outils permettant d’ajouter une expérience de recherche dans des données.

Dans ce tutoriel, vous utilisez un service de recherche pour faire des recherches dans les fichiers texte obtenus auprès de clinicaltrials.gov.

1. Accédez à votre compte de stockage dans le portail Azure.

2. Faites défiler vers le bas et sélectionnez **Ajouter Recherche Azure** sous **SERVICE BLOB**.

3. Sous **Importer des données**, sélectionnez **Choisir votre service**.

4. Sélectionnez **Cliquez ici pour créer un service de recherche**.

5. Dans **Nouveau service de recherche**, entrez un nom unique pour votre service de recherche dans le champ **URL**.

6. Sous **Groupe de ressources**, sélectionnez **Utiliser l’existant** et choisissez le groupe de ressources que vous avez créé précédemment.

7. Comme **Niveau tarifaire**, sélectionnez le niveau **Gratuit** et cliquez sur **Sélectionner**.

8. Sélectionnez **Créer** pour créer le service de recherche.

   ![Recherche de données non structurées](media/storage-unstructured-search/createsearch2.png)

## <a name="connect-your-search-service-to-your-container"></a>Connecter votre service de recherche à votre conteneur

Maintenant que vous avez un service de recherche, vous pouvez l’attacher à votre stockage d’objets blob. Cette section vous guide tout au long des processus de choix d’une source de données, de création d’un index et de création d’un indexeur.

1. Accédez à votre compte de stockage.

2. Sélectionnez **Ajouter Recherche Azure** sous **SERVICE BLOB**.

3. Sélectionnez **Service de recherche** dans **Importer des données**, puis cliquez sur le service de recherche que vous avez créé dans la section précédente. La fenêtre **Nouvelle source de données** s’ouvre.

### <a name="create-a-data-source"></a>Création d'une source de données

  Une source de données spécifie les données à indexer et comment accéder à ces données. Le même service de recherche peut utiliser une source de données plusieurs fois.

1. Entrez un nom pour la source de données. Sous **Données à extraire**, sélectionnez **Contenu et métadonnées**. La source de données spécifie les parties des objets blob à indexer.

2. Étant donné que les objets blob que vous utilisez sont des fichiers texte, définissez **Mode d’analyse** sur **Texte**.

   ![Recherche de données non structurées](media/storage-unstructured-search/datasources.png)

3. Sélectionnez **Conteneur de stockage** pour répertorier les comptes de stockage disponibles.

4. Sélectionnez votre compte de stockage, puis sélectionnez le conteneur que vous avez créé précédemment.

   ![Recherche de données non structurées](media/storage-unstructured-search/datacontainer.png)

5. Cliquez sur **Sélectionner** pour revenir à **Nouvelle source de données**, puis sélectionnez **OK** pour continuer.

### <a name="configure-the-index"></a>Configurer l’index

  Un index est une collection de champs issus de votre source de données dans lesquels vous pouvez effectuer des recherches. Vous devez définir et configurer les paramètres de ces champs pour indiquer au service de recherche de quelle manière effectuer des recherches dans vos données.

1. Dans **Importer des données**, sélectionnez **Personnaliser l’index cible**.

2. Entrez un nom pour votre index dans le champ **Nom de l’index**.

3. Cochez la case de l’attribut **Récupérable** sous **metadata_storage_name**.

   ![Recherche de données non structurées](media/storage-unstructured-search/valuestoselect.png)

4. Sélectionnez **OK** pour accéder à la fenêtre **Créer un indexeur**.

Les paramètres de l’index et les attributs que vous donnez à ces paramètres sont importants. Les paramètres spécifient *quelles* données stocker et les attributs indiquent *comment* stocker ces données.

La colonne **NOM DU CHAMP** contient les paramètres. Le tableau suivant fournit la liste des attributs disponibles et leurs descriptions.

#### <a name="field-attributes"></a>Attributs de champ

| Attribut | Description |
| --- | --- |
| *Clé* |Chaîne fournissant un ID unique à chaque document utilisé pour rechercher des documents. Chaque index doit avoir une clé. Un seul champ peut être la clé, et son type doit être défini sur Edm.String. |
| *Affichable dans les résultats d’une recherche* |Définit si un champ peut être retourné dans un résultat de recherche. |
| *Filtrable* |Permet d’utiliser le champ dans des requêtes de filtre. |
| *Triable* |Permet à une requête de trier les résultats de recherche à l’aide de ce champ. |
| *À choix multiple* |Permet d’utiliser un champ pour le filtrage autonome dans une structure de navigation par facettes par un utilisateur. En général, les champs contenant des valeurs répétitives que vous pouvez utiliser pour regrouper des documents (par exemple, plusieurs documents correspondant à la même marque ou catégorie de service) sont les mieux adaptés en tant que facettes. |
| *Possibilité de recherche* |Indique que le champ peut faire l’objet d’une recherche en texte intégral. |

### <a name="create-an-indexer"></a>Créer un indexeur

  Un indexeur connecte une source de données à un index de recherche et permet de planifier la réindexation de vos données.

1. Entrez un nom dans le champ **Nom**, puis sélectionnez **OK**.

   ![Recherche de données non structurées](media/storage-unstructured-search/exindexer.png)

2. Vous revenez alors dans **Importer des données**. Sélectionnez **OK** pour terminer le processus de connexion.

Vous avez maintenant connecté votre objet blob à votre service de recherche. Le portail a besoin de quelques minutes pour montrer que l’index est rempli. Toutefois, le service de recherche démarre l’indexation immédiatement afin que vous puissiez effectuer des recherches tout de suite.

## <a name="search-your-text-files"></a>Rechercher dans vos fichiers texte

Pour rechercher dans vos fichiers, ouvrez l’Explorateur de recherche dans l’index de votre service de recherche nouvellement créé.

Les étapes suivantes vous indiquent où trouver l’Explorateur de recherche en vous fournissant quelques exemples de requêtes :

1. Accédez à toutes les ressources et recherchez le service de recherche que vous venez de créer.

   ![Recherche de données non structurées](media/storage-unstructured-search/exampleurl.png)

2. Sélectionnez votre index pour l’ouvrir.

   ![Recherche de données non structurées](media/storage-unstructured-search/overview.png)

3. Sélectionnez **Explorateur de recherche** pour ouvrir l’Explorateur de recherche, où vous pouvez effectuer des requêtes actives sur vos données.

   ![Recherche de données non structurées](media/storage-unstructured-search/indexespane.png)

4. Sélectionnez **Rechercher** alors que le champ de chaîne de requête est vide. Une requête vide retourne *toutes* les données de vos objets blob.

   ![Recherche de données non structurées](media/storage-unstructured-search/emptySearch.png)

### <a name="perform-a-full-text-search"></a>Effectuer une recherche en texte intégral

Entrez **Myopia** dans le champ **Chaîne de requête**, puis sélectionnez **Rechercher**. Cette étape démarre une recherche dans le contenu des fichiers et retourne un sous-ensemble de ces fichiers contenant le mot « Myopia ».

  ![Recherche de données non structurées](media/storage-unstructured-search/secondMyopia.png)

### <a name="perform-a-system-properties-search"></a>Effectuer une recherche de propriétés système

En plus d’une recherche en texte intégral, vous pouvez créer des requêtes pour effectuer des recherches par propriétés système à l’aide du paramètre `$select`.

Entrez `$select=metadata_storage_name` dans la chaîne de requête et appuyez sur **Entrée**. Seul ce champ précis est retourné.

La chaîne de requête modifie directement l’URL, donc les espaces ne sont pas autorisés. Pour rechercher dans plusieurs champs, utilisez une virgule, comme ici : `$select=metadata_storage_name,metadata_storage_path`

Vous pouvez uniquement utiliser le paramètre `$select` avec des champs qui ont été marqués comme récupérables lors de la définition de votre index.

  ![Recherche de données non structurées](media/storage-unstructured-search/metadatasearchunstructured.png)

Vous avez maintenant terminé ce didacticiel et vous avez un ensemble de données non structurées dans lequel vous pouvez effectuer des recherches.

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer les ressources que vous avez créées, le plus simple est de supprimer le groupe de ressources. La suppression du groupe de ressources supprime aussi toutes les ressources qu’il contient. Dans l’exemple suivant, la suppression du groupe de ressources supprime aussi le compte de stockage et le groupe de ressources.

1. Dans le portail Azure, accédez à la liste des groupes de ressources associés à votre abonnement.
2. Sélectionnez le groupe de ressources que vous souhaitez supprimer.
3. Sélectionnez le bouton **Supprimer le groupe de ressources** et entrez le nom du groupe de ressources dans le champ de suppression.
4. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Suivez ce lien pour en savoir plus sur l’indexation des documents avec la recherche Azure :

> [!div class="nextstepaction"]
> [Indexation de documents dans Stockage Blob Azure avec Recherche Azure](../../search/search-howto-indexing-azure-blob-storage.md)
