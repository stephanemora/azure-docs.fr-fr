---
title: Créer une fonction dans Azure qui répond à des requêtes HTTP
description: Découvrez comment créer une fonction à partir de la ligne de commande, puis comment publier le projet local sur un hébergement serverless dans Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli, devx-track-azurepowershell
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: b299f0bb13bb25fbc192f3d117be11ca1ce26586
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145550"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Démarrage rapide : Créer une fonction dans Azure qui répond à des requêtes HTTP

::: zone pivot="programming-language-csharp"  
Dans cet article, vous allez utiliser des outils en ligne de commande pour créer une fonction basée sur une bibliothèque de classes C# qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-javascript"
Dans cet article, vous allez utiliser des outils en ligne de commande pour créer une fonction JavaScript qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. 
::: zone-end
::: zone pivot="programming-language-typescript"
Dans cet article, vous allez utiliser des outils en ligne de commande pour créer une fonction TypeScript qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. 
::: zone-end   
::: zone pivot="programming-language-powershell"
Dans cet article, vous allez utiliser des outils en ligne de commande pour créer une fonction PowerShell qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-python" 
Dans cet article, vous utilisez des outils en ligne de commande pour créer une fonction Python qui répond à des requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-java" 
Dans cet article, vous allez utiliser des outils en ligne de commande pour créer une fonction Java qui répond aux requêtes HTTP. Après avoir testé le code localement, vous le déployez dans l’environnement serverless d’Azure Functions. 
::: zone-end

