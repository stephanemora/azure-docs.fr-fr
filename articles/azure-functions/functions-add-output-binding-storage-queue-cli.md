---
title: Connecter Azure Functions à Stockage Azure à l’aide des outils en ligne de commande
description: Découvrez comment connecter Azure Functions à une file d’attente Stockage Azure en ajoutant une liaison de sortie à votre projet en ligne de commande.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201000"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Connecter Azure Functions à Stockage Azure à l’aide des outils en ligne de commande

Dans cet article, vous intégrez une file d’attente Stockage Azure à la fonction et au compte de stockage que vous avez créés dans [le guide de démarrage rapide précédent](functions-create-first-azure-function-azure-cli.md). Vous effectuez cette intégration à l’aide d’une *liaison de sortie* qui écrit les données d’une requête HTTP dans un message en file d’attente. Le fait de suivre les instructions de cet article n’entraîne aucun coût supplémentaire au-delà des quelques cents USD du guide de démarrage rapide précédent. Pour en savoir plus sur les liaisons, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Configurer votre environnement local

Avant de commencer, vous devez terminer l’article [Démarrage rapide : Créer un projet Azure Functions à partir de la ligne de commande](functions-create-first-azure-function-azure-cli.md). Si vous avez déjà nettoyé les ressources à la fin de cet article, suivez à nouveau les étapes pour recréer l’application Functions et les ressources associées dans Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Récupérer la chaîne de connexion de Stockage Azure

Quand vous avez créé une application de fonction au sein d’Azure dans le guide de démarrage rapide précédent, vous avez également créé un compte de stockage. La chaîne de connexion pour ce compte est stockée de manière sécurisée dans les paramètres d’application au sein d’Azure. En téléchargeant le paramètre dans le fichier *local.settings.json*, vous pouvez utiliser cette connexion pour écrire dans une file d’attente de stockage du même compte au moment de l’exécution locale de la fonction. 

1. À partir de la racine du projet, exécutez la commande suivante, en remplaçant `<APP_NAME>` par le nom de votre application de fonction créée dans le guide de démarrage rapide précédent. Cette commande remplace toutes les valeurs existantes dans le fichier.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Ouvrez *local.settings.json* et recherchez la valeur nommée `AzureWebJobsStorage`, qui est la chaîne de connexion du compte de stockage. Vous utilisez le nom `AzureWebJobsStorage` et la chaîne de connexion dans d’autres sections de cet article.

> [!IMPORTANT]
> Dans la mesure où *local.settings.json* contient des secrets téléchargés à partir d’Azure, excluez toujours ce fichier du contrôle de code source. Le fichier *.gitignore* créé avec un projet Functions local exclut le fichier par défaut.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Ajouter une définition de liaison de sortie à la fonction

Bien qu’une fonction ne puisse avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Ainsi, vous pouvez vous connecter à d’autres services et ressources Azure sans avoir à écrire du code d’intégration personnalisé. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Vous déclarez ces liaisons dans le fichier *function.json* situé dans le dossier de la fonction. Compte tenu du guide de démarrage rapide précédent, votre fichier *function.json* situé dans le dossier *HttpExample* contient deux liaisons dans la collection `bindings` :  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Chaque liaison a au moins un type, une direction et un nom. Dans l’exemple ci-dessus, la première liaison est de type `httpTrigger`, et sa direction est `in`. Pour la direction `in`, `name` spécifie le nom d’un paramètre d’entrée qui est envoyé à la fonction quand il est appelé par le déclencheur.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
La deuxième liaison dans la collection est nommée `res`. Cette liaison `http` est une liaison de sortie (`out`) qui est utilisée pour écrire la réponse HTTP. 

Pour écrire dans une file d’attente Stockage Azure à partir de cette fonction, ajoutez une liaison `out` de type `queue` nommée `msg`, comme indiqué dans le code ci-dessous :

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
La deuxième liaison dans la collection est de type `http`, et sa direction est `out`. Dans ce cas, le `name` spécial de `$return` indique que cette liaison utilise la valeur retournée par la fonction au lieu de fournir un paramètre d’entrée.

Pour écrire dans une file d’attente Stockage Azure à partir de cette fonction, ajoutez une liaison `out` de type `queue` nommée `msg`, comme indiqué dans le code ci-dessous :

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
La deuxième liaison dans la collection est nommée `res`. Cette liaison `http` est une liaison de sortie (`out`) qui est utilisée pour écrire la réponse HTTP. 

