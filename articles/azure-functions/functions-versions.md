---
title: Vue d’ensemble des versions du runtime Azure Functions
description: Azure Functions prend en charge plusieurs versions du runtime. Découvrez les différences entre elles et comment choisir celle qui vous convient.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: a601ea42549abad84d6cab5c429cf94147776436
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978622"
---
# <a name="azure-functions-runtime-versions-overview"></a>Vue d’ensemble des versions du runtime Azure Functions

 Il existe deux versions principales du runtime Azure Functions : 1.x et 2.x. La version actuelle qui accueille les nouvelles fonctionnalités et où sont apportées les améliorations est la version 2.x, bien que les deux versions soient prises en charge pour les scénarios de production.  Les informations ci-après les comparent sur certains points et expliquent comment créer chaque version et effectuer une mise à niveau depuis la version 1.x vers la version 2.x.

> [!NOTE] 
> Cet article fait référence au service cloud Azure Functions. Pour plus d’informations sur le produit en préversion qui vous permet d’exécuter Azure Functions localement, consultez [Vue d’ensemble du runtime d’Azure Functions](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>Création d’applications 1.x

Les applications créées dans le portail Azure sont définies sur 2.x par défaut, car il s’agit de la version la plus récente et c’est celle vers laquelle sont tournés les investissements en nouvelles fonctionnalités.  Toutefois, vous pouvez toujours créer des applications v1.x en effectuant les étapes suivantes.

1. Créez une fonction Azure à partir du portail Azure.
1. Ouvrez l’application créée, puis, alors qu’elle est vide, ouvrez **Paramètres de la fonction**.
1. Changer la version de ~2 à ~1.  *Ce bouton bascule est désactivé si votre application contient des fonctions*.
1. Cliquez sur Enregistrer et redémarrez l’application.  Tous les modèles doivent désormais être créés et exécutés dans la version 1.x.

## <a name="cross-platform-development"></a>Développement multiplateforme

Runtime 1.x prend en charge uniquement le développement et l’hébergement dans le portail ou sur Windows. Runtime 2.x s’exécute sur .NET Core 2, ce qui signifie qu’il peut s’exécuter sur toutes les plateformes prises en charge par .NET Core, y compris macOS et Linux. Cela permet un développement multiplateforme et des scénarios d’hébergement.

## <a name="languages"></a>Langues

Runtime 2.x utilise un nouveau modèle d’extensibilité de langage. En outre, afin que les outils et les performances soient améliorés, les fonctions ne sont disponibles que dans un seul langage dans chaque application en version 2.x. Les langages pris en charge dans la version 2.x sont C#, F#, JavaScript et Java. Les langages expérimentaux d’Azure Functions 1.x n’ont pas été mis à jour pour utiliser le nouveau modèle et ne sont donc pas pris en charge dans 2.x. Le tableau suivant montre les langages de programmation qui sont pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Pour en savoir plus, consultez [Langages pris en charge](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Migration depuis la version 1.x vers la version 2.x

Vous pouvez souhaiter migrer une application existante écrite en 1.x vers 2.x.  La plupart des points à prendre en compte quand vous passez d’une version à l’autre sont liés aux modifications du runtime de langage répertoriées ci-dessus (par exemple, passage en C# de .NET Framework 4.7 à .NET Core 2).  Vous devrez vous assurer que vos code et bibliothèques sont compatibles avec les runtimes de langage utilisés.  Veillez également à noter toutes les modifications soulignées ci-après qui affectent les déclencheurs, les liaisons et les fonctionnalités.

### <a name="changes-in-triggers-and-bindings"></a>Modifications dans les déclencheurs et les liaisons

Bien que la plupart des propriétés et configurations des déclencheurs et liaisons restent les mêmes d’une version à l’autre, dans la version 2.x, vous devez installer tout déclencheur ou liaison à l’application. Les seules exceptions sont les déclencheurs HTTP et du minuteur.  Consultez [Inscrire et installer des extensions de liaison](./functions-triggers-bindings.md#register-binding-extensions).  Notez que le `function.json` ou les attributs de la fonction peuvent faire l’objet de modifications d’une version à l’autre (par exemple, la propriété `connection` CosmosDB est désormais `ConnectionStringSetting`).  Consultez le [tableau des liaisons existantes](#bindings) pour obtenir des liens vers de la documentation sur chaque liaison.

### <a name="changes-in-features-available"></a>Modifications dans les fonctionnalités disponibles

Outre les modifications dans les langages et les liaisons, il existe certaines fonctionnalités qui ont été supprimées, mises à jour ou remplacées d’une version à l’autre.  Voici certains des principaux points à prendre en considération quand vous commencez à utiliser la version 2.x après avoir utilisé la version 1.x.  Dans la version 2.x, les modifications suivantes ont été apportées :

* Les clés permettant d’appeler une fonction sont toujours stockées dans le stockage d’objets blob chiffré. Dans la version 1.x, par défaut, elles se trouvaient dans le stockage de fichiers et pouvaient être déplacées dans un objet blob si certaines fonctionnalités telles que les emplacements étaient activées.  Si vous mettez à niveau une application 1.x vers 2.x et que des secrets se trouvent dans le stockage de fichiers, elles sont réinitialisées.
* Pour que les performances soient améliorées, les déclencheurs de type « webhook » sont supprimés et remplacés par des déclencheurs « HTTP ».
* La configuration de l’hôte (`host.json`) doit être vide ou contenir `version` `2.0` pour l’une des propriétés.
* Pour que la supervision et l’observabilité soient améliorées, le tableau de bord Azure WebJobs (`AzureWebJobsDashboard`) est remplacé par [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* Les paramètres d’application (`local.settings.json`) nécessitent une valeur pour la propriété `FUNCTIONS_WORKER_RUNTIME` qui correspond au langage de l’application `dotnet | node | java | python`.
    * Pour que les temps de démarrage et l’empreinte soient améliorés, les applications sont limitées à un seul langage. Vous pouvez publier plusieurs applications pour avoir des fonctions dans différents langages pour une même solution.
* Le délai d’expiration par défaut pour les fonctions dans un plan de service d’application est de 30 minutes.  Vous pouvez néanmoins le définir manuellement sur un délai illimité.
* [En raison des limitations propres au .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), les scripts `.fsx` pour les fonctions F# ont été supprimés. Les fonctions F# compilées sont toujours prises en charge.
* Le format des déclencheurs basés sur un webhook (par exemple, Event Grid) est désormais `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="upgrading-a-locally-developed-application"></a>Mise à niveau d’une application développée localement

Si votre application 1.x a été développée localement, vous pouvez apporter des modifications à celle-ci ou au projet pour le rendre compatible avec la version 2.  Il est recommandé de créer une application et de porter le code vers celle-ci.  Bien que des modifications puissent être apportées à une application existante pour effectuer une mise à niveau sur place, il existe un certain nombre d’autres améliorations entre les versions 1 et 2 dont le code hérité ne tire probablement pas parti (par exemple en C#, le passage de `TraceWriter` à `ILogger`).  

L’approche recommandée consiste à partir d’un des modèles v2 et à déplacer le code vers une nouvelle application ou un nouveau projet.

#### <a name="visual-studio-runtime-versions"></a>Versions du runtime Visual Studio

Dans Visual Studio, vous sélectionnez la version du runtime quand vous créez un projet.  Visual Studio peut supporter les deux versions principales et utiliser de manière dynamique celle qui convient au projet.  Ces paramètres sont dérivés du fichier `.csproj`.  Pour les applications 1.x, le projet a les propriétés

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

Dans la version 2, les propriétés du projet sont

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Le fait de cliquer sur Déboguer ou Publier permet de définir correctement la bonne version pour les paramètres du projet.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code et Azure Functions Core Tools

D’autres outils locaux s’appuient sur Azure Functions Core Tools.  Ces outils sont installés sur la machine ; en général, une seule version est installée sur une machine de développement à la fois.  Consultez les [instructions indiquant comment installer des versions spécifiques de Core Tools](./functions-run-local.md).

Pour VS Code, vous devrez peut-être également mettre à jour le paramètre utilisateur pour le `azureFunctions.projectRuntime` afin qu’il corresponde à la version des outils installés.  Cette opération met également à jour les modèles et les langages signalés pendant la création d’applications.

### <a name="changing-version-of-apps-in-azure"></a>Changement de la version des applications dans Azure

Les versions des applications publiées sont définies par le biais du paramètre d’application `FUNCTIONS_RUNTIME_VERSION`.  Ce dernier est défini sur `~2` pour les applications v2, et sur `~1` pour les applications v1.  Il est fortement déconseillé de changer la version du runtime d’une application où sont publiées des fonctions existantes sans changer également le code de ces fonctions.  L’approche recommandée consiste à créer une application de fonction et à la définir sur la version appropriée, à tester les modifications, puis à désactiver ou supprimer l’application précédente.

## <a name="bindings"></a>Liaisons 

Runtime 2.x utilise un nouveau [modèle d’extensibilité de liaison](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) qui présente les avantages suivants :

* Prise en charge pour les extensions de liaison de tiers.
* Découplage du runtime et des liaisons. Cela permet de gérer les versions des extensions de liaison et de les publier de façon indépendante. Vous pouvez, par exemple, choisir de mettre à niveau vers une version d’une extension qui s’appuie sur une version plus récente d’un kit de développement logiciel (SDK) sous-jacent.
* Un environnement d’exécution plus léger, où seules les liaisons en cours d’utilisation sont connues et chargées par le runtime.

Toutes les liaisons Azure Functions intégrées ont adopté ce modèle et ne sont plus incluses par défaut, à l’exception du déclencheur du minuteur et du déclencheur HTTP. Ces extensions sont installées automatiquement lorsque vous créez des fonctions par le biais d’outils tels que Visual Studio ou par le biais du portail.

Le tableau suivant indique les liaisons prises en charge dans chaque version du runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Coder et tester Azure Functions localement](functions-run-local.md)
* [Procédure pour cibler des versions du runtime Azure Functions](set-runtime-version.md)
* [Notes de publication](https://github.com/Azure/azure-functions-host/releases)
