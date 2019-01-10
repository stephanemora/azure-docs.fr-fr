---
title: Guide de développement Azure Functions | Microsoft Docs
description: Découvrez les concepts et techniques Azure Functions dont vous avez besoin pour développer des fonctions dans Azure, et ce, pour tous les langages de programmation et toutes les liaisons.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: guide de développement, azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 42635852bb5c6e7b388d4dc58b9d5bfaa6212438
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725847"
---
# <a name="azure-functions-developers-guide"></a>Guide de développement Azure Functions
Dans Azure Functions, des fonctions spécifiques partagent quelques concepts techniques et composants de base, quels que soient le langage et la liaison que vous utilisez. Avant de passer à l'apprentissage des détails propres à un langage ou une liaison donnés, veillez à lire cette présentation qui s'applique à l’ensemble d’entre eux.

Cet article suppose que vous avez déjà lu la [vue d'ensemble d'Azure Functions](functions-overview.md) et que vous connaissez les [concepts du Kit de développement logiciel (SDK) WebJobs, notamment les déclencheurs, les liaisons et le runtime JobHost](https://github.com/Azure/azure-webjobs-sdk/wiki). Azure Functions est fondé sur le SDK WebJobs. 

## <a name="function-code"></a>Code de fonction
La *fonction* est le principal concept d'Azure Functions. Vous écrivez du code pour une fonction dans le langage de votre choix et enregistrez le code et les fichiers de configuration dans le même dossier. La configuration est nommée `function.json`, et contient les données de configuration JSON. Plusieurs langages sont pris en charge, et chacun d’entre eux offre une expérience légèrement différente, optimisée pour fonctionner idéalement pour ce langage. 

Le fichier function.json définit les liaisons de fonction et d’autres paramètres de configuration. Le runtime utilise ce fichier pour déterminer les événements à surveiller et comment passer des données et renvoyer des données à partir de l’exécution de la fonction. Voici un exemple de fichier function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Définissez la propriété `disabled` sur `true` pour empêcher l’exécution de la fonction.

La propriété `bindings` vous permet de configurer les liaisons et les déclencheurs. Chaque liaison partage quelques paramètres communs et des paramètres propres à un type de liaison donné. Chaque liaison requiert les paramètres suivants :

| Propriété | Valeurs/types | Commentaires |
| --- | --- | --- |
| `type` |chaîne |Type de liaison. Par exemple, `queueTrigger`. |
| `direction` |'in', 'out' |Indique si la liaison sert à recevoir des données dans la fonction ou à envoyer des données à partir de la fonction. |
| `name` |chaîne |Le nom utilisé pour les données liées dans la fonction. Pour C#, il s’agit d’un nom d'argument ; pour JavaScript, il s’agit de la clé dans une liste de clés/valeurs. |

## <a name="function-app"></a>Conteneur de fonctions
Une application de fonction fournit un contexte d’exécution dans Azure dans lequel vos fonctions s’exécutent. Un conteneur de fonctions est constitué d’une ou de plusieurs des fonctions individuelles qui sont gérées ensemble par Azure App Service. Toutes les fonctions d’un conteneur de fonctions partagent le même plan de tarification, le même déploiement continu et la même version du runtime. Considérez un conteneur de fonctions comme un moyen d’organiser et de gérer collectivement vos fonctions. 

> [!NOTE]
> À partir de la [version 2.x](functions-versions.md) du runtime Azure Functions, toutes les fonctions dans une application de fonction doivent être créées dans le même langage.

## <a name="runtime"></a>Runtime
Le runtime Azure Functions, également appelé hôte de script, est l’hôte sous-jacent qui écoute les événements, collecte et envoie les données et, enfin, exécute votre code. Ce même hôte est utilisé par le SDK WebJobs.

Il existe également un hôte web qui gère les requêtes de déclencheur HTTP pour le runtime. Le fait d’avoir deux hôtes vous permet d’isoler le runtime du trafic frontal géré par l’hôte web.

## <a name="folder-structure"></a>Structure de dossiers
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Quand vous configurez un projet pour déployer des fonctions sur une application de fonction dans Azure, vous pouvez traiter cette structure de dossiers comme le code de votre site. Nous vous recommandons d’utiliser un [package de déploiement](deployment-zip-push.md) pour déployer votre projet sur votre application de fonction dans Azure. Vous pouvez également utiliser des outils existants tels que [l’intégration et le déploiement continus](functions-continuous-deployment.md) et Azure DevOps.

> [!NOTE]
> Veillez à déployer votre fichier `host.json` et vos dossiers de fonction directement dans le dossier `wwwroot`. N’incluez pas le dossier `wwwroot` dans vos déploiements. Sinon, vous vous retrouverez avec `wwwroot\wwwroot` dossiers.

## <a id="fileupdate"></a> Comment mettre à jour les fichiers du conteneur de fonctions
L’éditeur de fonctions intégré au portail Azure vous permet de mettre à jour le fichier *function.json* et le fichier de code pour une fonction. Pour télécharger ou mettre à jour d’autres fichiers comme *package.json* ou *project.json* ou les dépendances, vous devez utiliser d’autres méthodes de déploiement.

Les conteneurs de fonctions sont créés sur App Service, de sorte que toutes les [options de déploiement disponibles sur les applications web standard](../app-service/deploy-local-git.md) le sont également sur les conteneurs de fonctions. Voici des méthodes que vous pouvez utiliser pour télécharger ou mettre à jour les fichiers du conteneur de fonctions. 

#### <a name="use-local-tools-and-publishing"></a>Utiliser des outils locaux pour la publication
Vous pouvez créer et publier des applications de fonction à l’aide de différents outils, notamment [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) et [Azure Functions Core Tools](./functions-develop-local.md). Pour plus d’informations, consultez [Coder et tester Azure Functions localement](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="continuous-deployment"></a>Déploiement continu
Suivez les instructions de la rubrique [Déploiement continu pour Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Exécution en parallèle
Lorsque plusieurs événements de déclenchement se produisent plus rapidement qu'un runtime de fonction monothread ne peut les traiter, le runtime peut appeler la fonction plusieurs fois en parallèle.  Si un conteneur de fonctions utilise le [plan d’hébergement de consommation](functions-scale.md#how-the-consumption-plan-works), il peut monter automatiquement augmenter la taille des instances.  Que l’application s’exécute sur le plan d’hébergement de consommation ou sur un [plan d’hébergement App Service](../app-service/overview-hosting-plans.md) standard, chaque instance de l’application de fonction peut traiter en parallèle des appels de fonction simultanés en utilisant plusieurs threads.  Le nombre maximal d’appels de fonction simultanés dans chaque instance de chaque application de fonction varie en fonction du type de déclencheur utilisé, ainsi que des ressources utilisées par d’autres fonctions au sein de l’application de fonction.

## <a name="functions-runtime-versioning"></a>Contrôle de version du runtime Functions

Vous pouvez configurer la version du runtime Functions en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION`. Par exemple, la valeur « ~2 » indique que votre application de fonction utilise 2.x comme version principale. Les applications Function sont mises à niveau pour chaque nouvelle version secondaire lorsqu’elles sont disponibles. Pour obtenir plus d’informations, notamment sur la façon d’afficher la version exacte de votre application de fonction, consultez [Guide pratique pour cibler des versions du runtime Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Référentiels
Le code pour Azure Fonctions est open source et stocké dans des dépôts GitHub :

* [Runtime Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portail Azure Functions](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modèles Azure Functions](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensions du kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Liaisons
Voici un tableau de toutes les liaisons prises en charge.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Vous rencontrez des problèmes avec des erreurs de liaisons ? Consultez la documentation [Codes d’erreur de liaison d’Azure Functions](functions-bindings-error-pages.md).

## <a name="reporting-issues"></a>Problèmes liés aux rapports
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Informations de référence pour les développeurs F# sur Azure Functions](functions-reference-fsharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Azure Functions: The Journey](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) (Découverte d’Azure Functions) sur le blog de l’équipe Azure App Service. Un historique sur le développement d’Azure Functions.

