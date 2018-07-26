---
title: Utiliser un projet de base de données U-SQL afin de développer une base de données U-SQL pour Azure Data Lake | Microsoft Docs
description: Découvrez comment développer une base de données U-SQL avec Azure Data Lake Tools pour Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890713"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Utiliser un projet de base de données U-SQL afin de développer une base de données U-SQL pour Azure Data Lake

Une base de données U-SQL permet d’avoir des vues structurées des données non structurées, de gérer des données structurées dans des tables et de fournir un système de catalogue de métadonnées générales pour organiser vos données structurées et personnaliser un code. La base de données est le concept qui regroupe ces objets associés.

En savoir plus sur la [base de données U-SQL et sur le langage de définition de données (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Un projet de base de données U-SQL est un type de projet dans Visual Studio qui permet aux développeurs de développer, de gérer et de déployer rapidement et facilement leurs bases de données U-SQL.

## <a name="create-a-u-sql-database-project"></a>Créer un projet de base de données U-SQL

Après la version 2.3.3000.0, Azure Data Lake Tools pour Visual Studio a ajouté un nouveau modèle de projet appelé projet de base de données U-SQL. Pour créer un projet U-SQL, parcourez **Fichier > Nouveau > Projet**. Le projet de base de données U-SQL se trouve sous **Azure Data Lake > nœud U-SQL**.

![Data Lake Tools pour Visual Studio crée un projet de base de données u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Développer des objets de base de données U-SQL à l’aide du projet de base de données

Cliquez avec le bouton droit sur le projet de base de données U-SQL, puis cliquez sur **Ajouter > Nouvel élément**. Tous les types d’objet pris en charge se trouvent dans l’Assistant Ajouter un nouvel élément. 

1.  Pour un objet non-assembly, par exemple, une fonction table, un nouveau script U-SQL est créé après l’ajout d’un nouvel élément. Vous pouvez commencer à développer l’instruction DDL pour cet objet dans l’éditeur.
2.  Pour un objet assembly, l’outil fournit un éditeur d’interface utilisateur convivial qui vous permet d’inscrire l’assembly et de déployer des fichiers .dll, ainsi que des fichiers supplémentaires. Suivez les étapes ci-dessous pour ajouter une définition d’objet assembly au projet de base de données U-SQL :

1.  Ajouter des références du projet C# qui comprend UDO/UDAG/UDF au projet de base de données U-SQL.

    ![Data Lake Tools pour Visual Studio ajoute une référence de projet de base de données u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools pour Visual Studio ajoute une référence de projet de base de données u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  En mode création d’assembly, choisissez l’assembly référencé dans la liste déroulante **Créer un assembly à partir de la référence**.

    ![Data Lake Tools pour Visual Studio crée un assembly à partir de la référence.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Ajouter des **dépendances gérées** et des **fichiers supplémentaires** le cas échéant. Lorsque vous ajoutez des fichiers supplémentaires, l’outil utilise le chemin d’accès relatif pour garantir que les assemblys seront trouvés tant sur votre ordinateur local que, ultérieurement, sur l’ordinateur de build. @_DeployTempDirectory est une variable prédéfinie qui pointe l’outil vers le dossier de sortie de build. Dans la sortie de build, chaque assembly a un sous-dossier portant son nom, dans lequel se trouvent tous les fichiers dll et supplémentaires. 
 
## <a name="build-u-sql-database-project"></a>Générer un projet de base de données U-SQL

La sortie de build du projet de base de données U-SQL est un package de déploiement de base de données U-SQL dont le nom est suivi du suffixe `.usqldbpack`. Le package `.usqldbpack` est un fichier zip incluant toutes les instructions DDL dans un seul script U-SQL du dossier **DDL**, ainsi que tous les fichiers dll et supplémentaires dans le dossier **Temp**.

En savoir plus sur [Comment générer un projet de base de données U-SQL avec une ligne de commande MSBuild et une tâche de génération Visual Studio Team Service](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Déployer une base de données U-SQL

Le package .usqldbpack peut être déployé sur un compte local ou sur un compte Azure Data Lake Analytics à l’aide de Visual Studio ou du kit de développement logiciel (SDK) de déploiement. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Déployer une base de données U-SQL dans Visual Studio

Vous pouvez déployer une base de données U-SQL via un projet de base de données U-SQL ou un package .usqldbpack dans Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Déployer via un projet de base de données U-SQL

1.  Cliquez avec le bouton droit sur le projet de base de données U-SQL et choisissez **Déployer**.
2.  Dans l’Assistant de déploiement d’une base de données U-SQL, sélectionnez le **compte ADLA** sur lequel vous souhaitez déployer la base de données. Le compte (local) et le compte ADLA sont pris en charge.
3.  La **source de base de données** est renseignée en pointant automatiquement vers le package .usqldbpack dans le dossier de sortie de génération du projet.
4.  Entrer le **nom de la base de données** pour créer une base de données. S’il existe une base de données du même nom dans le compte Azure Data Lake Analytics cible, tous les objets définis dans le projet de base de données sont créés sans avoir à recréer la base de données.
5.  Cliquez sur **Envoyer** pour déployer la base de données U-SQL. Toutes les ressources (assemblys et fichiers supplémentaires) sont téléchargées et une tâche U-SQL incluant toutes les instructions DDL est envoyée.

    ![Data Lake Tools pour Visual Studio déploie un projet de base de données u-sql.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools pour Visual Studio déploie un Assistant de projet de base de données u-sql.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Déployer via le package de déploiement de base de données U-SQL

1.  Ouvrez l’**Explorateur de serveurs**, développez le **compte Azure Data Lake Analytics** sur lequel vous souhaitez déployer la base de données.
2.  Cliquez avec le bouton droit sur les bases de données U-SQL et choisissez **Déployer la base de données**.
3.  Définissez la **source de base de données** pour le chemin de package (fichier .usqldbpack) de déploiement de base de données U-SQL.
4.  Entrez le **nom de la base de données** pour créer une base de données. S’il existe une base de données du même nom dans le compte Azure Data Lake Analytics cible, tous les objets définis dans le projet de base de données sont créés sans avoir à recréer la base de données.

    ![Data Lake Tools pour Visual Studio déploie un package de base de données u-sql.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools pour Visual Studio déploie un Assistant package de base de données u-sql.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Déployer une base de données U-SQL avec le kit de développement logiciel (SDK)

`PackageDeploymentTool.exe` fournit les interfaces de programmation et de ligne de commande qui vous aident à déployer des bases de données U-SQL. Le kit de développement logiciel (SDK) est inclus dans le [package Nuget du SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), qui se trouve à l’emplacement `build/runtime/PackageDeploymentTool.exe`.

[En savoir plus sur le kit de développement logiciel (SDK) et sur la manière de configurer le pipeline CI/CD pour le déploiement de base de données U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Référencer un projet de base de données U-SQL

Un projet U-SQL peut référencer un projet de base de données U-SQL. La référence affecte deux charges de travail :

- Build de projet : les environnements de base de données référencés sont configurés avant de générer les scripts U-SQL. 
- Exécution locale vs compte (projet local) : les environnements de base de données référencés sont déployés sur le compte (de projet local) avant l’exécution du script U-SQL. [En savoir plus sur l’exécution locale et la différence entre le compte (ordinateur Local) et le compte (projet local) ici](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Comment ajouter une référence de base de données U-SQL

1. Cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sélectionnez **Ajouter la référence de la base de données U-SQL...**.

    ![Data Lake Tools pour Visual Studio ajoute une référence de projet de base de données.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Configurez la référence de base de données à partir d’un projet de base de données U-SQL dans la solution actuelle ou d’un fichier de package de base de données U-SQL.
3. Indiquez le nom de la base de données.

    ![Data Lake Tools pour Visual Studio ajoute un Assistant de référence de projet de base de données.](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Étapes suivantes

- [Comment configurer un pipeline CI/CD pour Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Comment tester votre code Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Exécuter un script U-SQL sur votre ordinateur local](data-lake-analytics-data-lake-tools-local-run.md)
