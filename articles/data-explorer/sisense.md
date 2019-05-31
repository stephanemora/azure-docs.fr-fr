---
title: Visualiser les données à partir de l’Explorateur de données Azure à l’aide de Sisense
description: Dans cet article, découvrez comment configurer l’Explorateur de données Azure comme source de données pour Sisense et visualiser les données.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358182"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualiser les données à partir de l’Explorateur de données Azure dans Sisense

Sisense est une plateforme décisionnelle analytique qui vous permet de créer des applications d’analytique qui offrent des expériences utilisateur hautement interactifs. La business intelligence et le signalement de logiciels de tableau de bord permet d’accéder et combiner des données en quelques clics. Vous pouvez vous connecter à des données structurées et non structurées de sources, joindre des tables provenant de plusieurs sources avec un minimum de script et le codage et créer des rapports et tableaux de bord web interactives. Dans cet article, vous allez apprendre à configurer de l’Explorateur de données Azure comme source de données pour Sisense et visualiser les données à partir d’un cluster de l’exemple.

## <a name="prerequisites"></a>Conditions préalables

Vous avez besoin de ce qui suit pour terminer cet article :

* [Téléchargez et installez l’application de Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Créer un cluster et la base de données qui inclut les exemples de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Se connecter à des tableaux de bord Sisense à l’aide du Connecteur JDBC de l’Explorateur de données Azure

1. Télécharger et copier les dernières versions des fichiers jar suivants à *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Ouvrez **Sisense** application.
1. Sélectionnez **données** onglet et sélectionnez **+ ElastiCube** pour créer un nouveau modèle ElastiCube.
    
    ![Sélectionnez ElastiCube](media/sisense/data-select-elasticube.png)

1. Dans **ajouter un nouveau modèle ElastiCube**, nommez le modèle ElastiCube et **enregistrer**.
   
    ![Ajouter un nouveau modèle ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Sélectionnez **+ données**.

    ![Bouton de sélection de données](media/sisense/select-data.png)

1. Dans **sélectionnez le connecteur** , sélectionnez **JDBC générique** connecteur.

    ![Choisissez le Connecteur JDBC](media/sisense/select-connector.png)

1. Dans le **Connect** onglet, renseignez les champs suivants pour le **JDBC générique** connecteur, puis sélectionnez **suivant**.

    ![Paramètres du Connecteur JDBC](media/sisense/jdbc-connector.png)

    |Champ |Description |
    |---------|---------|
    |Chaîne de connexion     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Dossier de fichiers JAR JDBC  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nom de la classe du pilote    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |User Name   |    Nom d’utilisateur AAD     |
    |Mot de passe     |   Mot de passe utilisateur AAD      |

1. Dans le **sélectionner les données** rechercher, onglet **sélectionner la base de données** pour sélectionner la base de données à laquelle vous disposez des autorisations. Dans cet exemple, sélectionnez *test1*.

    ![sélectionner la base de données](media/sisense/select-database.png)

1. Dans *tester* volet (nom de la base de données) :
    1. Sélectionnez le nom de table pour afficher un aperçu de la table et afficher les noms de colonne de table. Vous pouvez supprimer les colonnes inutiles.
    1. Sélectionnez la case à cocher de la table appropriée pour sélectionner cette table. 
    1. Sélectionnez **Terminé**.

    ![Sélectionnez la table](media/sisense/select-table-see-columns.png)    

1. Sélectionnez **Build** pour créer votre jeu de données. 

    * Dans le **Build** fenêtre, sélectionnez **Build**.

      ![Fenêtre de la génération](media/sisense/build-window.png)

    * Attendez que le processus de génération est terminée, puis sélectionnez **génération a réussi**.

      ![La génération a réussi](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Créer des tableaux de bord Sisense

1. Dans **Analytique** onglet, sélectionnez **+**  >  **nouveau tableau de bord** pour créer des tableaux de bord sur ce jeu de données.

    ![Nouveau tableau de bord](media/sisense/new-dashboard.png)

1. Choisir un tableau de bord et sélectionnez **créer**. 

    ![Créer un tableau de bord](media/sisense/create-dashboard.png)

1. Sous **nouveau Widget**, sélectionnez **+ sélectionner des données** pour créer un nouveau widget. 

    ![Ajouter des champs au tableau de bord StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Sélectionnez **+ ajouter davantage de données** pour ajouter des colonnes supplémentaires à votre graphique. 

    ![Ajoutez des données au graphique](media/sisense/add-more-data.png)

1. Sélectionnez **+ Widget** pour créer un autre widget. Widgets de glisser -déplacer pour réorganiser votre tableau de bord.

    ![Tableau de bord Storm](media/sisense/final-dashboard.png)

Vous pouvez maintenant Explorer vos données avec visual analytique, créer des tableaux de bord et transformer des données en informations exploitables pour avoir un impact sur votre entreprise.

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)

