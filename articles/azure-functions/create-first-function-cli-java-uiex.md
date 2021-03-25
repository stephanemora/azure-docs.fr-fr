---
title: Créer une fonction Java à partir de la ligne de commande – Azure Functions
description: Apprenez à créer une fonction Java à partir de la ligne de commande, puis à publier le projet local sur un hébergement serverless dans Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050424"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Démarrage rapide : Créer une fonction Java dans Azure à partir de la ligne de commande

> [!div class="op_single_selector" title1="Sélectionnez votre langage de fonction : "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Utilisez des outils en ligne de commande pour créer une fonction Java qui répond à des requêtes HTTP. Testez le code localement, puis déployez-le dans l’environnement serverless d’Azure Functions.

Le suivi de ce guide de démarrage rapide génère une dépense de quelques cents USD tout au plus dans votre <abbr title="Profil qui gère les informations de facturation pour l’utilisation d’Azure.">Compte Azure</abbr>.

Si Maven n’est pas votre outil de développement préféré, consultez nos tutoriels similaires destinés aux développeurs Java utilisant [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) et [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

+ Un compte Azure avec un <abbr title="Structure organisationnelle de base dans laquelle vous gérez les ressources dans Azure, généralement associée à une personne ou à un service au sein d’une organisation.">abonnement</abbr>. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure.

+ [Java Developer Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) version 8 ou 11. La variable d’environnement `JAVA_HOME` doit être définie sur l’emplacement d’installation de la version appropriée du kit JDK.

+ [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.

### <a name="prerequisite-check"></a>Vérification du prérequis

+ Dans une fenêtre de terminal ou de commande, exécutez `func --version` pour vérifier que vous disposez d’ <abbr title="Ensemble d’outils en ligne de commande permettant d’utiliser Azure Functions sur votre ordinateur local.">Azure Functions Core Tools</abbr> version 3.x.

+ Exécutez `az --version` pour vérifier que vous disposez d’Azure CLI version 2.4 ou ultérieure.

+ Exécutez `az login` pour vous connecter à Azure et vérifier que l’abonnement est actif.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Créer un projet de fonction local

Dans Azure Functions, un projet de fonction est un conteneur pour une ou plusieurs fonctions qui répondent chacune à un <abbr title="Type d’événement qui appelle le code de la fonction, par exemple une requête HTTP, un message de file d’attente ou une heure spécifique.">déclencheur</abbr>. Toutes les fonctions d’un projet partagent les mêmes configurations locale et d’hébergement. Dans cette section, vous créez un projet de fonction qui contient une seule fonction.

1. Dans un dossier vide, exécutez la commande suivante pour générer le projet Functions à partir d’un [archétype Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)

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

    <br/>
    <details>
    <summary><strong>Pour exécuter des fonctions sur Java 11</strong></summary>

    Utilisez `-DjavaVersion=11` si vous voulez que vos fonctions s’exécutent sur Java 11. Pour plus d’informations, consultez les [versions Java](functions-reference-java.md#java-versions).
    </details>

1. Maven vous invite à entrer les valeurs nécessaires pour terminer la génération du projet lors du déploiement.
    Fournissez les valeurs suivantes à l’invite :

    | Prompt | Valeur | Description |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Valeur qui identifie de façon unique votre projet parmi tous les projets, avec respect des [règles de nommage de package](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) pour Java. |
    | **artifactId** | `fabrikam-functions` | Valeur qui correspond au nom du fichier jar, sans numéro de version. |
    | **version** | `1.0-SNAPSHOT` | Choisissez la valeur par défaut. |
    | **package** | `com.fabrikam` | Valeur qui correspond au package Java pour le code de fonction généré. Utilisez la valeur par défaut. |

1. Tapez `Y` ou appuyez sur Entrée pour confirmer.

    Maven crée les fichiers projet dans un nouveau dossier avec le nom d’_artifactId_, qui est `fabrikam-functions` dans cet exemple. 

1. Accédez au dossier du projet :

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Qu’est-ce qui est créé dans le dossier LocalFunctionProj ?</strong></summary>

Ce dossier contient différents fichiers pour le projet, tels que *Function.java*, *FunctionTest.java* et *pom.xml*. Il existe également des fichiers de configuration nommés [local.settings.json](functions-run-local.md#local-settings-file) et [host.json](functions-host-json.md). Dans la mesure où *local.settings.json* peut contenir des secrets téléchargés à partir d’Azure, le fichier est exclu du contrôle de code source par défaut dans le fichier *.gitignore*.
</details>

<br/>
<details>
<summary><strong>Code pour Function.java</strong></summary>

*Function.java* contient une méthode `run` qui reçoit des données de requête dans la variable `request` d’un objet [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage), doté de l’annotation [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) qui définit le comportement du déclencheur. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Le message de réponse est généré par l’API [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

L’archétype génère également un test unitaire pour votre fonction. Lorsque vous modifiez votre fonction pour ajouter des liaisons ou de nouvelles fonctions au projet, vous devez également modifier les tests dans le fichier *FunctionTest.java*.
</details>

<br/>
<details>
<summary><strong>Code pour pom.xml</strong></summary>

Les paramètres des ressources Azure créées pour héberger votre application sont définis dans l’élément de **configuration** du plug-in avec un **groupId** égal à `com.microsoft.azure` dans le fichier *pom.xml* généré. Par exemple, l’élément de configuration ci-dessous indique à un déploiement basé sur Maven de créer une application de fonction dans le groupe de ressources `java-functions-group` de la région `westus` <abbr title="Référence géographique à un centre de données Azure spécifique dans lequel des ressources sont allouées.">region</abbr>. L’application de fonction elle-même s’exécute sur Windows hébergé dans le plan `java-functions-app-service-plan` qui, par défaut, est un plan de consommation serverless.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Vous pouvez modifier ces paramètres pour contrôler la façon dont les ressources sont créées dans Azure, par exemple en modifiant `runtime.os` pour passer de `windows` à `linux` avant le déploiement initial. Pour obtenir la liste complète des paramètres pris en charge par le plug-in Maven, consultez les [détails de la configuration](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Exécuter la fonction localement

1. **Exécutez votre fonction** en démarrant l’hôte du runtime d’Azure Functions local à partir du dossier *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    Vers la fin de la sortie, les lignes suivantes doivent s’afficher :

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Si HttpExample n’apparaît pas comme indiqué ci-dessus, cela signifie probablement que vous avez démarré l’hôte à partir d’un emplacement autre que le dossier racine du projet. Dans ce cas, utilisez <kbd>Ctrl+C</kbd> pour arrêter l’hôte, accédez au dossier racine du projet, puis réexécutez la commande précédente.

1. **Copiez l’URL** de votre fonction `HttpExample` à partir de cette sortie dans un navigateur et ajoutez la chaîne de requête `?name=<YOUR_NAME>` pour obtenir une URL complète semblable à `http://localhost:7071/api/HttpExample?name=Functions`. Le navigateur doit afficher un message similaire à `Hello Functions` :

    ![Résultat de la fonction exécutée localement dans le navigateur](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Le terminal dans lequel vous avez démarré votre projet affiche également une sortie de journal quand vous effectuez des requêtes.

1. Quand vous avez terminé, utilisez <kbd>Ctrl+C</kbd> et choisissez <kbd>y</kbd> pour arrêter l’hôte Functions.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Déployer le projet de fonction sur Azure

Une application de fonction et les ressources associées sont créées dans Azure lors du déploiement initial de votre projet Functions. Les paramètres des ressources Azure créées pour héberger votre application sont définis dans le fichier *pom.xml*. Dans cet article, vous allez accepter les valeurs par défaut.

<br/>
<details>
<summary><strong>Pour créer une application de fonction s’exécutant sur Linux</strong></summary>

Pour créer une application de fonction s’exécutant sur Linux au lieu de Windows, changez l’élément `runtime.os` dans le fichier *pom.xml* en remplaçant `windows` par `linux`. L’exécution de Linux dans un plan de consommation est prise en charge dans [ces régions](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Des applications s’exécutant sur Linux et des applications s’exécutant sur Windows ne peuvent pas se côtoyer dans le même groupe de ressources.
</details>

1. Avant d’effectuer le déploiement, connectez-vous à votre abonnement Azure en utilisant Azure CLI ou Azure PowerShell. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    La commande [az login](/cli/azure/reference-index#az-login) vous connecte à votre compte Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    L’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) vous connecte à votre compte Azure.

    ---

1. Utilisez la commande suivante pour déployer votre projet vers une nouvelle application de fonction.

    ```console
    mvn azure-functions:deploy
    ```

    Le déploiement empaquette les fichiers du projet et les déploie dans la nouvelle application de fonction à l’aide du [déploiement zip](functions-deployment-technologies.md#zip-deploy). Le code s’exécute à partir du package de déploiement dans Azure.

<br/>
<details>
<summary><strong>Qu’est-ce qui est créé dans Azure ?</strong></summary>

+ Groupe de ressources. Nommé en tant que _java-functions-group_.
+ Compte de stockage. Requis par les fonctions. Le nom est généré de façon aléatoire en fonction des exigences du nom de compte de stockage.
+ Plan d’hébergement. Hébergement serverless pour votre application de fonction dans la région _westus_. Le nom est _java-functions-app-service-plan_.
+ Application de fonction. Une application de fonction est l’unité de déploiement et d’exécution de vos fonctions. Le nom est généré de façon aléatoire, en fonction de votre _artifactId_ auquel est ajouté un numéro généré de façon aléatoire.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Appeler la fonction sur Azure

Dans la mesure où votre fonction utilise un déclencheur HTTP, vous **l’appelez en adressant une requête HTTP à son URL** dans le navigateur ou avec un outil semblable à <abbr title="Outil en ligne de commande permettant de générer des requêtes HTTP sur une URL ; voir https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Browser](#tab/browser)

Copiez l’**URL d’appel** complète affichée dans la sortie de la commande `publish` dans la barre d’adresse d’un navigateur, en ajoutant le paramètre de requête `&name=Functions`. Le navigateur doit afficher une sortie similaire à celle générée au moment de l’exécution locale de la fonction.

![Sortie de la fonction exécutée sur Azure dans un navigateur](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Exécutez [`curl`](https://curl.haxx.se/) avec l’**URL d’appel**, en ajoutant le paramètre `&name=Functions`. La sortie de la commande doit correspondre au texte « Hello Functions ».

![Sortie de la fonction exécutée sur Azure à l’aide de curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Nettoyer les ressources

Si vous passez à l’[étape suivante](#next-steps) et que vous ajoutez une <abbr title="Dans le Stockage Azure, moyen d’associer une fonction à une file d’attente de stockage, afin qu’elle puisse créer des messages dans la file d’attente.">liaison de sortie de file d’attente</abbr>, gardez toutes vos ressources en place pour tirer parti de ce que vous avez déjà fait.

Sinon, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient afin d’éviter les coûts supplémentaires.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Se connecter à une file d’attente Stockage Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
