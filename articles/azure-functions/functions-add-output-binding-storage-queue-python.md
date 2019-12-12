---
title: Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python
description: Découvrez comment ajouter une liaison de sortie de file d’attente Stockage Azure à votre fonction Python.
ms.date: 10/02/2019
ms.topic: quickstart
ms.openlocfilehash: f2db0f470c4205919343e3838a4f73b05bf71fb0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928876"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Ajouter une liaison de file d’attente Stockage Azure à votre fonction Python

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Cet article vous montre comment intégrer la fonction que vous avez créée dans l’[article du guide de démarrage rapide précédent](functions-create-first-function-python.md) à une file d’attente Stockage Azure. La liaison de sortie que vous ajoutez à cette fonction écrit des données d’une requête HTTP dans un message en file d’attente.

La plupart des liaisons requièrent une chaîne de connexion stockée que Functions utilise pour accéder au service lié. Pour faciliter cette connexion, vous utilisez le compte de stockage que vous avez créé avec votre application de fonction. La connexion à ce compte est déjà stockée dans un paramètre d’application nommé `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Prérequis

Avant de commencer cet article, suivez les étapes de la [partie 1 du guide de démarrage rapide Python](functions-create-first-function-python.md).

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Télécharger les paramètres de l’application de fonction

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

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
> Comme vous avez activé des bundles d’extension dans le fichier host.json, l’[extension de liaison de stockage](functions-bindings-storage-blob.md#packages---functions-2x-and-higher) a été téléchargée et installée lors du démarrage, avec les autres extensions de liaison Microsoft.

Copiez l’URL de votre fonction `HttpTrigger` à partir de la sortie du runtime et collez-la dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. Vous devez voir la même réponse dans le navigateur que dans l’article précédent.

Cette fois, la liaison de sortie crée également une file d’attente nommée `outqueue` dans votre compte de stockage et ajoute un message avec cette même chaîne.

Ensuite, vous utilisez l’interface de ligne de commande Azure pour afficher la nouvelle file d’attente et vérifier qu’un message a été ajouté. Vous pouvez également afficher votre file d’attente en utilisant l’[Explorateur Stockage Azure][Azure Storage Explorer] ou sur le [portail Azure](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Redéployer le projet 

Pour mettre à jour votre application publiée, utilisez la commande [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools pour déployer votre code de projet sur Azure. Dans cet exemple, remplacez `<APP_NAME>` par le nom de votre application.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Là encore, vous pouvez utiliser cURL ou un navigateur pour tester la fonction déployée. Comme précédemment, ajoutez la chaîne de requête `&name=<yourname>` à l’URL, comme dans l’exemple suivant :

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Vous pouvez [réexaminer le message de file d’attente de stockage](#query-the-storage-queue) pour vérifier que la liaison de sortie génère un nouveau message dans la file d’attente, comme prévu.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez mis à jour votre fonction déclenchée via HTTP pour écrire des données dans une file d’attente de stockage. Pour en savoir plus sur le développement de fonctions Azure Functions avec Python, voir le [Guide des développeurs Python sur Azure Functions](functions-reference-python.md) et [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md). Pour obtenir des exemples de projets Functions complets en Python, consultez les [exemples Functions en Python](/samples/browse/?products=azure-functions&languages=python). Pour en savoir plus sur le prix, consultez la [page de tarification des fonctions](https://azure.microsoft.com/pricing/details/functions/) et l’article [Estimation du coût d’un plan Consommation](functions-consumption-costs.md).

Ensuite, vous devez activer la supervision Application Insights pour votre application de fonction :

> [!div class="nextstepaction"]
> [Activer l’intégration à Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/