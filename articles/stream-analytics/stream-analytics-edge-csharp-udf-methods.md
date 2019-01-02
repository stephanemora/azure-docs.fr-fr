---
title: Développer des fonctions .NET Standard pour des travaux de périphérie Azure Stream Analytics (préversion)
description: Découvrez comment écrire des fonctions C# définies par l’utilisateur pour des travaux de périphérie Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5df4c9dfe18b02ade3a37717da9c68acbfcf1853
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106598"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Développer des fonctions définies par l’utilisateur .NET Standard pour des travaux de périphérie Azure Stream Analytics (préversion)

Azure Stream Analytics offre un langage de requête de type SQL pour effectuer des transformations et des calculs sur des flux de données d’événement. De nombreuses fonctions sont intégrées, mais certains scénarios complexes nécessitent davantage de flexibilité. Avec les fonctions .NET Standard définies par l’utilisateur, vous pouvez appeler vos propres fonctions écrites dans n’importe quel langage .NET Standard (C#, F#, etc.) pour étendre le langage de requête Stream Analytics. Les fonctions définies par l’utilisateur vous permettent d’effectuer des calculs mathématiques complexes, d’importer des modèles Machine Learning personnalisés à l’aide de ML.NET et d’utiliser une logique personnalisée d’imputation pour les données manquantes. La fonctionnalité de fonction définie par l’utilisateur pour les travaux de périphérie Stream Analytics étant disponible en préversion, elle ne doit pas être utilisée dans les charges de travail de production.

## <a name="overview"></a>Vue d’ensemble
Grâce aux outils Visual Studio pour Azure Stream Analytics, vous pouvez facilement écrire des fonctions définies par l’utilisateur, tester vos travaux localement (même en mode hors connexion) et publier votre travail Stream Analytics dans Azure. Une fois publié sur Azure, vous pouvez déployer votre travail sur des appareils IoT à l’aide d’IoT Hub.

Il existe trois façons d’implémenter des fonctions définies par l’utilisateur :

* Fichiers code-behind dans un projet ASA
* Fonction définie par l’utilisateur à partir d’un projet local
* Package existant à partir d’un compte de stockage Azure

## <a name="package-path"></a>Chemin du package

Le format d’un package de fonction définie par l’utilisateur a le chemin `/UserCustomCode/CLR/*`. Les bibliothèques de liens dynamiques (DLL) et les ressources sont copiées sous le dossier `/UserCustomCode/CLR/*`, qui permet d’isoler les DLL utilisateur du système et les DLL Azure Stream Analytics. Ce chemin du package est utilisé pour toutes les fonctions, quelle que soit la méthode d’utilisation choisie.

## <a name="supported-types-and-mapping"></a>Types pris en charge et mappage

|**Type de fonction définie par l’utilisateur (C#)**  |**Type Azure Stream Analytics**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|chaîne  |  nvarchar(max)   |
|dateTime  |  dateTime   |
|struct  |  IRecord   |
|objet  |  IRecord   |
|Array<object>  |  IArray   |
|dictionary<string, object>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
Vous pouvez écrire des fonctions définies par l’utilisateur dans le fichier code-behind **Script.asql**. Les outils Visual Studio compilent automatiquement le fichier code-behind dans un fichier d’assembly. Les assemblys sont packagés dans un fichier zip et chargés dans votre compte de stockage quand vous envoyez votre travail à Azure. Vous pouvez apprendre à écrire une fonction C# définie par l’utilisateur à l’aide de d’un fichier code-behind en suivant le tutoriel [Fonction C# définie par l’utilisateur pour les travaux de périphérie Stream Analytics](stream-analytics-edge-csharp-udf.md). 

## <a name="local-project"></a>Projet local
Les fonctions définies par l’utilisateur peuvent être écrites dans un assembly référencé plus tard dans une requête Azure Stream Analytics. Il s’agit de l’option recommandée pour les fonctions complexes qui nécessitent toute la puissance d’un langage .NET Standard au-delà de son langage d’expression, comme la logique procédurale ou la récursivité. Les fonctions définies par l’utilisateur à partir d’un projet local peuvent également être utilisées si vous avez besoin de partager la logique de la fonction entre plusieurs requêtes Azure Stream Analytics. L’ajout de fonctions définies par l’utilisateur à votre projet local vous donne la possibilité de déboguer et tester vos fonctions localement dans Visual Studio.

Pour référencer un projet local :

1. Créez une bibliothèque de classes dans votre solution.
2. Écrivez le code dans votre classe. Toutes les classes doivent être définies comme étant *publiques* et tous les objets comme étant *publics statiques*. 
3. Générez votre projet. Les outils créent un fichier zip contenant tous les artefacts du dossier bin, puis chargent ce fichier zip dans le compte de stockage. Pour les références externes, utilisez la référence d’assembly au lieu du package NuGet.
4. Référencez la nouvelle classe dans votre projet Azure Stream Analytics.
5. Ajoutez une nouvelle fonction dans votre projet Azure Stream Analytics.
6. Configurez le chemin d’assembly dans le fichier de configuration du travail, `JobConfig.json`. Définissez le chemin d’assembly sur **Référence de projet locale ou code-behind**.
7. Regénérez le projet de fonction et le projet Azure Stream Analytics.  

### <a name="example"></a>Exemples

Dans cet exemple, **UDFTest** est un projet de bibliothèque de classes C# et **ASAEdgeUDFDemo** est le projet de périphérie Azure Stream Analytics, qui référence **UDFTest**.

![Projet IoT Edge Azure Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Générez votre projet C# pour pouvoir ajouter une référence à votre fonction C# définie par l’utilisateur à partir de la requête Azure Stream Analytics.
    
   ![Générer un projet Iot Edge Azure Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Ajoutez la référence au projet C# dans le projet de périphérie ASA. Cliquez avec le bouton droit sur le nœud Références et choisissez Ajouter une référence.

   ![Ajouter une référence à un projet C# dans Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Choisissez le nom du projet C# dans la liste. 
    
   ![Choisissez le nom de votre projet C# dans la liste de référence](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Vous devez voir **UDFTest** listé sous **Références** dans **l’Explorateur de solutions**.

   ![Afficher la référence de la fonction définie par l’utilisateur dans l’Explorateur de solutions](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Cliquez avec le bouton droit sur le dossier **Fonctions** et choisissez **Nouvel élément**.

   ![Ajouter un nouvel élément au dossier Fonctions dans la solution de périphérie Azure Stream Analytics](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Ajoutez une fonction C# **SquareFunction.json** à votre projet Azure Stream Analytics.

   ![Sélectionner la fonction C Sharp parmi les éléments de périphérie Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Double-cliquez sur la fonction dans **l’Explorateur de solutions** pour ouvrir la boîte de dialogue de configuration.

   ![Configuration de la fonction C Sharp dans Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Dans la configuration de fonction C#, choisissez **Charger à partir d’une référence de projet ASA** ainsi que les noms des assemblys, classes et méthodes associés dans la liste déroulante. Pour référencer les méthodes, les types et les fonctions et dans la requête de périphérie Stream Analytics, les classes doivent être définies comme étant *publiques* et les objets comme étant *publics statiques*.

   ![Configuration de la fonction C Sharp dans Stream Analytics](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Packages existants

Vous pouvez créer des fonctions définies par l’utilisateur .NET Standard dans l’IDE de votre choix et les appeler à partir de votre requête Azure Stream Analytics. Commencez par compiler votre code et packager toutes les DLL. Le format du package a le chemin `/UserCustomCode/CLR/*`. Ensuite, chargez `UserCustomCode.zip` à la racine du conteneur dans votre compte de stockage Azure.

Une fois les packages zip d’assemblys chargés dans votre compte de stockage Azure, vous pouvez utiliser les fonctions dans des requêtes Azure Stream Analytics. Il vous suffit d’inclure les informations de stockage dans la configuration du travail de périphérie Stream Analytics. Vous ne pouvez pas tester la fonction localement à l’aide de cette option, car les outils Visual Studio ne téléchargent pas votre package. Le chemin du package est analysé directement par le service. 

Pour configurer le chemin d’assembly dans le fichier de configuration du travail, `JobConfig.json` :

Développez la section **Configuration du code défini par l’utilisateur** et remplissez la configuration avec les valeurs suggérées suivantes :

 |**Paramètre**  |**Valeur suggérée**  |
 |---------|---------|
 |Source de l’assembly  | Packages d’assembly existants du cloud    |
 |Ressource  |  Choisissez des données du compte actuel   |
 |Abonnement  |  Choisissez votre abonnement.   |
 |Compte de stockage  |  Choisissez votre compte de stockage.   |
 |Conteneur  |  Choisissez le conteneur que vous avez créé dans votre compte de stockage.   |

![Configuration du travail de périphérie Azure Stream Analytics dans Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>Limites
La préversion des fonctions définies par l’utilisateur a les limitations suivantes :

* Les langages .NET Standard peuvent uniquement être utilisés pour Azure Stream Analytique sur IoT Edge. Pour les travaux dans le cloud, vous pouvez écrire des fonctions JavaScript définies par l’utilisateur. Pour en savoir plus, consultez le tutoriel [Fonctions JavaScript définies par l’utilisateur dans Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md).

* Les fonctions définies par l’utilisateur .NET Standard peuvent uniquement être créées dans Visual Studio et publiées sur Azure. Les versions en lecture seule des fonctions définies par l’utilisateur .NET Standard peuvent être consultées sous **Fonctions** dans le portail Azure. La création de fonctions .NET Standard n’est pas prise en charge dans le portail Azure.

* L’éditeur de requêtes du portail Azure affiche une erreur quand vous utilisez des fonctions définies par l’utilisateur .NET Standard dans le portail. 

* Comme le code personnalisé partage le contexte du moteur Azure Stream Analytics, il ne peut pas référencer un élément qui a un espace de noms/nom_dll en conflit avec le code Azure Stream Analytics. Par exemple, vous ne pouvez pas référencer *Newtonsoft Json*.

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Écrire une fonction C# définie par l’utilisateur pour un travail de périphérie Azure Stream Analytics (préversion)](stream-analytics-edge-csharp-udf.md)
* [Tutoriel : Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Utiliser Visual Studio pour afficher les tâches Azure Stream Analytics](stream-analytics-vs-tools.md)