Pour écrire dans une file d’attente Stockage Azure à partir de cette fonction, ajoutez une liaison `out` de type `queue` nommée `msg`, comme indiqué dans le code ci-dessous :

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Dans ce cas, `msg` est fourni à la fonction en tant qu’argument de sortie. Pour un type `queue`, vous devez également spécifier le nom de la file d’attente dans `queueName` et fournir le *nom* de la connexion Stockage Azure (à partir de *local.settings.json*) dans `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Pour plus d’informations sur les détails des liaisons, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md) et [Configuration de la sortie de la file d’attente](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Ajouter du code pour utiliser la liaison de sortie

Avec la liaison de file d’attente spécifiée dans *function.json*, vous pouvez à présent mettre à jour votre fonction pour recevoir le paramètre de sortie `msg` et écrire des messages dans la file d’attente.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Notez que vous *n’avez* pas besoin d’écrire du code pour l’authentification, l’obtention d’une référence de file d’attente ou l’écriture de données. Toutes ces tâches d’intégration sont prises en charge de manière pratique dans le runtime d’Azure Functions et la liaison de sortie de file d’attente.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Voir le message dans la file d’attente Stockage Azure

Vous pouvez voir la file d’attente dans le [portail Azure](../storage/queues/storage-quickstart-queues-portal.md) ou dans l’[Explorateur Stockage Microsoft Azure](https://storageexplorer.com/). Vous pouvez également voir la file d’attente dans Azure CLI, en effectuant les étapes suivantes :

1. Ouvrez le fichier *local.setting.json* du projet de fonction, et copiez la valeur de la chaîne de connexion. Dans une fenêtre de terminal ou une fenêtre Commande, exécutez la commande suivante pour créer une variable d’environnement nommée `AZURE_STORAGE_CONNECTION_STRING`, en collant votre chaîne de connexion spécifique à la place de `<MY_CONNECTION_STRING>`. (Cette variable d’environnement signifie que vous n’avez pas besoin de fournir la chaîne de connexion pour les prochaines commandes à l’aide de l’argument `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Facultatif) Utilisez la commande [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) pour voir les files d’attente de stockage dans votre compte. La sortie de cette commande doit inclure une file d’attente nommée `outqueue`, qui a été créée au moment où la fonction a écrit son premier message dans cette file d’attente.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. Utilisez la commande [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) pour lire le message de cette file d’attente, qui doit être le premier nom que vous avez utilisé quand vous avez précédemment testé la fonction. La commande lit et supprime le premier message dans la file d’attente. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Ce script utilise certutil pour décoder la collection de messages encodés en base64 dans un fichier temporaire local. S’il n’y a pas de sortie, essayez de supprimer `> NUL` du script pour arrêter la suppression de la sortie certutil, en cas d’erreur. 
    
    ---
    
    Étant donné que le corps du message stocké est [encodé en base64](functions-bindings-storage-queue-trigger.md#encoding), le message doit être décodé préalablement à son affichage. Une fois que vous avez exécuté `az storage message get`, le message est supprimé de la file d’attente. S’il n’y avait qu’un seul message dans `outqueue`, vous ne récupérez pas de message à la deuxième exécution de cette commande et vous obtenez une erreur à la place.

## <a name="redeploy-the-project-to-azure"></a>Redéployer le projet sur Azure

Une fois que vous avez vérifié localement que la fonction a écrit un message dans la file d’attente Stockage Azure, vous pouvez redéployer votre projet pour mettre à jour le point de terminaison exécuté sur Azure.

1. Dans le dossier *LocalFunctionsProj*, utilisez la commande [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) pour redéployer le projet, en remplaçant `<APP_NAME>` par le nom de votre application.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Comme dans le guide de démarrage rapide précédent, utilisez un navigateur ou CURL pour tester la fonction redéployée.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Copiez l’**URL d’appel** complète affichée au sein de la sortie de la commande de publication dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `&name=Functions`. Le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

    ![Sortie de la fonction exécutée sur Azure dans un navigateur](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    Exécutez [`curl`](https://curl.haxx.se/) avec l’**URL d’appel**, en ajoutant le paramètre `&name=Functions`. La sortie de la commande doit correspondre au texte « Hello Functions ».
    
    ![Sortie de la fonction exécutée sur Azure à l’aide de CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Examinez à nouveau la file d’attente de stockage, comme indiqué dans la section précédente, pour vérifier qu’elle contient le nouveau message écrit.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez mis à jour votre fonction déclenchée via HTTP pour écrire des données dans une file d’attente de stockage. Pour en savoir plus sur le développement de fonctions à partir de la ligne de commande avec Core Tools et Azure CLI, consultez maintenant ces articles :

+ [Utiliser Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Exemples de projets Functions complets en C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemples de projets Functions complets en JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guide des développeurs JavaScript sur Azure Functions](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemples de projets Functions complets en TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guide du développeur TypeScript sur Azure Functions](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemples de projets Functions complets en Python](/samples/browse/?products=azure-functions&languages=python)

+ [Guide du développeur Python sur Azure Functions](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemples de projets Functions complets dans PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Guide du développeur PowerShell sur Azure Functions](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)

+ [Page de tarification de Functions](https://azure.microsoft.com/pricing/details/functions/)

+ [Estimation des coûts d’un plan Consommation](functions-consumption-costs.md) 
