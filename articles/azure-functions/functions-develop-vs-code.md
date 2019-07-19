---
title: Développer Azure Functions à l’aide de Visual Studio Code | Microsoft Docs
description: Découvrez comment développer et tester Azure Functions à l’aide de l’extension Azure Functions pour Visual Studio Code.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454240"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Développer Azure Functions à l’aide de Visual Studio Code

L’[extension Azure Functions pour Visual Studio Code] vous permet de développer, de tester et de déployer des fonctions C# sur Azure. S’il s’agit de votre première expérience avec Azure Functions, vous pouvez en apprendre davantage dans l’article [Présentation d’Azure Functions](functions-overview.md).

L’extension Azure Functions propose les avantages suivants : 

* Modifier, générer et exécuter des fonctions sur votre ordinateur de développement local. 
* Publier votre projet Azure Functions directement sur Azure. 
* Écrire vos fonctions dans différents langages tout en bénéficiant de tous les avantages du développement Visual Studio Code. 

L’extension peut être utilisée avec les langages suivants, pris en charge par le runtime Azure Functions 2.x : 

* [C# compiled](functions-dotnet-class-library.md) 
* [C# script](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Nécessite que vous [définissiez C# script comme langage de projet par défaut](#c-script-projects).

Dans cet article, des exemples sont disponibles uniquement pour les fonctions de bibliothèque de classes JavaScript (Node.js) et C#.  

Cet article fournit des informations sur le développement et la publication sur Azure de fonctions, à l’aide de l’extension Azure Functions. Avant de lire cet article, vous devez [créer votre première fonction à l’aide de Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Ne mélangez pas un développement local avec un développement de portail dans une même application de fonction. Quand vous publiez à partir d’un projet local dans une application de fonction, le processus de déploiement remplace toutes les fonctions que vous avez développées dans le portail.

## <a name="prerequisites"></a>Prérequis

Avant que vous puissiez installer et exécuter l’[extension Azure Functions][extension Azure Functions pour Visual Studio Code], les conditions suivantes doivent être remplies :

* [Visual Studio Code](https://code.visualstudio.com/) doit être installé sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Les autres ressources dont vous avez besoin, tel qu’un compte de stockage Azure, sont créées dans votre abonnement au cours du processus de [publication Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Vous pouvez développer des fonctions localement et les publier sur Azure sans devoir les démarrer et les exécuter en local. Il existe des prérequis supplémentaires pour l’exécution de vos fonctions localement, y compris le téléchargement automatique d’Azure Functions Core Tools. Pour plus d’informations, consultez la section [Prérequis supplémentaires pour une exécution locale](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Créer un projet Azure Functions

L’extension Functions vous permet de créer un projet d’application de fonction, ainsi que votre première fonction. Les étapes suivantes expliquent comment créer une fonction déclenchée via HTTP dans un nouveau projet Functions. Le [Déclencheur HTTP](functions-bindings-http-webhook.md) est le modèle de déclencheur de fonction le plus simple à expliquer.

1. À partir d’**Azure : Functions**, cliquez sur l’icône Créer une fonction.

    ![Créer une fonction](./media/functions-develop-vs-code/create-function.png)

1. Sélectionnez le dossier de votre projet d’application de fonction, puis **sélectionnez un langage pour votre projet de fonction**. 

1. Sélectionnez le modèle de fonction **Déclencheur HTTP**, ou vous pouvez choisir d’**ignorer ceci pour le moment** pour créer un projet sans fonction. Vous pourrez toujours [ajouter une fonction à votre projet](#add-a-function-to-your-project) ultérieurement. 

    ![Choisir le modèle de déclencheur HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Tapez `HTTPTrigger` pour le nom de fonction et appuyez sur Entrée, puis sélectionnez l’autorisation **Fonction**. Avec ce niveau d’autorisation, vous devez fournir une [clé de fonction](functions-bindings-http-webhook.md#authorization-keys) lors de l’appel du point de terminaison de la fonction.

    ![Choisir l’authentification de la fonction](./media/functions-develop-vs-code/create-function-auth.png)

    Une fonction est créée dans le langage que vous avez choisi à l’aide du modèle de fonction déclenchée via HTTP.

    ![Modèle de fonction déclenchée via HTTP dans Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

Le modèle de projet crée un projet dans le langage choisi, et installe les dépendances requises. Pour tous les langages, le nouveau projet comporte les fichiers suivants :

* **host.json** : vous permet de configurer l’hôte Functions. Ces paramètres s’appliquent lors de l’exécution en local et dans Azure. Pour plus d’informations, consultez l’article de référence sur [host.json](functions-host-json.md).

* **local.settings.json** : tient à jour les paramètres utilisés lors de l’exécution locale des fonctions. Ces paramètres sont uniquement utilisés lors de l’exécution locale. Pour en savoir plus, voir [Fichier de paramètres locaux](#local-settings-file).

    >[!IMPORTANT]
    >Étant donné que le fichier local.settings.json peut contenir des secrets, vous devez l’exclure du contrôle de code source du projet.

À ce stade, vous pouvez ajouter des liaisons d’entrée et de sortie à votre fonction en [modifiant le fichier function.json](#javascript-2), ou en [ajoutant un paramètre à une fonction de bibliothèque de classes C#](#c-class-library-2).

Vous pouvez également [ajouter une fonction à votre projet](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installer des extensions de liaison

À l’exception des déclencheurs HTTP et de minuteur, les liaisons sont implémentées dans des packages d’extension. Vous devez installer les packages d’extension pour les déclencheurs et liaisons qui en ont besoin. La façon dont vous devez installer les extensions de liaison dépend du langage de votre projet.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>Bibliothèque de classes C\#

Exécutez la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) dans la fenêtre de terminal pour installer les packages d’extension dont vous avez besoin dans votre projet. L’exemple suivant permet d’installer l’extension de stockage Azure, qui implémente des liaisons pour le stockage d’objets blob, de files d’attente et de tables.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Ajouter une fonction à votre projet

Vous pouvez ajouter une nouvelle fonction à un projet existant à l’aide de l’un des modèles de déclencheur Functions prédéfinis. Pour ajouter un nouveau déclencheur de fonction, appuyez sur F1 pour ouvrir la palette de commandes, puis recherchez et exécutez la commande **Azure Functions : Créer une fonction...** . Suivez les invites pour choisir le type de déclencheur et définir les attributs requis de ce dernier. Si votre déclencheur nécessite une clé d’accès ou une chaîne de connexion pour se connecter à un service, préparez-la avant de créer le déclencheur de fonction. 

Les résultats de cette opération varient selon le langage de votre projet :

### <a name="javascript"></a>JavaScript

Un dossier est créé dans le projet. Il contient un nouveau fichier function.json et le nouveau fichier de code JavaScript.

### <a name="c-class-library"></a>Bibliothèque de classes C\#

Un nouveau fichier de bibliothèque de classes C# (.cs) est ajouté à votre projet.

## <a name="add-input-and-output-bindings"></a>Ajouter des liaisons d’entrée et de sortie

Vous pouvez développer votre fonction en ajoutant des liaisons d’entrée et de sortie. La façon de procéder dépend du langage de votre projet. Pour en savoir plus sur les liaisons, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md). 

Les exemples suivants permettent de se connecter à une file d’attente de stockage nommée `outqueue`, où la chaîne de connexion pour le compte de stockage est définie le paramètre d’application `MyStorageConnection` du fichier local.settings.json. 

### <a name="javascript"></a>JavaScript

Visual Studio Code vous permet d’ajouter des liaisons à votre fichier function.json en suivant une série d’invites pratique. Pour créer une liaison, cliquez avec le bouton droit (Ctrl+Clic sur macOS) sur le fichier `function.json` dans le dossier de votre fonction, puis choisissez **Ajouter une liaison...** . 

![Ajouter une liaison à une fonction JavaScript existante ](media/functions-develop-vs-code/function-add-binding.png)

Voici des exemples d’invites pour définir une nouvelle liaison de sortie de stockage :

| Prompt | Valeur | Description |
| -------- | ----- | ----------- |
| **Sélectionner le sens de la liaison** | `out` | La liaison est une liaison de sortie. |
| **Sélectionner une liaison avec un sens...** | `Azure Queue Storage` | La liaison est une liaison de file d’attente Stockage Azure. |
| **Nom utilisé pour identifier cette liaison dans votre code** | `msg` | Nom qui identifie le paramètre de liaison référencé dans votre code. |
| **File d’attente à laquelle le message sera envoyé** | `outqueue` | Nom de la file d’attente dans laquelle écrit la liaison. Si *queueName* n’existe pas, la liaison le crée à la première utilisation. |
| **Sélectionnez le paramètre dans « local.setting.json »** | `MyStorageConnection` | Nom d’un paramètre d’application qui contient la chaîne de connexion du compte de stockage. Le paramètre `AzureWebJobsStorage` contient la chaîne de connexion du compte de stockage que vous avez créé avec l’application de fonction. |

Dans cet exemple, une liaison est ajoutée au tableau `bindings` dans votre fichier function.json :

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Vous pouvez également ajouter la même définition de liaison directement dans votre fichier function.json.

Dans le code de votre fonction, la liaison `msg` est accessible à partir du `context`, comme dans l’exemple suivant :

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Pour plus d’informations, consultez la référence [Liaison de sortie de stockage de file d’attente](functions-bindings-storage-queue.md#output---javascript-example).

### <a name="c-class-library"></a>Bibliothèque de classes C\#

Mettez à jour la méthode de la fonction et ajoutez le paramètre suivant à la définition de la méthode `Run` :

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ce code vous oblige à ajouter l’instruction `using` suivante :

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Le paramètre `msg` est un type `ICollector<T>`, qui représente une collection de messages écrits dans une liaison de sortie quand la fonction se termine. Vous ajoutez un ou plusieurs messages à la collection, qui sont envoyés à la file d’attente après l’exécution de la fonction.

Pour plus d’informations, consultez la référence [Liaison de sortie de stockage de file d’attente](functions-bindings-storage-queue.md#output---c-example).

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Publication dans Azure

Visual Studio Code vous permet de publier votre projet de fonctions directement sur Azure. Pendant le processus, vous créez une application de fonction et les ressources associées dans votre abonnement Azure. L’application de fonction fournit un contexte d'exécution pour vos fonctions. Le projet est empaqueté et déployé sur la nouvelle application de fonction dans votre abonnement Azure.

Lors de la publication à partir de Visual Studio Code, l’une des deux méthodes de déploiement suivantes est utilisée :

* [Zip Deploy avec le mode d’exécution à partir du package activé](functions-deployment-technologies.md#zip-deploy) : utilisée pour la plupart des déploiements Azure Functions.
* [URL du package externe](functions-deployment-technologies.md#external-package-url) : utilisée pour le déploiement vers des applications Linux sur un [plan de consommation](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Création d’application de fonction rapide

Par défaut, Visual Studio Code génère automatiquement des valeurs pour les ressources Azure requises par votre application de fonction. Ces valeurs sont basées sur le nom d’application de fonction que vous choisissez. Pour obtenir un exemple d’utilisation des valeurs par défaut pour la publication de votre projet vers une nouvelle application de fonction dans Azure, consultez l’article sur le [démarrage rapide de Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Si vous souhaitez fournir des noms explicites pour les ressources créées, vous devez activer la publication à l’aide des options avancées.

### <a name="enabled-publishing-with-advanced-create-options"></a>Publication activée avec options de création avancées

Pour obtenir le contrôle des paramètres associés à la création d’applications Azure Functions, mettez à jour l’extension Azure Functions pour activer les paramètres avancés.

1. Cliquez sur **Fichier > Préférences > Paramètres**

1. Accédez à **Paramètres utilisateur > Extensions > Azure Functions**

1. Cochez la case **Fonction Azure : Création avancée**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Publication vers une nouvelle application de fonction dans Azure avec la création avancée

Les étapes suivantes permettent de publier votre projet vers une nouvelle application de fonction créée à l’aide des options de création avancées.

1. Dans la zone **Azure : Fonctions**, sélectionnez l'icône Déployer dans l'application de fonction.

    ![Paramètres de l’application de fonction](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Si ce n'est déjà fait, vous êtes invité à vous **connecter à Azure**. Vous pouvez également **créer un compte Azure gratuit**. Une fois la connexion établie à partir du navigateur, revenez à Visual Studio Code.

1. Si vous disposez de plusieurs abonnements, **sélectionnez un abonnement** pour l'application de fonction, puis choisissez **+ Créer une nouvelle application de fonction dans Azure**.

1. Suivez les invites et fournissez les informations suivantes :

    | Prompt | Valeur | Description |
    | ------ | ----- | ----------- |
    | Sélectionnez une application de fonction dans Azure | + Créer une application de fonction dans Azure | Au niveau de l’invite suivante, tapez un nom global unique identifiant votre nouvelle application de fonction et appuyez sur Entrée. Les caractères valides pour un nom d’application de fonction sont `a-z`, `0-9` et `-`. |
    | Sélectionner un système d’exploitation | Windows | L’application de fonction s’exécute sous Windows |
    | Sélectionner un plan d’hébergement | Plan de consommation | [L’hébergement dans un plan de consommation](functions-scale.md#consumption-plan) serverless est utilisé. |
    | Sélectionner un runtime pour votre nouvelle application | Langage de votre projet | Le runtime doit correspondre au projet que vous publiez. |
    | Sélectionner un groupe de ressources pour les nouvelles ressources | Créer un groupe de ressources | Dans l’invite suivante, tapez un nom de groupe de ressources, comme `myResourceGroup`, et appuyez sur Entrée. Vous pouvez également choisir un groupe de ressources existant. |
    | Sélectionner un compte de stockage | Créer un nouveau compte de stockage | Dans l’invite suivante, tapez le nom global unique du nouveau compte de stockage utilisé par votre application de fonction et appuyez sur Entrée. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également choisir un compte existant. |
    | Sélectionner l’emplacement des nouvelles ressources | region | Choisissez également un emplacement dans une [région](https://azure.microsoft.com/regions/) proche de chez vous, ou proche d’autres services auxquels vos fonctions ont accès. |

    Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué. Sélectionnez **Afficher la sortie de** dans cette notification pour afficher les résultats de la création et du déploiement, y compris les ressources Azure que vous avez créées.

## <a name="republish-project-files"></a>Republier les fichiers du projet

Lorsque vous configurez un [déploiement continu](functions-continuous-deployment.md), votre application de fonction dans Azure est mise à jour chaque fois que les fichiers sources sont mis à jour à l’emplacement source connecté. Bien que nous recommandions cette pratique de développement, vous pouvez également republier les mises à jour des fichiers de votre projet à partir de Visual Studio Code. 

> [!IMPORTANT]
> La publication sur une application de fonction existante remplace le contenu de cette application dans Azure.

1. Dans Visual Studio Code, appuyez sur F1 pour ouvrir la palette de commandes. Dans la palette de commandes, recherchez et sélectionnez `Azure Functions: Deploy to function app...`.

1. Si ce n'est déjà fait, vous êtes invité à vous **connecter à Azure**. Une fois la connexion établie à partir du navigateur, revenez à Visual Studio Code. Si vous avez plusieurs abonnements, **sélectionnez un abonnement** qui contient votre application de fonction.

1. Choisissez votre application de fonction existante dans Azure. Dans la fenêtre d’avertissement sur le remplacement de tous les fichiers dans l’application de fonction, choisissez **Déployer** pour accepter l’avertissement et continuer. 

Le projet est régénéré, repackagé et chargé vers Azure. Le projet existant est remplacé par le nouveau package, et l’application de fonction redémarre.

## <a name="get-deployed-function-url"></a>Obtenir l’URL de la fonction déployée

Pour être en mesure d’appeler une fonction déclenchée via HTTP, vous avez besoin de l’URL de la fonction lors du déploiement sur votre application de fonction. Cette URL inclut les [clés de fonction](functions-bindings-http-webhook.md#authorization-keys) requises. Vous pouvez utiliser l’extension pour obtenir les URL de vos fonctions déployées.

1. Appuyez sur F1 pour ouvrir la palette de commandes, puis recherchez et exécutez la commande **Azure Functions : Copier l’URL de la fonction**.

1. Suivez les invites pour choisir votre application de fonction dans Azure, puis le déclencheur HTTP spécifique que vous souhaitez appeler. 

L’URL de la fonction est copiée dans le Presse-papiers, ainsi que les clés éventuelles requises transmises via le paramètre de requête `code`. Utilisez un outil HTTP pour envoyer des requêtes POST, ou un navigateur pour les requêtes GET envoyées à la fonction à distance.  

## <a name="run-functions-locally"></a>Exécuter des fonctions localement

L’extension Azure Functions vous permet d’exécuter un projet Functions sur votre ordinateur de développement local. Le runtime local est le même runtime que celui qui héberge votre application de fonction dans Azure. Les paramètres locaux sont lus à partir du [fichier local.settings.json](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>Prérequis supplémentaires pour une exécution locale

Pour être en mesure d’exécuter votre projet Functions localement, vous devez également respecter ces exigences supplémentaires :

* Installez la version 2.x d’[Azure Functions Core Tools](functions-run-local.md#v2). Le package Core Tools est téléchargé et installé pour vous automatiquement lorsque vous démarrez le projet en local. Ce package inclut l’intégralité du runtime Azure Functions. Par conséquent, son téléchargement et son installation peuvent prendre un certain temps.

* Installer les exigences spécifiques pour le langage choisi :

    | Langage | Prérequis |
    | -------- | --------- |
    | **C#** | [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Outils CLI .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Extension de débogueur pour Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Extension Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6+](https://www.python.org/downloads/)|

    <sup>*</sup>Active LTS et Maintenance LTS (versions 8.11.1 et 10.14.1 recommandées).

### <a name="configure-the-project-to-run-locally"></a>Configurer le projet pour une exécution locale

Le runtime Functions utilise un compte de stockage Azure en interne pour tous les types de déclencheurs autres que HTTP et webhooks. Autrement dit, vous devez définir la clé **Values.AzureWebJobsStorage** sur une chaîne de connexion de compte de stockage Azure valide.

Cette section utilise [l’extension Stockage Azure pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) avec [Microsoft Azure Storage Explorer](https://storageexplorer.com/) pour vous connecter à la chaîne de connexion de stockage et la récupérer.   

Pour définir la chaîne de connexion de compte de stockage :

1. Dans Visual Studio, ouvrez **Cloud Explorer**, développez **Compte de stockage** > **Votre compte de stockage**, puis sélectionnez **Propriétés** et copiez la valeur **Chaîne de connexion principale**.

2. Dans votre projet, ouvrez le fichier local.settings.json et définissez la valeur de la clé **AzureWebJobsStorage** sur la chaîne de connexion que vous avez copiée.

3. Répétez l’étape précédente pour ajouter des clés uniques au tableau **Valeurs** pour les autres connexions requises par vos fonctions.

Pour en savoir plus, voir [Fichier de paramètres locaux](#local-settings-file).

### <a name="debugging-functions-locally"></a>Débogage de fonctions en local  

Pour déboguer vos fonctions, appuyez sur F5. Si vous n’avez pas encore téléchargé le package [Core Tools][Azure Functions Core Tools], vous êtes invité à le faire. Lorsque Core Tools est installé et en cours d’exécution, la sortie est affichée dans le terminal. Cela revient au même que d’exécuter la commande Core Tools `func host start` dans le terminal, mais avec de nouvelles tâches de génération et un débogueur attaché.  

Lorsque le projet est en cours d’exécution, vous pouvez déclencher vos fonctions comme vous le feriez en cas de déploiement sur Azure. Lors de l’exécution en mode débogage, les points d’arrêt sont atteints dans Visual Studio Code comme prévu.

L’URL de requête pour les déclencheurs HTTP s’affiche dans la sortie du terminal. Les clés de fonction pour les déclencheurs HTTP ne sont pas utilisées lors de l’exécution locale. Pour plus d’informations, consultez [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md).  

Pour plus d’informations, consultez [Utiliser Azure Functions Core Tools][Azure Functions Core Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Par défaut, ces paramètres ne sont pas migrés automatiquement lorsque le projet est publié dans Azure. Une fois la publication terminée, vous pouvez publier les paramètres du fichier local.settings.json sur votre application de fonction dans Azure. Pour plus d’informations, consultez [Publier les paramètres de l’application](#publish-application-settings).

Les valeurs dans **ConnectionStrings** ne sont jamais publiées.

Ces valeurs de paramètres d’application de fonction peuvent aussi être lues dans votre code en tant que variables d’environnement. Pour plus d’informations, consultez la section Variables d’environnement de ces rubriques de référence propres à un langage :

* [C# précompilé](functions-dotnet-class-library.md#environment-variables)
* [Script C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Paramètres d’application dans Azure

Les paramètres du fichier local.settings.json de votre projet doivent être les mêmes que les paramètres de l’application de fonction dans Azure. Les paramètres que vous ajoutez au fichier local.settings.json doivent être également ajoutés à l’application de fonction dans Azure. Ces paramètres ne sont pas chargés automatiquement quand vous publiez le projet. De même, tous les paramètres que vous créez dans votre application de fonction [dans le portail](functions-how-to-use-azure-function-app-settings.md#settings) doivent être téléchargés dans votre projet local.

### <a name="publish-application-settings"></a>Publier les paramètres de l’application

Le moyen le plus simple de publier les paramètres obligatoires sur votre application de fonctions dans Azure consiste à utiliser le lien **Charger les paramètres** qui apparaît une fois votre projet correctement publié.

![Chargement des paramètres de l’application après le déploiement](./media/functions-develop-vs-code/upload-app-settings.png)

Vous pouvez également publier les paramètres à l’aide de la commande `Azure Functions: Upload Local Setting` dans la palette de commandes. Les paramètres individuels sont ajoutés aux paramètres de l’application dans Azure à l’aide de la commande `Azure Functions: Add New Setting...`. 

> [!TIP]
> Veillez à enregistrer votre fichier local.settings.json avant de le publier.

Si le fichier local est chiffré, il est déchiffré, publié et chiffré à nouveau. Si les mêmes paramètres présentent des valeurs différentes dans les deux emplacements, vous êtes invité à choisir la marche à suivre.

Affichez les paramètres d’une application existante dans la zone **Azure : Functions** en développant votre abonnement, votre application de fonction et **Paramètres de l’application**.

![Afficher les paramètres de l’application de fonction dans Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Télécharger les paramètres depuis Azure

Si vous avez créé des paramètres d’application dans Azure, vous pouvez les télécharger dans votre fichier local.settings.json. Utilisez la commande `Azure Functions: Download Remote Settings...`. 

Tout comme dans le cas du chargement, si le fichier local est chiffré, il est déchiffré, publié et chiffré à nouveau. Si les mêmes paramètres présentent des valeurs différentes dans les deux emplacements, vous êtes invité à choisir la marche à suivre.

## <a name="monitoring-functions"></a>Surveillance des fonctions

En cas d’[exécution locale](#run-functions-locally), les données de journal sont diffusées vers la console de terminal. Vous pouvez également obtenir les données de journal lorsque votre projet Functions est en cours d’exécution dans une application de fonction dans Azure. Soit vous pouvez vous connecter aux journaux diffusés en continu dans Azure pour voir les données de journal quasiment en temps réel, soit vous pouvez activer Application Insights pour une compréhension plus complète des comportements de votre application.

### <a name="streaming-logs"></a>Diffusion de journaux d’activité

Lors du développement d’une application, il est utile de visualiser des informations de journalisation en temps quasi réel. Vous pouvez afficher le flux de fichiers journaux générés par vos fonctions. La sortie suivante est un exemple de journaux diffusés en continu pour une requête faite à une fonction déclenchée via HTTP :

![Sortie de diffusion de journaux en continu pour un déclencheur HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

Pour en savoir plus, consultez la section [Journaux en continu](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Les journaux de diffusion en continu ne prennent en charge qu'une seule instance de l’hôte Functions. Lorsque votre fonction est mise à l’échelle vers plusieurs instances, les données des autres instances ne s'affichent pas dans le flux du journal. Dans Application Insights, le [Flux de métriques temps réel](../azure-monitor/app/live-stream.md) prend en charge plusieurs instances. Également en quasi temps réel, l'analytique de diffusion en continue repose aussi sur des [données échantillonnées](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Il est recommandé de superviser l’exécution de vos fonctions en intégrant votre application de fonction à Azure Application Insights. Lorsque vous créez une application de fonction dans le portail Azure, cette intégration est faite pour vous par défaut. Toutefois, lorsque vous créez votre application de fonction lors de la publication avec Visual Studio, l’intégration avec votre application de fonction dans Azure n’est pas effectuée.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Pour en savoir plus, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>Projets en C\# script

Par défaut, tous les projets C# sont créés sous forme de [projets de bibliothèque de classes C# compiled](functions-dotnet-class-library.md). Si vous préférez travailler sur des projets C# script, vous devez sélectionner C# script comme langage par défaut dans les paramètres d’extension Azure Functions.

1. Cliquez sur **Fichier > Préférences > Paramètres**.

1. Accédez à **Paramètres utilisateur > Extensions > Azure Functions**.

1. Choisissez **C#Script** dans **Fonction Azure : Langage du projet**.

À ce stade, les appels émis vers le package Core Tools sous-jacent incluent l’option `--csx`, qui génère et publie les fichiers de projet C# script (.csx). Lorsque ce langage par défaut est spécifié, tous les projets créés sont des projets C# script par défaut. Vous n’êtes pas invité à choisir un langage de projet lorsqu’une valeur par défaut est définie. Pour créer des projets dans d’autres langages, vous devez modifier ce paramètre ou le supprimer du fichier utilisateur settings.json. Une fois ce paramètre supprimé, vous êtes de nouveau invité à choisir votre langage lors de la création d’un projet.

## <a name="command-palette-reference"></a>Référence pour la palette de commandes

L’extension Azure Functions fournit une interface graphique utile dans la zone Azure pour interagir avec vos applications de fonction dans Azure. La même fonctionnalité est également disponible sous forme de commande dans la palette de commandes (F1). Les commandes propres à Azure Functions suivantes sont disponibles :

|Commande Azure Functions  | Description  |
|---------|---------|
|**Add New Settings...**  |  Crée un nouveau paramètre d’application dans Azure. Pour plus d’informations, consultez [Publier les paramètres de l’application](#publish-application-settings). Vous devrez peut-être également [télécharger ce paramètre dans vos paramètres locaux](#download-settings-from-azure). |
| **Configure Deployment Source...** | Connectez votre application de fonction dans Azure à un référentiel Git local. Pour plus d’informations, consultez [Déploiement continu pour Azure Functions](functions-continuous-deployment.md). |
| **Connect to GitHub Repository...** | Connectez votre application de fonction à un référentiel GitHub. |
| **Copy Function URL** | Obtient l’URL distante d’une fonction déclenchée via HTTP exécutée dans Azure. Pour plus d’informations, consultez Comment [obtenir l’URL de la fonction déployée](#get-deployed-function-url). |
| **Create function app in Azure...** | Crée une application de fonction sur votre abonnement dans Azure. Pour plus d’informations, consultez la section [Publication vers une nouvelle application de fonction dans Azure](#publish-to-azure).        |
| **Decrypt Settings** | Utilisez cette commande pour déchiffrer les [paramètres locaux](#local-settings-file) qui ont été chiffrés à l’aide de `Azure Functions: Encrypt Settings`.  |
| **Delete Function App...** | Supprime une application de fonction existante de votre abonnement dans Azure. Lorsqu’il n’y a aucune autre application dans le plan App Service, vous avez la possibilité de le supprimer également. Les autres ressources, comme les comptes de stockage et les groupes de ressources, ne sont pas supprimées. Pour supprimer toutes les ressources, vous devez utiliser la commande [Supprimer le groupe de ressources](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Votre projet local n’est pas affecté. |
|**Delete Function...**  | Supprime une fonction existante à partir d’une application de fonction dans Azure. Étant donné que cette suppression n’affecte pas votre projet local, envisagez de supprimer la fonction en local, puis [republiez votre projet](#republish-project-files). |
| **Delete Proxy...** | Supprime un proxy d’Azure Functions de votre application de fonction dans Azure. Pour en savoir plus sur les proxys, consultez [Utilisation d’Azure Functions Proxies](functions-proxies.md). |
| **Delete Setting...** | Supprimer un paramètre d’application de fonction dans Azure. N’affecte pas les paramètres de votre fichier local.settings.json. |
| **Disconnect from Repo...**  | Supprimez la connexion de [déploiement continu](functions-continuous-deployment.md) entre une application de fonction dans Azure et un référentiel de contrôle de code source. |
| **Download Remote Settings...** | Télécharge les paramètres de l’application de fonction choisie dans Azure dans votre fichier local.settings.json. Si le fichier local est chiffré, il est déchiffré, mis à jour et chiffré à nouveau. Si les mêmes paramètres présentent des valeurs différentes dans les deux emplacements, vous êtes invité à choisir la marche à suivre. Assurez-vous que vous avez enregistré les modifications apportées à votre fichier local.settings.json avant d’exécuter cette commande. |
| **Edit settings...** | Modifie la valeur d’un paramètre d’application de fonction existant dans Azure. N’affecte pas les paramètres de votre fichier local.settings.json.  |
| **Encrypt settings** | Chiffre les éléments individuels du tableau `Values` des [paramètres locaux](#local-settings-file). Dans ce fichier, `IsEncrypted` est également défini sur `true`, ce qui indique au runtime local de déchiffrer les paramètres avant de les utiliser. Chiffrez les paramètres locaux pour réduire le risque de fuite d’informations précieuses. Dans Azure, les paramètres d’application sont toujours stockés chiffrés. |
| **Execute Function Now** | Permet de démarrer manuellement une [fonction déclenchée par minuteur](functions-bindings-timer.md) dans Azure à des fins de test. Pour en savoir plus sur le déclenchement des fonctions non-HTTP dans Azure, consultez [Exécuter manuellement une fonction non déclenchées via HTTP](functions-manually-run-non-http.md). |
| **Initialize Project for Use with VS Code...** | Ajoute les fichiers de projet Visual Studio Code requis à un projet Functions existant. Cette commande permet de travailler sur un projet créé à l’aide du package Core Tools. |
| **Install or Update Azure Functions Core Tools** | Installe ou met à jour les outils [Azure Functions Core Tools] utilisés pour l’exécution en local. |
| **Redeploy**  | Vous permet de redéployer les fichiers de projet à partir d’un référentiel Git connecté, vers un déploiement spécifique dans Azure. Pour republier les mises à jour locales à partir de Visual Studio Code, [republiez votre projet](#republish-project-files). |
| **Rename Settings...** | Modifie le nom de clé d’un paramètre d’application de fonction existant dans Azure. N’affecte pas les paramètres de votre fichier local.settings.json. Après avoir renommé les paramètres dans Azure, vous devez [télécharger ces modifications dans le projet local](#download-settings-from-azure). |
| **Restart** | Redémarre l’application de fonction dans Azure. Le déploiement de mises à jour entraîne également le redémarrage de l’application de fonction. |
| **Set AzureWebJobStorage...**| Définit la valeur du paramètre d’application `AzureWebJobStorage`. Ce paramètre est requis par Azure Functions. Il est défini lors de la création de l’application de fonction dans Azure. |
| **Start** | Démarre une application de fonction arrêtée dans Azure. | 
| **Start Streaming Logs** | Démarre la diffusion des journaux en continu pour l’application de fonction dans Azure. Utilisez ces journaux pendant le dépannage à distance dans Azure si vous avez besoin d’afficher ces informations en temps quasi réel. Pour en savoir plus, consultez la section [Journaux en continu](#streaming-logs). |
| **Stop** | Arrête une application de fonction exécutée dans Azure. |
| **Stop Streaming Logs** | Arrête la diffusion des journaux en continu pour l’application de fonction dans Azure. |
| **Toggle as Slot Setting** | Lorsque cette option est activée, elle permet de s’assurer qu’un paramètre d’application persiste pour un emplacement de déploiement donné. |
| **Uninstall Azure Functions Core Tools** | Supprime le package Azure Functions Core Tools, ce qui est requis par l’extension. |
| **Upload Local Settings...** | Charge les paramètres de votre fichier local.settings.json vers l’application de fonction choisie dans Azure. Si le fichier local est chiffré, il est déchiffré, chargé et chiffré à nouveau. Si les mêmes paramètres présentent des valeurs différentes dans les deux emplacements, vous êtes invité à choisir la marche à suivre. Assurez-vous que vous avez enregistré les modifications apportées à votre fichier local.settings.json avant d’exécuter cette commande. |
| **View Commit in GitHub** | Affiche la dernière validation dans un déploiement spécifique lorsque votre application de fonction est connectée à un référentiel. |
| **View Deployment Logs** | Affiche les journaux associés à un déploiement propre à l’application de fonction dans Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Functions Core Tools, consultez [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md).

Pour en savoir plus sur le développement de fonctions en tant que bibliothèques de classes, consultez [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md). Cet article fournit également des liens vers des exemples d’utilisation d’attributs pour déclarer les différents types de liaisons pris en charge par Azure Functions.    

[Extension Azure Functions pour Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md