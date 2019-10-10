---
title: Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python
description: Découvrez comment ajouter une liaison de sortie de file d’attente Stockage Azure à votre fonction Python en utilisant Azure CLI et Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839191"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python

Azure Functions vous permet de connecter des services Azure et d’autres ressources à des fonctions sans avoir à écrire votre propre code d’intégration. Ces *liaisons*, qui représentent l’entrée et la sortie, sont déclarées dans la définition de la fonction. Les données issues des liaisons sont fournies à la fonction en tant que paramètres. Un *déclencheur* est un type spécial de liaison d’entrée. Si une fonction ne peut avoir qu’un seul déclencheur, elle peut avoir plusieurs liaisons d’entrée et de sortie. Pour en savoir plus, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

Cet article vous montre comment intégrer la fonction que vous avez créée dans l’[article du guide de démarrage rapide précédent](functions-create-first-function-python.md) à une file d’attente Stockage Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

La plupart des liaisons requièrent une chaîne de connexion stockée que Functions utilise pour accéder au service lié. Pour faciliter cette connexion, vous utilisez le compte de stockage que vous avez créé avec votre application de fonction. La connexion à ce compte est déjà stockée dans un paramètre d’application nommé `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prérequis

Avant de commencer cet article, suivez les étapes de la [partie 1 du guide de démarrage rapide Python](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Télécharger les paramètres de l’application de fonction

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Activer les offres groupées d’extension

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Vous pouvez maintenant ajouter la liaison de sortie de stockage à votre projet.

## <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Dans Functions, chaque type de liaison requiert la définition d’une `direction`, d’un `type` et d’un `name` unique dans le fichier function.json. La façon dont vous définissez ces attributs dépend du langage de votre application de fonction.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Ajouter le code qui utilise la liaison de sortie

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Lorsque vous utilisez une liaison de sortie, vous n’avez pas besoin de recourir au code du Kit de développement logiciel (SDK) Stockage Azure pour l’authentification, l’obtention d’une référence de file d’attente ou l’écriture de données. La liaison de sortie de file d’attente et le runtime Functions effectuent ces tâches.

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Comme auparavant, utilisez la commande suivante pour démarrer le runtime Functions localement :

```bash
func host start
```

> [!NOTE]  
> Comme vous avez activé des offres groupées d’extension dans le fichier host.json dans le cadre du guide de démarrage rapide précédent, l’[extension de liaison de stockage](functions-bindings-storage-blob.md#packages---functions-2x) a été téléchargée et installée lors du démarrage, avec les autres extensions de liaison Microsoft.

Copiez l’URL de votre fonction `HttpTrigger` à partir de la sortie du runtime et collez-la dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. Vous devez voir la même réponse dans le navigateur que dans l’article précédent.

Cette fois, la liaison de sortie crée également une file d’attente nommée `outqueue` dans votre compte de stockage et ajoute un message avec cette même chaîne.

Ensuite, vous utilisez l’interface de ligne de commande Azure pour afficher la nouvelle file d’attente et vérifier qu’un message a été ajouté. Vous pouvez également afficher votre file d’attente en utilisant l’[Explorateur Stockage Azure][Azure Storage Explorer] ou sur le [portail Azure](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Définir la connexion de compte de stockage

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Interroger la file d’attente de stockage

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

À présent, il est temps de republier sur Azure l’application de fonction mise à jour.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Là encore, vous pouvez utiliser cURL ou un navigateur pour tester la fonction déployée. Comme précédemment, ajoutez la chaîne de requête `&name=<yourname>` à l’URL, comme dans l’exemple suivant :

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Vous pouvez [examiner le message de file d’attente de stockage](#query-the-storage-queue) pour vérifier que la liaison de sortie regénère un nouveau message dans la file d’attente.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez mis à jour votre fonction déclenchée via HTTP pour écrire des données dans une file d’attente de stockage. Pour en savoir plus sur le développement de fonctions Azure Functions avec Python, voir le [Guide des développeurs Python sur Azure Functions](functions-reference-python.md) et [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md). Pour obtenir des exemples de projets Functions complets en Python, consultez les [exemples Functions en Python](/samples/browse/?products=azure-functions&languages=python). 

Ensuite, vous devez activer la supervision Application Insights pour votre application de fonction :

> [!div class="nextstepaction"]
> [Activer l’intégration à Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/