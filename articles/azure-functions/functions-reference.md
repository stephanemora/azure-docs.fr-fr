---
title: Conseils relatifs au développement d'Azure Functions
description: Découvrez les concepts et techniques Azure Functions dont vous avez besoin pour développer des fonctions dans Azure, et ce, pour tous les langages de programmation et toutes les liaisons.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: fdc898c02cfd20ecfdd72dece4fb1e92d803dbb0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386898"
---
# <a name="azure-functions-developer-guide"></a>Guide du développeur Azure Functions
Dans Azure Functions, des fonctions spécifiques partagent quelques concepts techniques et composants de base, quels que soient le langage et la liaison que vous utilisez. Avant de passer à l'apprentissage des détails propres à un langage ou une liaison donnés, veillez à lire cette présentation qui s'applique à l’ensemble d’entre eux.

Cet article suppose que vous avez déjà lu la [Présentation d’Azure Functions](functions-overview.md).

## <a name="function-code"></a>Code de fonction
La *fonction* est le principal concept d'Azure Functions. Une fonction contient deux éléments importants - votre code, qui peut être écrit dans un certain nombre de langages, et, dans certaines configurations, le fichier function.json. Pour les langages compilés, ce fichier de configuration est généré automatiquement à partir des annotations de votre code. Pour les langages de script, vous devez fournir le fichier de configuration vous-même.

Le fichier function.json définit le déclencheur, les liaisons de fonction et d’autres paramètres de configuration. Chaque fonction possède un seul déclencheur. Le runtime utilise ce fichier de configuration pour déterminer les événements à surveiller et comment passer des données et renvoyer des données à partir de l’exécution d’une fonction. Voici un exemple de fichier function.json.

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

Pour plus d’informations, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

La propriété `bindings` vous permet de configurer les liaisons et les déclencheurs. Chaque liaison partage quelques paramètres communs et des paramètres propres à un type de liaison donné. Chaque liaison requiert les paramètres suivants :

| Propriété    | Valeurs | Type | Commentaires|
|---|---|---|---|
| type  | Nom de la liaison.<br><br>Par exemple : `queueTrigger`. | string | |
| direction | `in`, `out`  | string | Indique si la liaison sert à recevoir des données dans la fonction ou à envoyer des données à partir de la fonction. |
| name | Identificateur de fonction.<br><br>Par exemple : `myQueue`. | string | Le nom utilisé pour les données liées dans la fonction. Pour C#, il s’agit d’un nom d'argument ; pour JavaScript, il s’agit de la clé dans une liste de clés/valeurs. |

