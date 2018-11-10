---
title: Bonnes pratiques pour gérer les assemblys U-SQL dans le pipeline CI/CD pour Azure Data Lake
description: Découvrez les bonnes pratiques pour gérer les assemblys C# U-SQL dans le pipeline CI/CD avec Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670286"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Bonnes pratiques pour gérer les assemblys U-SQL dans le pipeline CI/CD

Dans cet article, vous allez apprendre à gérer le code source des assemblys U-SQL avec le tout nouveau projet de base de données U-SQL. Vous pouvez également voir comment implémenter un pipeline d’intégration et de déploiement continus (CI/CD) pour l’inscription des assemblys à l’aide d’Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Utiliser le projet de base de données U-SQL pour gérer le code source des assemblys

Le [projet de base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) est un type de projet dans Visual Studio qui permet aux développeurs de développer, de gérer et de déployer rapidement et facilement leurs bases de données U-SQL. Tous les objets de base de données U-SQL (à l’exception des informations d’identification) peuvent être gérés avec le projet de base de données U-SQL. 

La méthode recommandée pour gérer le code source C# des assemblys et les scripts U-SQL DDL d’inscription des assemblys consiste à :

* Utiliser le **projet de base de données U-SQL** pour gérer les scripts U-SQL d’inscription des assemblys.
* Utiliser **Bibliothèque de classes (pour application U-SQL)** afin de gérer le code source C# et les dépendances pour les opérateurs, fonctions et agrégateurs définis par l’utilisateur.
* Utiliser le projet de base de données U-SQL pour référencer le projet de bibliothèque de classes. 

Un projet de base de données U-SQL peut référencer un projet de bibliothèque de classes (pour application U-SQL). Les assemblys inscrits dans la base de données U-SQL peuvent être créés à l’aide de code source C# référencé à partir de ce projet de bibliothèque de classes (pour application U-SQL).

Vous pouvez suivre les étapes de création de projets et d’ajout de références ci-dessous :
1. Créez un projet de bibliothèque de classes (pour application U-SQL) par le biais de **Fichier > Nouveau > Projet**. Le projet se trouve sous le nœud **Azure Data Lake > U-SQL**.
   ![Data Lake Tools pour Visual Studio : créer un projet de bibliothèque de classes C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Ajoutez votre code C# défini par l’utilisateur au projet de bibliothèque de classes (pour application U-SQL). 
3. Créez un projet U-SQL par le biais de **Fichier > Nouveau > Projet**. Le projet se trouve sous le nœud **Azure Data Lake > U-SQL**.
   ![Data Lake Tools pour Visual Studio - Créer un projet de base de données U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Ajoutez une référence au projet de bibliothèque de classes C# pour le projet de base de données U-SQL.

    ![Data Lake Tools pour Visual Studio - Ajouter une référence de projet de base de données U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools pour Visual Studio - Ajouter une référence de projet de base de données U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Créez un script d’assembly dans le projet de base de données U-SQL en **cliquant avec le bouton droit sur le projet, puis en choisissant Ajouter un nouvel élément**.
   ![Data Lake Tools pour Visual Studio : ajouter un script d’assembly](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Ouvrez le script d’assembly puis, en mode création d’assembly, choisissez l’assembly référencé dans le menu déroulant **Créer un assembly à partir de la référence**.

    ![Data Lake Tools pour Visual Studio - Créer un assembly à partir de la référence](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Ajoutez des **dépendances gérées** et des **fichiers supplémentaires**, s’il en existe. Lorsque vous ajoutez des fichiers supplémentaires, l’outil utilise le chemin d’accès relatif pour garantir que les assemblys seront trouvés aussi bien sur votre ordinateur local qu’ultérieurement sur l’ordinateur de build. 

**@_DeployTempDirectory** en bas de la fenêtre de l’éditeur est une variable prédéfinie qui pointe l’outil vers le dossier de sortie de build. Dans le dossier de sortie de build, chaque assembly a un sous-dossier portant son nom. Toutes les DLL et tous les fichiers supplémentaires se trouvent dans ce sous-dossier. 

## <a name="build-a-u-sql-database-project"></a>Générer un projet de base de données U-SQL

La sortie de build du projet de base de données U-SQL est un package de déploiement de base de données U-SQL dont le nom est suivi du suffixe `.usqldbpack`. Le package `.usqldbpack` est un fichier .zip où toutes les instructions DDL se trouvent dans le script U-SQL du dossier **DDL**, et tous les fichiers .dll générés et fichiers supplémentaires pour les assemblys se trouvent dans le dossier **Temp**.

## <a name="deploy-a-u-sql-database"></a>Déployer une base de données U-SQL

Le package `.usqldbpack` peut être déployé sur un compte local ou sur un compte Azure Data Lake Analytics à l’aide de Visual Studio ou du SDK de déploiement. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Déployer une base de données U-SQL dans Visual Studio

Vous pouvez déployer une base de données U-SQL via un projet de base de données U-SQL ou un package `.usqldbpack` dans Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Déployer via un projet de base de données U-SQL

1.  Cliquez avec le bouton droit sur le projet de base de données U-SQL, puis sélectionnez **Déployer**.
2.  Dans **l’Assistant Déployer la base de données U-SQL**, sélectionnez le **compte ADLA** dans lequel vous souhaitez déployer la base de données. Les comptes locaux et les comptes ADLA sont pris en charge.
3.  La **source de base de données** est renseignée automatiquement, et pointe vers le package .usqldbpack situé dans le dossier de sortie de génération du projet.
4.  Pour créer une base de données, entrez un nom dans **Nom de la base de données**. S’il existe une base de données du même nom dans le compte Azure Data Lake Analytics cible, tous les objets définis dans le projet de base de données sont créés sans que vous ayez à recréer la base de données.
5.  Pour déployer la base de données U-SQL, cliquez sur **Envoyer**. Toutes les ressources (assemblys et fichiers supplémentaires) sont chargées, et une tâche U-SQL incluant toutes les instructions DDL est envoyée.

    ![Data Lake Tools pour Visual Studio - Déployer un projet de base de données U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools pour Visual Studio - Assistant Déployer un projet de base de données U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Déployer la base de données U-SQL dans Azure DevOps

`PackageDeploymentTool.exe` fournit les interfaces de programmation et de ligne de commande qui vous aident à déployer des bases de données U-SQL. Le SDK est inclus dans le [package Nuget du SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), qui se trouve à l’emplacement `build/runtime/PackageDeploymentTool.exe`.

Dans Azure DevOps, vous pouvez utiliser une tâche de ligne de commande et ce SDK afin de configurer le pipeline d’automatisation pour l’actualisation de la base de données U-SQL. [En savoir plus sur le kit de développement logiciel (SDK) et sur la manière de configurer le pipeline CI/CD pour le déploiement de base de données U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Étapes suivantes

* [Comment configurer un pipeline CI/CD pour Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Comment tester votre code Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Exécuter un script U-SQL sur votre ordinateur local](data-lake-analytics-data-lake-tools-local-run.md)
