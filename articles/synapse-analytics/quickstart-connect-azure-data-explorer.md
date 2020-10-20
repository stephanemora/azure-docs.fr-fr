---
title: 'Démarrage rapide : Connecter Azure Data Explorer à un espace de travail Synapse'
description: Comment connecter un cluster Azure Data Explorer à un espace de travail Synapse à l’aide d’Azure Synapse Apache Spark
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 53092f5c49073098f28d2fd06f38391e858b2b9d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946933"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>Connecter Azure Data Explorer à l’aide de Synapse Apache Spark

Cet article explique comment accéder à des bases de données Azure Data Explorer à partir de Synapse Studio à l’aide de Synapse Apache Spark. 

## <a name="prerequisites"></a>Prérequis

* [Créez un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
* Vous avez un espace de travail Synapse ou vous avez suivi ce [guide de démarrage rapide](./quickstart-create-workspace.md) pour créer un espace de travail 
* Vous avez un pool Synapse Apache Spark ou vous avez suivi ce [guide de démarrage rapide](./quickstart-create-apache-spark-pool-portal.md) pour créer un nouveau pool
* [Créez une application Azure AD en provisionnant une application Azure AD.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Accordez à votre application Azure AD l’accès à votre base de données en suivant [Gérer les autorisations de base de données d’Azure Data Explorer](/azure/data-explorer/manage-database-permissions)

## <a name="navigate-to-synapse-studio"></a>Accéder à Synapse Studio

Dans un espace de travail Synapse, sélectionnez **Lancer Synapse Studio**. Dans la page d’accueil de Synapse Studio, sélectionnez **Données** pour accéder à l’**Explorateur d’objets de données**.

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>Connecter une base de données Azure Data Explorer à un espace de travail Synapse

La connexion d’une base de données Azure Data Explorer à un espace de travail s’effectue via le service lié. Un service lié Azure Data Explorer permet aux utilisateurs de parcourir et d’explorer les données, de les lire et de les écrire à partir d’Apache Spark pour Azure Synapse Analytics et d’exécuter des travaux d’intégration dans un pipeline.

À partir de l’Explorateur d’objets de données, procédez comme suit pour connecter directement un cluster Azure Data Explorer :

1. Sélectionnez l’icône **+** à proximité de Données
2. Sélectionnez **Se connecter à des données externes**
3. Sélectionnez **Azure Data Explorer (Kusto)**
5. Sélectionnez **Continue** (Continuer)
6. Nommez le service lié. Le nom sera affiché dans l’Explorateur d’objets et utilisé par les runtimes Synapse pour se connecter à la base de données. Nous vous recommandons d’utiliser un nom convivial.
7. Sélectionnez le cluster Azure Data Explorer de votre abonnement ou entrez l’URI.
8. Entrez « l’ID de principal de service » et la « clé du principal du service » (vérifiez que ce principal de service dispose d’un accès en affichage à la base de données pour l’opération de lecture et d’un accès à l’ingéreur pour l’ingestion des données).
9. Entrez le nom de la base de données Azure Data Explorer.
10. Cliquez sur **Tester la connexion** pour vérifier que vous disposez des autorisations appropriées.
11. Sélectionnez **Créer**

    ![Nouveau service lié](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (Facultatif) Le test de connexion ne valide pas l’accès en écriture. Assurez-vous que votre ID de principal de service dispose d’un accès en écriture à la base de données Azure Data Explorer.

12. Les clusters et bases de données Azure Data Explorer sont visibles sous l’onglet **Lié** dans la section Azure Data Explorer. 

    ![Parcourir les clusters](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > Dans la version actuelle, les objets de base de données sont renseignés en fonction des autorisations de votre compte AAD sur les bases de données Azure Data Explorer. Lorsque vous exécutez les travaux d’intégration ou les notebooks Apache Spark, les informations d’identification du service de liaison sont utilisées (p. ex., le principal de service).


## <a name="quickly-interact-with-code-generated-actions"></a>Interagir rapidement avec des actions générées par le code

* Lorsque vous cliquez avec le bouton droit sur une base de données ou une table, vous obtenez la liste des mouvements qui déclenchent un exemple de notebook Spark pour la lecture de données, l’écriture de données et le streaming de données vers Azure Data Explorer. 
    [![Nouveaux exemples de notebooks](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* Voici un exemple de données de lecture. Attachez le notebook à votre pool Spark et exécutez la cellule [![Nouveau notebook de lecture](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox).

   > [!NOTE] 
   > La première exécution peut prendre plus de trois minutes pour lancer la session Spark. Les exécutions suivantes seront beaucoup plus rapides.  


## <a name="limitations"></a>Limites
Le connecteur Azure Data Explorer n’est actuellement pas pris en charge avec le réseau virtuel managé Azure Synapse.


## <a name="next-steps"></a>Étapes suivantes

* [Exemple de code avec options avancées](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Connecteur Spark Azure Data Explorer (Kusto)](https://github.com/Azure/azure-kusto-spark)