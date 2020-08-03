---
title: 'Tutoriel : Démarrer la création d’un espace de travail Synapse'
description: Dans ce tutoriel, vous allez découvrir comment créer un espace de travail Synapse, un pool SQL et un pool Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b4d48dcc8f09ae8e2ec3bb198f8864de1c945682
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093540"
---
# <a name="create-a-synapse-workspace"></a>Créer un espace de travail Synapse

Dans ce tutoriel, vous allez découvrir comment créer un espace de travail Synapse, un pool SQL et un pool Apache Spark. 

## <a name="prepare-a-storage-account"></a>Préparer un compte de stockage

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Créez un compte de stockage avec les paramètres suivants :

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom du compte de stockage**| Choisissez n’importe quel nom.| Dans ce document, nous allons utiliser **contosolake**.|
    |Concepts de base|**Type de compte**| **StorageV2** ||
    |Concepts de base|**Lieu**|Choisissez un emplacement.| Nous vous conseillons de choisir votre espace de travail Azure Synapse Analytics et votre compte Azure Data Lake Storage Gen2 dans la même région.|
    |Avancé|**Data Lake Storage Gen2**|**Activé**| Azure Synapse fonctionne seulement avec les comptes de stockage pour lesquels ce paramètre est activé.|
    |||||

1. Après avoir créé le compte de stockage, sélectionnez **Contrôle d’accès (IAM)** dans le volet gauche. Attribuez ensuite les rôles suivants ou vérifiez qu’ils sont déjà attribués :
    * Attribuez-vous le rôle **Propriétaire**.
    * Attribuez-vous le rôle **Propriétaire des données Blob du stockage**.
1. Dans le volet de gauche, sélectionnez **Conteneurs** et créez un conteneur.
1. Attribuez au conteneur un nom de votre choix. Dans ce document, nous appellerons le conteneur **users**.
1. Acceptez le paramètre par défaut **Niveau d’accès public**, puis sélectionnez **Créer**.

À l’étape suivante, vous configurerez votre espace de travail Azure Synapse pour utiliser ce compte de stockage comme compte de stockage « principal » et le conteneur pour stocker les données de l’espace de travail. L’espace de travail stocke les données dans des tables Apache Spark. Il stocke les journaux des applications Spark dans un dossier appelé **/synapse/workspacename**.

## <a name="create-a-synapse-workspace"></a>Créer un espace de travail Synapse

1. Ouvrez le [Portail Azure](https://portal.azure.com) et, en haut, recherchez **Synapse**.
1. Dans les résultats de la recherche, sous **Services**, sélectionnez **Azure Synapse Analytics (préversion des espaces de travail)** .
1. Sélectionnez **Ajouter** pour créer un espace de travail avec ces paramètres :

    |Onglet|Paramètre | Valeur suggérée | Description |
    |---|---|---|---|
    |Concepts de base|**Nom de l’espace de travail**|Vous pouvez lui donner le nom que vous voulez.| Dans ce document, nous l’appellerons **myworkspace**.|
    |Concepts de base|**Région**|Choisissez la même région que le compte de stockage.|

1. Sous **sélectionnez Data Lake Storage Gen 2**, sélectionnez le compte et le conteneur que vous avez créés précédemment.
1. Sélectionnez **Vérifier + créer** > **Créer**. Votre espace de travail est prêt en quelques minutes.

## <a name="verify-access-to-the-storage-account"></a>Vérifier l’accès au compte de stockage

Les identités managées pour votre espace de travail Azure Synapse ont peut-être déjà accès au compte de stockage. Vérifiez ce point en effectuant ces étapes :

1. Ouvrez le [Portail Azure](https://portal.azure.com) et le compte de stockage principal choisi pour votre espace de travail.
1. Dans le volet de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Attribuez les rôles suivants ou vérifiez qu’ils sont déjà attribués. Nous utilisons le même nom pour l’identité de l’espace de travail et le nom de l’espace de travail.
    * Pour le rôle **Contributeur aux données Blob du stockage** sur le compte de stockage, affectez **myworkspace** comme identité de l’espace de travail.
    * Attribuez le nom **myworkspace** à l’espace de travail.

1. Sélectionnez **Enregistrer**.

## <a name="open-synapse-studio"></a>Ouvrir Synapse Studio

Après avoir créé votre espace de travail Azure Synapse, vous pouvez ouvrir Synapse Studio de deux manières :

* Ouvrez votre espace de travail Synapse dans le [Portail Azure](https://portal.azure.com). En haut de la section **Vue d’ensemble**, sélectionnez **Lancer Synapse Studio**.
* Accédez à `https://web.azuresynapse.net` et connectez-vous à votre espace de travail.

## <a name="create-a-sql-pool"></a>Créer un pool SQL

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools SQL**.
1. Sélectionnez **Nouveau** et renseignez les paramètres suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|---|
    |**Nom du pool SQL**| **SQLDB1**|
    |**Niveau de performances**|**DW100C**|
    |||

1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool SQL sera prêt en quelques minutes. Votre pool SQL est associé à une base de données du pool SQL qui est également appelée **SQLDB1**.

Un pool SQL consomme des ressources facturables tant qu’il est actif. Vous pouvez suspendre le pool plus tard pour réduire les coûts.

## <a name="create-an-apache-spark-pool"></a>Créer un pool Apache Spark

1. Dans Synapse Studio, dans le volet de gauche, sélectionnez **Gérer** > **Pools Apache Spark**.
1. Sélectionnez **Nouveau** et renseignez les paramètres suivants :

    |Paramètre | Valeur suggérée | 
    |---|---|---|
    |**Nom du pool Apache Spark**|**Spark1**
    |**Taille du nœud**| **Petite**|
    |**Nombre de nœuds**| Définissez la valeur minimale sur 3 et la valeur maximale sur 3|

1. Sélectionnez **Vérifier + créer** > **Créer**. Votre pool Apache Spark sera prêt en quelques secondes.

> [!NOTE]
> Malgré le nom, un pool Apache Spark n’est pas identique à un pool SQL. Il s’agit simplement de quelques métadonnées de base que vous utilisez pour indiquer à l’espace de travail Azure Synapse comment interagir avec Spark.

Comme ce sont des métadonnées, les pools Spark ne peuvent pas être démarrés ni arrêtés.

Lorsque vous effectuez une activité Spark dans Azure Synapse, vous spécifiez le pool Spark à utiliser. Le pool indique à Azure Synapse la quantité de ressources Spark à utiliser. Vous payez uniquement les ressources consommées. Quand vous cessez d’utiliser activement le pool, les ressources expirent automatiquement et sont recyclées.

> [!NOTE]
> Les bases de données Spark sont créées indépendamment à partir des pools Spark. Un espace de travail est toujours associé à une base de données Spark par défaut appelée **default**. Vous pouvez créer d’autres bases de données Spark.

## <a name="the-sql-on-demand-pool"></a>Le pool SQL à la demande

Chaque espace de travail est fourni avec un pool prédéfini appelé **SQL à la demande**. Ce pool ne peut pas être supprimé. Le pool SQL à la demande vous permet d’utiliser SQL sans devoir créer ni gérer un pool SQL dans Azure Synapse.

Contrairement aux autres types de pools, la facturation du pool SQL à la demande est basée sur la quantité de données numérisées (pas sur la quantité de ressources consommées) pour exécuter la requête.

* Le pool SQL à la demande possède ses propres bases de données SQL à la demande qui existent indépendamment de tout pool SQL à la demande.
* Un espace de travail a toujours un seul pool SQL à la demande nommé **SQL à la demande**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser avec un pool SQL](get-started-analyze-sql-pool.md)