Le fait de suivre ce guide de démarrage rapide entraîne une faible dépense de quelques cents USD tout au plus dans votre compte Azure.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Si vous souhaitez consulter une version de cet article adaptée à Visual Studio Code, [cliquez ici](functions-create-first-function-vs-code.md).
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Si Maven n’est pas votre outil de développement préféré, consultez nos tutoriels similaires destinés aux développeurs Java utilisant [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) et [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Créer un projet de fonction local

Dans Azure Functions, un projet de fonction est un conteneur pour une ou plusieurs fonctions qui répondent chacune à un déclencheur spécifique. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Exécutez la commande `func init`, de la façon suivante, pour créer un projet Functions dans un dossier nommé *LocalFunctionProj* avec le runtime spécifié :  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
Dans un dossier vide, exécutez la commande suivante pour générer le projet Functions à partir d’un [archétype Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). Utilisez `-DjavaVersion=11` si vous voulez exécuter vos fonctions sur Java 11. Pour plus d’informations, consultez les [versions Java](functions-reference-java.md#java-versions). 

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
```
---

Maven vous invite à entrer les valeurs nécessaires pour terminer la génération du projet lors du déploiement.   
Fournissez les valeurs suivantes à l’invite :

| Prompt | Valeur | Description |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Valeur qui identifie de façon unique votre projet parmi tous les projets, avec respect des [règles de nommage de package](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pour Java. |
| **artifactId** | `fabrikam-functions` | Valeur qui correspond au nom du fichier jar, sans numéro de version. |
| **version** | `1.0-SNAPSHOT` | Choisissez la valeur par défaut. |
| **package** | `com.fabrikam` | Valeur qui correspond au package Java pour le code de fonction généré. Utilisez la valeur par défaut. |

Tapez `Y` ou appuyez sur Entrée pour confirmer.

Maven crée les fichiers projet dans un nouveau dossier avec le nom d’_artifactId_, qui est `fabrikam-functions` dans cet exemple. 

::: zone-end  
Accédez au dossier du projet :

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Ce dossier contient divers fichiers pour le projet, notamment des fichiers config nommés [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Dans la mesure où *local.settings.json* peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier *.gitignore*.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Facultatif) Examiner le contenu du fichier

Si vous le souhaitez, vous pouvez passer à [Exécuter la fonction localement](#run-the-function-locally) et examiner le contenu du fichier plus tard.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* contient une méthode `Run` qui reçoit des données de requête dans la variable `req` de type [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), décorée avec **HttpTriggerAttribute** qui définit le comportement du déclencheur. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

L’objet retourné est un [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) qui retourne un message de réponse sous la forme d’un [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou d’un [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function.java
*Function.java* contient une méthode `run` qui reçoit des données de requête dans la variable `request` d’un objet [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage), doté de l’annotation[HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) qui définit le comportement du déclencheur. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Le message de réponse est généré par l’API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

#### <a name="pomxml"></a>pom.xml

Les paramètres des ressources Azure créées pour héberger votre application sont définis dans l’élément de **configuration** du plug-in avec un **groupId** de `com.microsoft.azure` dans le fichier pom.xml généré. Par exemple, l’élément de configuration ci-dessous indique à un déploiement basé sur Maven de créer une application de fonction dans le groupe de ressources `java-functions-group` de la région `westus`. L’application de fonction elle-même s’exécute sur Windows hébergé dans le plan `java-functions-app-service-plan` qui, par défaut, est un plan de consommation serverless.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Vous pouvez modifier ces paramètres pour contrôler la façon dont les ressources sont créées dans Azure, par exemple en modifiant `runtime.os` pour passer de `windows` à `linux` avant le déploiement initial. Pour obtenir la liste complète des paramètres pris en charge par le plug-in Maven, consultez les [détails de la configuration](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

Si vous souhaitez exécuter votre application de fonction sur Java 11 au lieu de Java 8, vous devez mettre à jour manuellement le fichier pom.xml avec les valeurs Java 11. Pour plus d’informations, consultez les [versions Java](functions-reference-java.md#java-versions). En cas d’exécution sur Java 11, assurez-vous que  

#### <a name="functiontestjava"></a>FunctionTest.java

L’archétype génère également un test unitaire pour votre fonction. Lorsque vous modifiez votre fonction pour ajouter des liaisons ou de nouvelles fonctions au projet, vous devez également modifier les tests dans le fichier *FunctionTest.java*.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contient une fonction Python `main()`, qui se déclenche selon la configuration définie dans *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req`, tel que cela est défini dans *function.json*. `req` est une instance de la [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). L’objet retourné, défini comme `$return` dans *function.json*, est une instance de la [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](./functions-bindings-http-webhook.md?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporte une fonction qui est déclenchée selon la configuration définie dans *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req`, tel que cela est défini dans *function.json*. L’objet retourné, défini comme `$return` dans *function.json*, est la réponse. Pour en savoir plus, voir [Déclencheurs et liaisons HTTP Azure Functions](./functions-bindings-http-webhook.md?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporte une fonction qui est déclenchée selon la configuration définie dans *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête dans la variable `req` de type **HttpRequest**, tel que cela est défini dans *function.json*. L’objet retourné, défini comme `$return` dans *function.json*, est la réponse. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* définit un script de fonction qui est déclenché selon la configuration définie dans *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Pour un déclencheur HTTP, la fonction reçoit les données de requête passées au paramètre `$Request`, tel que cela est défini dans *function.json*. L’objet retourné, défini comme `Response` dans *function.json*, est passé à l’applet de commande `Push-OutputBinding` comme réponse. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* est un fichier config qui définit le `bindings` d’entrée et de sortie de la fonction, notamment le type du déclencheur. 
::: zone-end

::: zone pivot="programming-language-python"
Vous pouvez changer `scriptFile` pour appeler un autre fichier Python, si vous le souhaitez.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Chaque liaison nécessite une direction, un type et un nom unique. Le déclencheur HTTP comporte une liaison d’entrée de type [`httpTrigger`](functions-bindings-http-webhook-trigger.md) et une liaison de sortie de type [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Créer des ressources Azure de prise en charge pour votre fonction

Avant de déployer le code de votre fonction dans Azure, vous devez créer trois ressources :

- Un groupe de ressources, qui est un conteneur logique pour les ressources associées.
- Un compte de stockage, qui conserve l’état et d’autres informations spécifiques à vos projets.
- Une application de fonction, qui fournit l’environnement d’exécution de votre code de fonction. Une application de fonction est mappée à votre projet de fonction local. Elle vous permet de regrouper les fonctions en tant qu’unité logique pour faciliter la gestion, le déploiement et le partage des ressources.

Utilisez les commandes Azure CLI suivantes pour créer ces éléments. Chaque commande fournit une sortie JSON à la fin de son exécution.

Si vous ne l’avez pas déjà fait, connectez-vous à Azure avec la commande [az login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```
    
Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources nommé `AzureFunctionsQuickstart-rg` dans la région `westeurope`. (Vous créez généralement votre groupe de ressources et vos ressources dans une région près de chez vous, en utilisant une région disponible à partir de la commande `az account list-locations`.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Vous ne pouvez pas héberger des applications Linux et Windows dans le même groupe de ressources. Si vous disposez d’un groupe de ressources existant nommé `AzureFunctionsQuickstart-rg` avec une application de fonction Windows ou une application web, vous devez utiliser un autre groupe de ressources.
 
    
Créez un compte de stockage universel dans votre groupe de ressources et votre région à l’aide de la commande [az storage account create](/cli/azure/storage/account#az-storage-account-create). Dans l’exemple suivant, remplacez `<STORAGE_NAME>` par un nom globalement unique qui vous convient. Les noms doivent contenir entre 3 et 24 caractères, et comporter uniquement des lettres minuscules. `Standard_LRS` spécifie un compte universel, qui est [pris en charge par Functions](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Le compte de stockage n’engendre que quelques centimes (USD) de dépense dans ce guide de démarrage rapide.
    
Créez l’application de fonction à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Dans l’exemple suivant, remplacez `<STORAGE_NAME>` par le nom du compte que vous avez utilisé à l’étape précédente, puis remplacez `<APP_NAME>` par le nom global unique qui vous convient. `<APP_NAME>` représente également le domaine DNS par défaut pour l’application de fonction. 
::: zone-end  

::: zone pivot="programming-language-python"  
Si vous utilisez Python 3.8, remplacez `--runtime-version` par `3.8` et `--functions_version` par `3`.

Si vous utilisez Python 3.6, remplacez `--runtime-version` par `3.6`.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Si vous utilisez Node.js 8, remplacez aussi `--runtime-version` par `8`.


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Cette commande crée une application de fonction qui s’exécute dans votre runtime de langage spécifié dans le [Plan Consommation Azure Functions](functions-scale.md#consumption-plan), qui est gratuit pour l’utilisation faite ici. La commande provisionne également une instance d’Azure Application Insights associée dans le même groupe de ressources, avec laquelle vous pouvez superviser votre application de fonction et visualiser les journaux. Pour plus d’informations, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md). L’instance n’entraîne aucun coût tant que vous ne l’activez pas.
    
## <a name="deploy-the-function-project-to-azure"></a>Déployer le projet de fonction sur Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Avant d’utiliser Core Tools pour déployer votre projet sur Azure, vous créez une build prête pour la production de fichiers JavaScript à partir des fichiers sources TypeScript.

La commande suivante prépare votre projet TypeScript pour le déploiement :

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Une fois les ressources nécessaires en place, vous êtes prêt à déployer votre projet Functions local sur l’application de fonction dans Azure à l’aide de la commande [func azure functionapp publish](functions-run-local.md#project-file-deployment). Dans l’exemple suivant, remplacez `<APP_NAME>` par le nom de votre application.

```
func azure functionapp publish <APP_NAME>
```

Si vous voyez l’erreur « Impossible de localiser l’application nommée... », attendez quelques secondes, puis réessayez, car Azure n’a peut-être pas complètement initialisé l’application après la commande `az functionapp create` précédente.

La commande de publication affiche des résultats similaires à la sortie suivante (tronquée par souci de simplicité) :

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Déployer le projet de fonction sur Azure

Une application de fonction et les ressources associées sont créées dans Azure lors du déploiement initial de votre projet Functions. Les paramètres des ressources Azure créées pour héberger votre application sont définis dans le [fichier pom.xml](#pomxml). Dans cet article, vous allez accepter les valeurs par défaut.

> [!TIP]
> Pour créer une application de fonction s’exécutant sur Linux au lieu de Windows, changez l’élément `runtime.os` dans le fichier pom.xml en remplaçant `windows` par `linux`. L’exécution de Linux dans un plan de consommation est prise en charge dans [ces régions](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Des applications s’exécutant sur Linux et des applications s’exécutant sur Windows ne peuvent pas se côtoyer dans le même groupe de ressources.

Avant de procéder au déploiement, utilisez la commande Azure CLI [az login](/cli/azure/authenticate-azure-cli) pour vous connecter à votre abonnement Azure. 

```azurecli
az login
```

Utilisez la commande suivante pour déployer votre projet vers une nouvelle application de fonction. 

```
mvn azure-functions:deploy
```

Cette commande crée les ressources suivantes dans Azure :

+ Groupe de ressources. Nommé en tant que _java-functions-group_.
+ Compte de stockage. Requis par les fonctions. Le nom est généré de façon aléatoire en fonction des exigences du nom de compte de stockage.
+ Plan d’hébergement. Hébergement serverless pour votre application de fonction dans la région _westus_. Le nom est _java-functions-app-service-plan_.
+ Application de fonction. Une application de fonction est l’unité de déploiement et d’exécution de vos fonctions. Le nom est généré de façon aléatoire, en fonction de votre _artifactId_ auquel est ajouté un numéro généré de façon aléatoire. 

Le déploiement empaquette les fichiers du projet et les déploie dans la nouvelle application de fonction à l’aide du [déploiement zip](functions-deployment-technologies.md#zip-deploy). Le code s’exécute à partir du package de déploiement dans Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Appeler la fonction sur Azure

Dans la mesure où votre fonction utilise un déclencheur HTTP, vous l’appelez en adressant une requête HTTP à son URL dans le navigateur ou à l’aide d’un outil semblable à curl. Dans les deux cas, le paramètre d’URL `code` est votre [clé de fonction](functions-bindings-http-webhook-trigger.md#authorization-keys) unique qui autorise l’appel de votre point de terminaison de fonction.

# <a name="browser"></a>[Browser](#tab/browser)

Copiez l’**URL d’appel** complète affichée au sein de la sortie de la commande de publication dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `&name=Functions`. Le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

![Sortie de la fonction exécutée sur Azure dans un navigateur](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

Exécutez [`curl`](https://curl.haxx.se/) avec l’**URL d’appel**, en ajoutant le paramètre `&name=Functions`. La sortie de la commande doit correspondre au texte « Hello Functions ».

![Sortie de la fonction exécutée sur Azure à l’aide de curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Pour voir les journaux en quasi temps réel d’une application de fonction publiée, utilisez le [flux de métriques temps réel Application Insights](functions-monitoring.md#streaming-logs).
>
> Exécutez la commande suivante pour ouvrir le flux de métriques temps réel dans un navigateur.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez à l’étape suivante, [Ajouter une liaison de sortie de file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md), gardez toutes vos ressources en place, car vous allez tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Pour quitter l’environnement virtuel, exécutez `deactivate`.
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md)
 
