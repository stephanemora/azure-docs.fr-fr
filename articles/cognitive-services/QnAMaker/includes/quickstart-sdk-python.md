---
title: 'Démarrage rapide : Bibliothèque de client QnA Maker pour Python'
description: Ce guide de démarrage rapide montre comment commencer à utiliser la bibliothèque de client QnA Maker pour Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 70d55bd6fba209119efa0d3828cad91c2de09f82
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792331"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

Utilisez la bibliothèque de client QnA Maker pour Python afin de :

* Créer une base de connaissances
* Mettre à jour une base de connaissances
* Publier une base de connaissances
* Obtenir la clé de point de terminaison du runtime de prédiction
* Attendre l’exécution d’une tâche de longue durée
* Télécharger une base de connaissances
* Obtenir une réponse d’une base de connaissances
* Supprimer une base de connaissances

[Documentation de référence](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/) | [Exemples Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

Utilisez la bibliothèque de client QnA Maker pour Python afin de :

* Créer une base de connaissances
* Mettre à jour une base de connaissances
* Publier une base de connaissances
* Attendre l’exécution d’une tâche de longue durée
* Télécharger une base de connaissances
* Obtenir une réponse d’une base de connaissances
* Supprimer une base de connaissances

[Documentation de référence](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Exemples Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prérequis

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Une fois en possession de votre abonnement Azure, créez une [ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) sur le portail Azure pour obtenir vos clé et point de terminaison de création. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API QnA Maker. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Une fois en possession de votre abonnement Azure, créez une [ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) sur le portail Azure pour obtenir vos clé et point de terminaison de création.
    * REMARQUE : Veillez à cocher la case **Managé**.
    * Après le déploiement de votre ressource QnA Maker, sélectionnez **Accéder à la ressource**. Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API QnA Maker. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

---

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez un fichier Python nommé `quickstart-file.py` et importez les bibliothèques suivantes.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

> [!IMPORTANT]
> Accédez au portail Azure, puis recherchez la clé et le point de terminaison de la ressource QnA Maker que vous avez créés à l’étape des prérequis. Ces informations se trouvent dans la page sur **la clé et le point de terminaison** de la ressource, sous **gestion des ressources**.

- Créez des variables d’environnement nommées QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT et QNA_MAKER_RUNTIME_ENDPOINT pour stocker ces valeurs.
- Le format de la valeur de QNA_MAKER_ENDPOINT est `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Le format de la valeur de QNA_MAKER_RUNTIME_ENDPOINT est `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
- Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

> [!IMPORTANT]
> Accédez au portail Azure, puis recherchez la clé et le point de terminaison de la ressource QnA Maker que vous avez créés à l’étape des prérequis. Ces informations se trouvent dans la page sur **la clé et le point de terminaison** de la ressource, sous **gestion des ressources**.

- Créez des variables d’environnement nommées QNA_MAKER_SUBSCRIPTION_KEY et QNA_MAKER_ENDPOINT pour stocker ces valeurs.
- Le format de la valeur de QNA_MAKER_ENDPOINT est `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Modèles objet

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) utilise deux modèles objet différents :
* **[QnAMakerClient](#qnamakerclient-object-model)** est l’objet utilisé pour créer, gérer, publier et télécharger la base de connaissances.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** est l’objet utilisé pour interroger la base de connaissances avec l’API GenerateAnswer et envoyer de nouvelles questions suggérées avec l’API Train (dans le cadre de l’[apprentissage actif](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) utilise le modèle objet suivant :
* **[QnAMakerClient](#qnamakerclient-object-model)** est l’objet utilisé pour créer, gérer, publier, télécharger et interroger la base de connaissances.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modèle objet QnAMakerClient

Le client QnA Maker de création est un objet [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python) qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient votre clé.

Une fois le client créé, utilisez la propriété [Knowledge base](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) pour créer, gérer et publier votre base de connaissances.

Gérez votre base de connaissances en envoyant un objet JSON. Pour les opérations immédiates, une méthode retourne généralement un objet JSON indiquant l’état. Pour les opérations de longue durée, la réponse est l’ID d’opération. Appelez la méthode [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#get-details-kb-id--custom-headers-none--raw-false----operation-config-) avec l’ID d’opération pour déterminer l’[état de la requête](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python).

### <a name="qnamakerruntimeclient-object-model"></a>Modèle objet QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

Le client QnA Maker de prédiction est un objet `QnAMakerRuntimeClient` qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient la clé du runtime de prédiction retournée par l’appel du client de création, [client.EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-), après la publication de la base de connaissances.

Utilisez la méthode `generate_answer` pour obtenir une réponse du runtime de requête.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

Une ressource managée QnA Maker ne nécessite pas d’utiliser l’objet QnAMakerRuntimeClient. Vous appelez plutôt [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) directement sur l’objet [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python).

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifier le client pour la création de la base de connaissances

Instanciez un client avec votre point de terminaison et la clé. Créez un objet CognitiveServicesCredentials avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Utilisez l’objet client pour obtenir un objet [knowledge base operations](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python).

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python).
    * Pour utiliser les métadonnées et les invites de suivi, utilisez le contexte éditorial, car cette donnée est ajoutée au niveau de chaque paire question/réponse.
* Pour les **fichiers**, utilisez l'objet [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python). FileDTO contient le nom de fichier ainsi que l’URL publique d’accès au fichier.
* Pour les **URL**, utilisez une liste de chaînes pour représenter les URL disponibles publiquement.

Appelez la méthode [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-), puis passer l’ID d’opération retourné à la méthode [Operations.getDetails](#get-status-of-an-operation) pour interroger l’état.

La dernière ligne du code suivant retourne l’ID de la base de connaissances à partir de la réponse de MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

Veillez à inclure la fonction [`_monitor_operation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de créer une base de connaissances.

## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en passant l’ID de celle-ci et un [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) contenant des objets DTO [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) et [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) à la méthode [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python). Utilisez la méthode [Operation.getDetail](#get-status-of-an-operation) pour déterminer si la mise à jour a réussi.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Veillez à inclure la fonction [`_monitor_operation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de mettre à jour une base de connaissances.

## <a name="download-a-knowledge-base"></a>Télécharger une base de connaissances

Utilisez la méthode [download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) pour télécharger la base de données sous la forme d’une liste de [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Ceci n'est _pas_ équivalent à l’exportation à partir de la page **Paramètres** du portail QnA Maker, car le résultat de cette méthode n’est pas un fichier TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur un point de terminaison.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Interroger une base de connaissances

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Obtenir la clé du runtime de requête

Une fois que la base de connaissances a été publiée, vous avez besoin de la clé du runtime de requête pour interroger le runtime. Il ne s’agit pas de la même clé que celle utilisée pour créer l’objet client d’origine.

Utilisez la méthode [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) pour obtenir la classe [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python).

Utilisez l’une des propriétés de clé retournées dans l’objet pour interroger la base de connaissances.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Authentifier le runtime pour générer une réponse

Créez un [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) pour interroger la base de connaissances afin de générer une réponse ou d’effectuer un entraînement par le biais de l’apprentissage actif.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Utilisez QnAMakerRuntimeClient pour obtenir une réponse de la base de connaissances ou pour envoyer de nouvelles questions suggérées à la base de connaissances en vue de l’[apprentissage actif](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Générer une réponse à partir de la base de connaissances

Générez une réponse à partir d’une base de connaissances publiée à l’aide de la méthode QnAMakerRuntimeClient.runtime.generate_answer. Cette méthode accepte l’ID de la base de connaissances et [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python). Accédez à des propriétés supplémentaires de QueryDTO comme Top et Context, que vous pouvez utiliser dans votre chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Générer une réponse à partir de la base de connaissances

Générez une réponse à partir d’une base de connaissances publiée en utilisant la méthode [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-). Cette méthode accepte l’ID de la base de connaissances et [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python). Accédez à des propriétés supplémentaires de QueryDTO comme Top et Context, que vous pouvez utiliser dans votre chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Il s’agit d’un exemple simple d’interrogation de la base de connaissances. Pour comprendre les scénarios d’interrogation avancés, consultez ces [autres exemples de requêtes](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) avec comme paramètre l’ID de la base de connaissances.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python) est retournée. Utilisez l’ID de l’opération pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine.

L’appel _setTimeout_ dans le bloc de code suivant est utilisé pour simuler le code asynchrone. Remplacez cela par la logique de nouvelle tentative.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande python de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/version-1)

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/version-2)

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).

---