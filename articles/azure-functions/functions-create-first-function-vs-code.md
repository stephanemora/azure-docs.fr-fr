---
title: Créer votre première fonction dans Azure à l’aide de Visual Studio Code
description: Créer et publier une fonction HTTP déclenchée simple dans Azure à l’aide de l’extension Azure Functions dans Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842154"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Démarrage rapide : Créer un projet Azure Functions avec Visual Studio Code

Dans cet article, vous utilisez Visual Studio Code pour créer une fonction qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. Le fait de suivre ce guide de démarrage rapide entraîne une petite dépense de quelques cents USD tout au plus dans votre compte Azure. 

Il existe également une [version basée sur l’interface CLI](functions-create-first-azure-function-azure-cli.md) de cet article.

## <a name="prerequisites"></a>Conditions préalables requises

+ [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) pour Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS et Maintenance LTS (versions 8.11.1 et 10.14.1 recommandées).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) ou [Python 3.6](https://www.python.org/downloads/release/python-368/), qui sont pris en charge par Azure Functions. Python 3.8 n’est pas encore pris en charge. 

+ [Extension Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pour Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Kit SDK .NET Core 2.2+](https://www.microsoft.com/net/download)

+ [Extension PowerShell pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ [Extension Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pour Visual Studio Code. 

+ [Compte Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) avec un abonnement actif. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

## <a name="create-an-azure-functions-project"></a>Créer votre projet local 

Dans cette section, vous utilisez Visual Studio Code pour créer un projet Azure Functions local dans le langage de votre choix. Plus loin dans cet article, vous allez publier votre code de fonction sur Azure. 

1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, sélectionnez l’icône **Créer un projet**.

    ![Choisir Créer un projet](media/functions-create-first-function-vs-code/create-new-project.png)

1. Choisissez un emplacement de répertoire pour votre espace de travail de projet et optez pour **Sélectionner**.

    > [!NOTE]
    > Ces étapes ont été conçues pour être terminées en dehors d’un espace de travail. Dans ce cas, ne sélectionnez pas de dossier de projet qui fait partie d’un espace de travail.

1. Quand vous y êtes invité, indiquez les informations suivantes :

    ::: zone pivot="programming-language-csharp"

    | Prompt | Valeur | 
    | ------ | ----- | 
    | Sélectionner un langage pour votre projet de fonction | C# |
    | Sélectionner une version | Azure Functions v2 | 
    | Sélectionner un modèle pour la première fonction de votre projet | Déclencheur HTTP | 
    | Fournir un nom de fonction | HttpExample | 
    | Fournir un espace de noms | My.Functions | 
    | Niveau d’autorisation | Anonyme | 
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | Ajouter à l’espace de travail |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Prompt | Valeur | 
    | ------ | ----- | 
    | Sélectionner un langage pour votre projet de fonction | JavaScript | 
    | Sélectionner une version | Azure Functions v2 | 
    | Sélectionner un modèle pour la première fonction de votre projet | Déclencheur HTTP | 
    | Fournir un nom de fonction | HttpExample | 
    | Niveau d’autorisation | Anonyme | 
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | Ajouter à l’espace de travail |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Prompt | Valeur | 
    | ------ | ----- | 
    | Sélectionner un langage pour votre projet de fonction | TypeScript | 
    | Sélectionner une version | Azure Functions v2 | 
    | Sélectionner un modèle pour la première fonction de votre projet | Déclencheur HTTP | 
    | Fournir un nom de fonction | HttpExample | 
    | Niveau d’autorisation | Anonyme | 
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | Ajouter à l’espace de travail |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Prompt | Valeur | 
    | ------ | ----- | 
    | Sélectionner un langage pour votre projet de fonction | PowerShell | 
    | Sélectionner une version | Azure Functions v2 | 
    | Sélectionner un modèle pour la première fonction de votre projet | Déclencheur HTTP | 
    | Fournir un nom de fonction | HttpExample | 
    | Niveau d’autorisation | Anonyme | 
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | Ajouter à l’espace de travail |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Prompt | Valeur | 
    | ------ | ----- | 
    | Sélectionner un langage pour votre projet de fonction | Python | 
    | Sélectionner une version | Azure Functions v2 | 
    | Sélectionner un alias Python pour créer un environnement virtuel | Alias ​​Python | 
    | Sélectionner un modèle pour la première fonction de votre projet | Déclencheur HTTP | 
    | Fournir un nom de fonction | HttpExample | 
    | Niveau d’autorisation | Anonyme | 
    | Sélectionner la façon dont vous souhaitez ouvrir votre projet | Ajouter à l’espace de travail | 

    ::: zone-end

1. Visual Studio Code effectue les opérations suivantes :

    + Crée un projet Azure Functions dans un nouvel espace de travail, qui contient les fichiers config [host.json](functions-host-json.md) et [local.settings.json](functions-run-local.md#local-settings-file). 

    ::: zone pivot="programming-language-csharp"

    + Crée un [fichier bibliothèque de classes HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project), qui implémente la fonction.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Crée un fichier package.json dans le dossier racine.

    + Crée un dossier HttpExample, qui contient le [fichier de définition function.json](functions-reference-node.md#folder-structure) et le fichier [index.js](functions-reference-node.md#exporting-a-function), un fichier Node.js qui contient le code de fonction.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Crée un fichier package.json et un fichier tsconfig.json dans le dossier racine.

    + Crée un dossier HttpExample qui contient le [fichier de définition function.json](functions-reference-node.md#folder-structure) et le fichier [index.ts](functions-reference-node.md#typescript), un fichier TypeScript qui contient le code de fonction.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Crée un dossier HttpExample, qui contient le [fichier de définition function.json](functions-reference-python.md#programming-model), et le fichier run.ps1, qui contient le code de fonction.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Crée un fichier requirements.txt au niveau du projet, qui liste les packages nécessaires à Functions.
    
    + Crée un dossier HttpExample, qui contient le fichier de définition [function.json](functions-reference-python.md#programming-model) et le fichier \_\_init\_\_.py, qui contient le code de fonction.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps d’utiliser Visual Studio Code pour publier le projet directement sur Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Exécuter la fonction dans Azure

1. Copiez l’URL du déclencheur HTTP à partir du panneau **Sortie**. De nouveau, ajoutez la chaîne de requête `name` en tant que `?name=<yourname>` à la fin de cette URL, puis exécutez la requête.

    L’URL qui appelle la fonction déclenchée via HTTP doit être au format suivant :

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Collez cette nouvelle URL de requête HTTP dans la barre d’adresse de votre navigateur. L’exemple suivant montre la réponse dans le navigateur à la requête GET distante retournée par la fonction : 

    ![Réponse de la fonction dans le navigateur](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous passerez à l’étape suivante, [Ajouter une liaison de file d’attente Stockage Azure à votre fonction](functions-add-output-binding-storage-queue-vs-code.md), vous devrez conserver toutes vos ressources en place, car vous allez tirer parti de ce que vous avez déjà fait.

Sinon, vous pouvez utiliser les étapes suivantes pour supprimer l’application de fonction et ses ressources connexes afin d’éviter des coûts supplémentaires.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Pour en savoir plus sur les coûts de Functions, consultez [Estimation des coûts d’un plan Consommation](functions-consumption-costs.md).

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio Code pour créer une application de fonction à l’aide d’une fonction HTTP simple déclenchée via HTTP. Dans le prochain article, vous développez cette fonction en ajoutant une liaison de sortie. Cette liaison écrit la chaîne de la requête HTTP dans un message dans une file d’attente de Stockage File d’attente Azure. 

> [!div class="nextstepaction"]
> [Ajouter une liaison de file d’attente Stockage Azure à votre fonction](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
