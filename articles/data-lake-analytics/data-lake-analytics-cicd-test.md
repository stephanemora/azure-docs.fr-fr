---
title: Guide pratique pour tester votre code Azure Data Lake Analytics | Microsoft Docs
description: Découvrez comment ajouter des cas de test pour votre U-SQL et un code C# étendu pour Azure Data Lake Analytics.
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
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890706"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Guide pratique pour tester votre code Azure Data Lake Analytics

Azure Data Lake fournit U-SQL comme langage qui combine le langage SQL déclaratif avec le langage C# impératif pour traiter des données quelle que soit l’échelle. Dans ce document, vous allez apprendre à créer des cas de test pour votre langage U-SQL et le code C# UDO (opérateur défini par l’utilisateur) étendu.

## <a name="test-u-sql-scripts"></a>Tester les scripts U-SQL

Le script U-SQL est compilé et optimisé pour le code exécutable et exécuté sur plusieurs ordinateurs sur le cloud ou votre ordinateur local. Le processus de compilation et d’optimisation traite la totalité du script U-SQL à exécuter. Vous ne pouvez pas faire un « test unitaire » traditionnel pour chaque instruction. Cependant, en utilisant le kit SDK de test U-SQL et le kit SDK d’exécution locale, vous pouvez effectuer des tests au niveau du script.

### <a name="create-test-cases-for-u-sql-script"></a>Créer des cas de test pour le script U-SQL

Azure Data Lake Tools pour Visual Studio fournit une bonne expérience pour vous aider à créer des cas de test pour le script U-SQL.

1.  Cliquez avec le bouton droit sur un script U-SQL dans l'Explorateur de solutions et choisissez **Créer un test unitaire**.
2.  Configurez pour créer un nouveau projet de test ou insérez le cas de test dans un projet de test existant.

    ![Data Lake Tools pour Visual Studio, créer un projet de test u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools pour Visual Studio, créer une configuration de projet de test u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Gérer la source de données de test

Lors du test de scripts U-SQL, des fichiers d’entrée de test sont nécessaires. Vous pouvez gérer ces données de test en configurant la **Source des données de test** dans la propriété du projet U-SQL. Lorsque vous appelez l’interface `Initialize()` dans le kit SDK de test U-SQL, un dossier Racine des données local temporaire est créé sous le répertoire de travail du projet de test, et tous les fichiers et sous-dossiers (et les fichiers dans les sous-dossiers) dans le dossier de la source des données de test sont copiés dans le dossier racine des données local temporaire avant d’exécuter les cas de test du script U-SQL. Vous pouvez ajouter plus de dossiers de source des données de test en divisant le chemin du dossier des données de test avec un point-virgule.

![Data Lake Tools pour Visual Studio, configurer la source des données de test du projet](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Gérer l’environnement de base de données pour le test

Si vos scripts U-SQL utilisent des objets de base de données U-SQL ou interrogent à l’aide de ceux-ci, par exemple, pour appeler des procédures stockées, vous devez initialiser l’environnement de base de données avant d’exécuter les cas de test U-SQL. L’interface `Initialize()` dans le kit SDK de test U-SQL vous permet de déployer toutes les bases de données qui sont référencées par le projet U-SQL dans le dossier racine des données local temporaire dans le répertoire de travail du projet de test. 

En savoir plus sur [la manière de gérer les références des projets à la base de données U-SQL pour le projet U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Vérifier les résultats du test

L’interface `Run()` retourne le résultat de l’exécution du travail, 0 signifie la réussite et 1 signifie l’échec. Vous pouvez également utiliser les fonctions C# assert pour vérifier les sorties. 

### <a name="execute-test-cases-in-visual-studio"></a>Exécuter des cas de test dans Visual Studio

Le projet de test du script U-SQL s’appuie sur le framework de test unitaire C#. Après la génération du projet, vous pouvez exécuter tous les cas de test via **Explorateur de tests > Sélection** ou cliquez sur le fichier .cs et choisissez **Exécuter les tests**.

## <a name="test-c-udos"></a>Tester les opérateurs définis par l’utilisateur (UDO) C#

### <a name="create-test-cases-for-c-udos"></a>Créer des cas de test pour les UDO C#

Vous pouvez utiliser le framework de test unitaire C# pour tester vos UDO (opérateur défini par l’utilisateur) C#. Lorsque vous testez les UDO, vous devez préparer l’objet **IRowset** correspondant en tant qu’entrées.

Il existe deux façons de créer IRowset :

1.  Charger des données à partir d’un fichier pour créer IRowset

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

2.  Utiliser les données de la collecte de données pour créer IRowset

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Vérifier les résultats du test

Après l’appel des fonctions UDO, vous pouvez vérifier le résultat par l’intermédiaire du schéma et la vérification de la valeur Rowset à l’aide des fonctions C# Assert. L’exemple de code peut être dans Exemple de projet de test unitaire U-SQL C# UDO via **Fichier > Nouveau > Projet** dans Visual Studio.

### <a name="execute-test-cases-in-visual-studio"></a>Exécuter des cas de test dans Visual Studio

Après la génération du projet de test, vous pouvez exécuter tous les cas de test via **Explorateur de tests > Sélection** ou cliquez sur le fichier .cs et choisissez **Exécuter les tests**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Exécuter des cas de test dans Visual Studio Team Service

Le **projet de test du script U-SQL** et le **projet de test C# UDO** héritent tous deux du projet de test unitaire C#. La [tâche Visual Studio Test](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) dans Visual Studio Team Service peut exécuter ces cas de test. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Exécuter des cas de test U-SQL dans Visual Studio Team Service

Pour le test U-SQL, veillez à charger `CPPSDK` sur votre ordinateur de build et à transmettre le chemin `CPPSDK` à USqlScriptTestRunner (cppSdkFolderFullPath : @"").

**Qu’est-ce que CPPSDK ?**

CPPSDK est un package qui inclut Microsoft Visual C++ 14 et Windows SDK 10.0.10240.0, qui est l’environnement requis par le runtime U-SQL. Vous pouvez obtenir ce package sous le dossier d’installation d’Azure Data Lake Tools pour Visual Studio :

- Pour Visual Studio 2015, il se trouve sous `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Pour Visual Studio 2017, il se trouve sous `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Guide pratique pour préparer CPPSDK dans l’agent de build de Visual Studio Team Service**

La méthode courante pour préparer cette dépendance CPPSDK dans Visual Studio Team Service est la suivante :

1.  Compressez le dossier qui inclut les bibliothèques CPPSDK.
2.  Archivez le fichier zip dans votre système de contrôle de code source. (Le fichier zip permet de s’assurer que vous archivez toutes les bibliothèques sous le dossier CPPSDK ou certains fichiers seront ignorés par « .gitignore ».)
3.  Décompressez le fichier zip dans le pipeline de build.
4.  Pointez `USqlScriptTestRunner` sur ce dossier décompressé sur l’ordinateur de build.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Exécuter des cas de test C# UDO dans Visual Studio Team Service

Pour le test C# UDO, veillez à référencer les assemblys ci-dessous qui sont nécessaires pour les opérateurs définis par l’utilisateur. Si vous y faites référence via [le package Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), assurez-vous d’ajouter une tâche de restauration NuGet dans votre pipeline de build.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Étapes suivantes

- [Comment configurer un pipeline CI/CD pour Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Exécuter un script U-SQL sur votre ordinateur local](data-lake-analytics-data-lake-tools-local-run.md)
- [Utiliser un projet de base de données U-SQL afin de développer une base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