## <a name="function-app"></a>Conteneur de fonctions
Une application de fonction fournit un contexte d’exécution dans Azure dans lequel vos fonctions s’exécutent. À ce titre, elle constitue l'unité de déploiement et de gestion de vos fonctions. Un conteneur de fonctions est constitué d’une ou de plusieurs des fonctions individuelles qui sont gérées, déployées et mises à l’échelle ensemble. Toutes les fonctions d'une application de fonction partagent le même plan de tarification, la même méthode de déploiement et la même version du runtime. Considérez un conteneur de fonctions comme un moyen d’organiser et de gérer collectivement vos fonctions. Pour en savoir plus, consultez [Gérer une application de fonction](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Toutes les fonctions d’une application de fonction doivent être créées dans le même langage. Dans les [version précédentes](functions-versions.md) du runtime Azure Functions, cela n’était pas obligatoire.

## <a name="folder-structure"></a>Structure de dossiers
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

La structure de dossiers ci-dessus est recommandée et utilisée par défaut pour une application de fonction. Si vous souhaitez changer l’emplacement du code d’une fonction, modifiez la section `scriptFile` du fichier _function.json_. Nous vous recommandons également d’utiliser un [package de déploiement](deployment-zip-push.md) pour déployer votre projet sur votre application de fonction dans Azure. Vous pouvez également utiliser des outils existants tels que [l’intégration et le déploiement continus](functions-continuous-deployment.md) et Azure DevOps.

> [!NOTE]
> En cas de déploiement manuel d’un package, veillez à déployer votre fichier _host.json_ et vos dossiers de fonction directement dans le dossier `wwwroot`. N’incluez pas le dossier `wwwroot` dans vos déploiements. Sinon, vous vous retrouverez avec `wwwroot\wwwroot` dossiers.

#### <a name="use-local-tools-and-publishing"></a>Utiliser des outils locaux pour la publication
Vous pouvez créer et publier des applications de fonction à l’aide de différents outils, notamment [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) et [Azure Functions Core Tools](./functions-develop-local.md). Pour plus d’informations, consultez [Coder et tester Azure Functions localement](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Comment modifier des fonctions dans le portail Azure
L’éditeur de fonctions intégré au portail Azure vous permet de mettre à jour votre code et votre fichier *function.json* directement en ligne. Cela n’est recommandé que pour les petites modifications ou les preuves de concept - la meilleure pratique est d’utiliser un outil de développement local comme VS Code.

## <a name="parallel-execution"></a>Exécution parallèle
Lorsque plusieurs événements de déclenchement se produisent plus rapidement qu'un runtime de fonction monothread ne peut les traiter, le runtime peut appeler la fonction plusieurs fois en parallèle.  Si un conteneur de fonctions utilise le [plan d’hébergement de consommation](event-driven-scaling.md), il peut monter automatiquement effectuer un scale-out.  Que l’application s’exécute sur le plan d’hébergement de consommation ou sur un [plan d’hébergement App Service](../app-service/overview-hosting-plans.md) standard, chaque instance de l’application de fonction peut traiter en parallèle des appels de fonction simultanés en utilisant plusieurs threads.  Le nombre maximal d’appels de fonction simultanés dans chaque instance de chaque application de fonction varie en fonction du type de déclencheur utilisé, ainsi que des ressources utilisées par d’autres fonctions au sein de l’application de fonction.

## <a name="functions-runtime-versioning"></a>Contrôle de version du runtime Functions

Vous pouvez configurer la version du runtime Functions en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION`. Par exemple, la valeur « ~3 » indique que votre application de fonction utilise 3.x comme version principale. Les applications de fonction sont mises à niveau vers chaque nouvelle version mineure lorsqu’elles sont disponibles. Pour obtenir plus d’informations, notamment sur la façon d’afficher la version exacte de votre application de fonction, consultez [Guide pratique pour cibler des versions du runtime Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Référentiels
Le code pour Azure Fonctions est open source et stocké dans des dépôts GitHub :

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Hôte Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portail Azure Functions](https://github.com/azure/azure-functions-ux)
* [Modèles Azure Functions](https://github.com/azure/azure-functions-templates)
* [Kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensions du kit de développement logiciel Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Liaisons
Voici un tableau de toutes les liaisons prises en charge.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Vous rencontrez des problèmes avec des erreurs de liaisons ? Consultez la documentation [Codes d’erreur de liaison d’Azure Functions](functions-bindings-error-pages.md).


## <a name="connections"></a>Connexions

Votre projet de fonction référence des informations de connexion par nom à partir de son fournisseur de configuration. Il n’accepte pas directement les détails de la connexion, ce qui leur permet d’être changés dans l’ensemble des environnements. Par exemple, une définition de déclencheur peut inclure une propriété `connection`. Celle-ci peut faire référence à une chaîne de connexion, mais vous ne pouvez pas définir la chaîne de connexion directement dans un `function.json`. Au lieu de cela, vous devez définir `connection` sur le nom d’une variable d’environnement qui contient la chaîne de connexion.

Le fournisseur de configuration par défaut utilise des variables d’environnement. Celles-ci peuvent être définies par [paramètres d’application](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) lors de l’exécution dans le service Azure Functions, ou à partir du [fichier de paramètres local](functions-run-local.md#local-settings-file) lors du développement local.

### <a name="connection-values"></a>Valeurs de connexion

Quand le nom de la connexion correspond à une seule valeur exacte, le runtime identifie la valeur en tant que _chaîne de connexion_, qui comprend généralement un secret. Les détails d’une chaîne de connexion sont définis par le service auquel vous souhaitez vous connecter.

Toutefois, un nom de connexion peut également faire référence à une collection de plusieurs éléments de configuration. Les variables d’environnement peuvent être traitées comme une collection à l’aide d’un préfixe partagé qui se termine par deux traits de soulignement `__`. Il est ensuite possible de référencer le groupe en définissant le nom de la connexion sur ce préfixe.

Par exemple, la propriété `connection` pour une définition de déclencheur de blobs Azure peut être `Storage1`. Tant qu’il n’existe aucune valeur de chaîne unique configurée avec `Storage1` comme nom, `Storage1__serviceUri` serait utilisé pour la propriété `serviceUri` de la connexion. Les propriétés de connexion sont différentes pour chaque service. Reportez-vous à la documentation de l’extension qui utilise la connexion.

### <a name="configure-an-identity-based-connection"></a>Configurer une connexion basée sur une identité

Certaines connexions dans Azure Functions sont configurées pour utiliser une identité au lieu d’un secret. La prise en charge dépend de l’extension qui utilise la connexion. Dans certains cas, une chaîne de connexion peut toujours être nécessaire dans Functions, même si le service auquel vous vous connectez prend en charge les connexions basées sur une identité.

> [!IMPORTANT]
> Même si une extension de liaison prend en charge les connexions basées sur une identité, cette configuration peut ne pas être encore prise en charge dans le plan Consommation. Consultez le tableau de prise en charge ci-dessous.

Les connexions basées sur une identité sont prises en charge par le déclencheur et les extensions de liaison suivants :

| Nom de l’extension | Version d’extension                                                                                     | Prend en charge les connexions basées sur une identité dans le plan Consommation |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Objets blob Azure     | [Version 5.0.0-beta1 ou ultérieure](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | Non                                    |
| File d’attente Azure    | [Version 5.0.0-beta1 ou ultérieure](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | Non                                    |

> [!NOTE]
> La prise en charge des connexions basées sur une identité n’est pas encore disponible pour les connexions de stockage utilisées par le runtime Functions pour les comportements de base. Cela signifie que le paramètre `AzureWebJobsStorage` doit être une chaîne de connexion.

#### <a name="connection-properties"></a>Propriétés de connexion

Une connexion basée sur une identité pour un service Azure accepte les propriétés suivantes :

| Propriété    | Variable d’environnement | Est obligatoire | Description |
|---|---|---|---|
| URI de service | `<CONNECTION_NAME_PREFIX>__serviceUri` | Oui | URI du plan de données du service auquel vous vous connectez. |

Des options supplémentaires peuvent être prises en charge pour un type de connexion donné. Reportez-vous à la documentation du composant qui effectue la connexion.

Quand elles sont hébergées dans le service Azure Functions, les connexions basées sur une identité utilisent une [identité managée](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). L’identité attribuée par le système est utilisée par défaut. Lors d’une exécution dans d’autres contextes, tels que le développement local, votre identité de développeur est utilisée à la place, même si cela peut être personnalisé à l’aide de paramètres de connexion alternatifs.

##### <a name="local-development"></a>Développement local

Lors d’une exécution locale, la configuration ci-dessus indique au runtime d’utiliser votre identité de développeur locale. La connexion tente d’obtenir un jeton à partir des emplacements suivants, dans l’ordre :

- Un cache local partagé entre les applications Microsoft
- Le contexte utilisateur actuel dans Visual Studio
- Le contexte utilisateur actuel dans Visual Studio Code
- Le contexte utilisateur actuel dans Azure CLI

Si aucune de ces options ne fonctionne, une erreur se produit.

Dans certains cas, vous souhaiterez peut-être spécifier l’utilisation d’une identité différente. Vous pouvez ajouter des propriétés de configuration pour la connexion qui pointent vers l’identité alternative.

> [!NOTE]
> Les options de configuration suivantes ne sont pas prises en charge quand elles sont hébergées dans le service Azure Functions.

Pour vous connecter à l’aide d’un principal de service Azure Active Directory avec un ID client et un secret, définissez la connexion avec les propriétés suivantes :

| Propriété    | Variable d’environnement | Est obligatoire | Description |
|---|---|---|---|
| URI de service | `<CONNECTION_NAME_PREFIX>__serviceUri` | Oui | URI du plan de données du service auquel vous vous connectez. |
| ID client | `<CONNECTION_NAME_PREFIX>__tenantId` | Oui | ID du locataire Azure Active Directory (annuaire). |
| ID client | `<CONNECTION_NAME_PREFIX>__clientId` | Oui |  ID client (application) d’une inscription d’application dans le locataire. |
| Clé secrète client | `<CONNECTION_NAME_PREFIX>__clientSecret` | Oui | Un secret client qui a été généré pour l’inscription de l’application. |

#### <a name="grant-permission-to-the-identity"></a>Accorder l’autorisation à l’identité

Quelle que soit l’identité utilisée, elle doit avoir les autorisations nécessaires pour effectuer les actions prévues. Pour ce faire, il convient généralement d’affecter un rôle dans RBAC Azure ou de spécifier l’identité dans une stratégie d’accès, en fonction du service auquel vous vous connectez. Reportez-vous à la documentation de chaque service pour savoir quelles autorisations sont nécessaires et la façon dont elles peuvent être définies.

> [!IMPORTANT]
> Parmi les autorisations exposées par le service, certaines ne sont peut-être pas nécessaires pour tous les contextes. Dans la mesure du possible, adhérez au **principe du privilège minimum**, en accordant à l’identité uniquement les privilèges nécessaires. Par exemple, si l’application doit simplement lire à partir d’un blob, utilisez le rôle [Lecteur des données blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-reader), car le rôle [Propriétaire des données blob du stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) comprend des autorisations excessives pour une opération de lecture.


## <a name="reporting-issues"></a>Problèmes liés aux rapports
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Coder et tester Azure Functions localement](./functions-develop-local.md)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md)
* [Informations de référence pour les développeurs Node.js sur Azure Functions](functions-reference-node.md)
