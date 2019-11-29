---
title: Vue d’ensemble des versions du runtime Azure Functions
description: Azure Functions prend en charge plusieurs versions du runtime. Découvrez les différences entre elles et comment choisir celle qui vous convient.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226549"
---
# <a name="azure-functions-runtime-versions-overview"></a>Vue d’ensemble des versions du runtime Azure Functions

Les versions principales du runtime Azure Functions sont liées à la version de .NET sur laquelle le runtime est basé. Le tableau suivant renseigne sur la version actuelle du runtime, le niveau de version et la version .NET associée. 

| Version du runtime | Niveau de version<sup>1</sup> | Version de .NET | 
| --------------- | ------------- | ------------ |
| 3.x  | preview | .NET Core 3.x | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | Disponibilité générale<sup>2</sup> | .NET Framework 4.6<sup>3</sup> |

<sup>1</sup>Les versions en disponibilité générale sont prises en charge pour les scénarios de production.   
<sup>2</sup>Version 1.x est en mode de maintenance. Les améliorations sont fournies uniquement dans les versions ultérieures.   
<sup>3</sup>Prend uniquement en charge le développement dans le portail Azure, ou localement sur des ordinateurs Windows.

>[!NOTE]  
> La version 3.x du runtime Functions est en préversion et n’est pas prise en charge pour les environnements de production. Pour plus d’informations sur la possibilité de tester la version 3.x, consultez [cette annonce](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

Cet article explique en détail certaines différences existant entre les versions, comment vous pouvez créer chaque version et comment les modifier.

## <a name="languages"></a>Languages

À compter de la version 2.x, le runtime utilise un modèle d’extensibilité de langage, et toutes les fonctions d’une application de fonction doivent partager le même langage. Le langage des fonctions d’une application de fonction est choisi au moment de la création de l’application, et est conservé dans le paramètre [FUNCTIONS\_WORKER\_RUNTIME](functions-app-settings.md#functions_worker_runtime). 

Les langages expérimentaux d’Azure Functions 1.x ne pouvant pas utiliser le nouveau modèle, ils ne sont pas pris en charge dans 2.x. Le tableau suivant montre les langages de programmation actuellement pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Pour en savoir plus, consultez [Langages pris en charge](supported-languages.md).

## <a name="creating-1x-apps"></a>Exécuter sur une version spécifique

Par défaut, les applications de fonction créées dans le portail Azure et par l’interface Azure CLI sont configurées pour la version 2.x. Dans la mesure du possible, vous devez utiliser cette version du runtime. Si vous le souhaitez, vous pouvez continuer à exécuter une application de fonction sur la version 1.x du runtime. Pour modifier la version du runtime, vous intervenez uniquement après avoir créé votre application de fonction, mais avant d’avoir ajouté des fonctions. Pour savoir comment épingler la version du runtime à la version 1.x, consultez [Afficher et mettre à jour la version actuelle du runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

Vous pouvez également effectuer la mise à niveau vers la version 3.x du runtime, qui est en préversion. Faites-le si vous devez, le cas échéant, exécuter vos fonctions sur .NET Core 3.x. Pour savoir comment mettre à niveau vers la version 3.x, consultez [Afficher et mettre à jour la version actuelle du runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migration de la version 1.x vers les versions ultérieures

Vous pouvez choisir de migrer une application existante écrite pour utiliser la version 1.x du runtime vers une application qui utilise la version 2.x. La plupart des modifications que vous devez apporter sont liées au runtime de langage, par exemple des modifications de l’API C# entre .NET Framework 4.7 et .NET Core 2. Vous devez également vous assurer que le code et les bibliothèques sont compatibles avec le runtime de langage choisi. Enfin, veillez à noter toutes les modifications soulignées ci-après qui affectent les déclencheurs, liaisons et fonctionnalités. Pour une migration plus performante, vous devez créer une nouvelle application de fonction pour la version 2.x et déplacer le code de fonction existant de la version 1.x vers la nouvelle application.  

### <a name="changes-in-triggers-and-bindings"></a>Modifications dans les déclencheurs et les liaisons

Avec la version 2.x, vous devez installer dans votre application les extensions des déclencheurs et liaisons spécifiques utilisés par les fonctions. La seule exception concerne les déclencheurs HTTP et de la minuterie, qui ne nécessitent aucune extension.  Pour plus d’informations, voir [Inscrire et installer des extensions de liaison](./functions-bindings-register.md).

Il convient également de noter quelques modifications dans le paramètre `function.json` ou les attributs de la fonction entre les versions. Par exemple, la propriété `path` d’Event Hub est désormais `eventHubName`. Consultez le [tableau des liaisons existantes](#bindings). Il contient des liens vers de la documentation sur chaque liaison.

### <a name="changes-in-features-and-functionality"></a>Changements apportés aux fonctionnalités

Quelques fonctionnalités ont également été supprimées, mises à jour ou remplacées dans la nouvelle version. Cette section détaille les modifications de la version 2.x par rapport à la version 1.x.

Les modifications suivantes ont été apportées à la version 2.x :

* Les clés permettant d’appeler les points de terminaison HTTP sont toujours stockées et chiffrées dans le Stockage Blob Azure. Dans la version 1.x, les clés étaient stockées par défaut dans le Stockage Fichier Azure. Au moment de mettre à niveau une application de la version 1.x à la version 2.x, les secrets existants qui se trouvent dans le Stockage Fichier sont réinitialisés.

* La version 2.x du runtime n’intègre aucune prise en charge des fournisseurs de webhooks. Cette modification a été apportée pour améliorer les performances. Vous pouvez continuer à utiliser des déclencheurs HTTP comme points de terminaison des webhooks.

* Le fichier de configuration d’hôte (host.json) doit être vide ou contenir la chaîne `"version": "2.0"`.

* Pour améliorer la surveillance, le tableau de bord WebJobs se trouvant dans le portail, qui a utilisé le paramètre [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard), est remplacé par Azure Application Insights, qui utilise le paramètre [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey). Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

* Toutes les fonctions d’une application de fonction doivent partager le même langage. Lorsque vous créez une application de fonction, vous devez choisir une pile d’exécution pour l’application. La pile d’exécution est spécifiée par la valeur [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) dans les paramètres de l’application. Cette exigence a été ajoutée pour améliorer l’empreinte mémoire et le temps de démarrage. Lorsque vous développez en local, vous devez également inclure ce paramètre dans le [fichier local.settings.json](functions-run-local.md#local-settings-file).

* Le délai d’expiration par défaut pour les fonctions dans un plan App Service est de 30 minutes. Vous pouvez modifier ce délai d’expiration manuellement et l’indiquer à nouveau comme étant illimité en utilisant le paramètre [functionTimeout](functions-host-json.md#functiontimeout) dans host.json.

* Les limitations d’accès concurrentiel HTTP sont implémentées par défaut pour les fonctions de plan de consommation, avec une valeur par défaut de 100 demandes simultanées par instance. Vous pouvez configurer ces limitations dans le paramètre [`maxConcurrentRequests`](functions-host-json.md#http) du fichier host.json.

* En raison des [limitations de .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), la prise en charge des fonctions de script F# (.fsx) a été supprimée. Les fonctions F# compilées (.fs) restent prises en charge.

* Le format d’URL des webhooks de déclencheur Event Grid a été remplacé par `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migration d’une application développée en local

Vous possédez peut-être des projets d’application de fonction développés en local à l’aide de la version 1.x du runtime. Pour les mettre à niveau vers la version 2.x, vous devez créer un projet d’application de fonction développé en local d’après la version 2.x, puis déplacer le code existant dans cette nouvelle application. Vous pouvez mettre à jour manuellement le projet et le code existants. Il s’agit d’une sorte de mise à niveau « sur place ». Toutefois, vous allez encore devoir apporter quelques améliorations entre la version 1.x et la version 2.x. Par exemple, en C#, l’objet de débogage a été modifié. Il s’agissait de `TraceWriter` et maintenant il s’agit de `ILogger`. Lorsque vous créez un nouveau projet en version 2.x, vous commencez par des fonctions de mise à jour en vous appuyant sur les modèles de version 2.x les plus récents.

#### <a name="visual-studio-runtime-versions"></a>Versions du runtime Visual Studio

Dans Visual Studio, vous sélectionnez la version du runtime au moment de créer un projet. Azure Functions Tools pour Visual Studio prend en charge les deux versions majeures du runtime. La version appropriée est utilisée au moment du débogage et de la publication, en fonction des paramètres de projet. Les paramètres de version sont définis dans le fichier `.csproj`, dans les propriétés suivantes :

##### <a name="version-1x"></a>Version 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Version 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Lorsque vous déboguez ou publiez votre projet, la version appropriée du runtime est utilisée.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code et Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) est utilisé pour développer la ligne de commande et également par [l’extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code. Pour un développement à partir de la version 2.x, installez la version 2.x de Core Tools. Pour un développement à partir de la version 1.x, vous devez installer la version 1.x de Core Tools. Pour plus d’informations, voir [Installer Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Pour un développement avec Visual Studio Code, vous devrez peut-être également mettre à jour le paramètre utilisateur pour `azureFunctions.projectRuntime` afin qu’il corresponde à la version des outils installés.  Ce paramètre met également à jour les modèles et les langages utilisés lors de la création de l’application de fonction.

### <a name="changing-version-of-apps-in-azure"></a>Changement de la version des applications dans Azure

La version du runtime Functions utilisée par les applications publiées dans Azure dépend du paramètre d’application [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version). Une valeur égale à `~2` cible la version 2.x du runtime et une valeur égale à `~1` cible la version 1.x du runtime. Ne modifiez pas arbitrairement ce paramètre. En effet, d’autres modifications du paramètre d’application et d’autres modifications du code dans vos fonctions sont probablement nécessaires. Pour en savoir plus sur la méthode recommandée afin de migrer votre application de fonction vers une version différente du runtime, voir [Comment cibler des versions du runtime Azure Functions](set-runtime-version.md).

## <a name="bindings"></a>Liaisons

À compter de la version 2.x, le runtime utilise un nouveau [modèle d’extensibilité de liaison](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) qui présente les avantages suivants :

* Prise en charge pour les extensions de liaison de tiers.

* Découplage du runtime et des liaisons. Cela permet de gérer les versions des extensions de liaison et leur publication de façon indépendante. Vous pouvez, par exemple, choisir de mettre à niveau vers une version d’une extension qui s’appuie sur une version plus récente d’un kit de développement logiciel (SDK) sous-jacent.

* Un environnement d’exécution plus léger, où seules les liaisons en cours d’utilisation sont connues et chargées par le runtime.

À l’exception des déclencheurs HTTP et de la minuterie, toutes les liaisons doivent être explicitement ajoutées au projet d’application de fonction, ou enregistrées dans le portail. Pour plus d’informations, voir [Inscrire des extensions de liaison](./functions-bindings-expressions-patterns.md).

Le tableau suivant indique les liaisons prises en charge dans chaque version du runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Coder et tester Azure Functions localement](functions-run-local.md)
* [Procédure pour cibler des versions du runtime Azure Functions](set-runtime-version.md)
* [Notes de publication](https://github.com/Azure/azure-functions-host/releases)
