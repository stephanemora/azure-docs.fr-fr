---
title: 'Démarrage rapide : Bibliothèque de client QnA Maker pour Java'
description: Ce guide de démarrage rapide montre comment bien démarrer avec la bibliothèque de client QnA Maker pour Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6149e6668fe1330e7115d9fff1315091c878ca0c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071245"
---
Utilisez la bibliothèque de client QnA Maker pour Java afin de :

* Créer une base de connaissances
* Mettre à jour une base de connaissances
* Publier une base de connaissances
* Obtenir la clé de point de terminaison du runtime de prédiction
* Attendre l’exécution d’une tâche de longue durée
* Télécharger une base de connaissances
* Obtenir une réponse d’une base de connaissances
* Supprimer une base de connaissances

[Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Package](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Une fois en possession de votre abonnement Azure, créez une [ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) sur le portail Azure pour obtenir vos clé et point de terminaison de création. À la fin du déploiement, sélectionnez **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API QnA Maker. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-libraries"></a>Installer les bibliothèques de client

Après avoir installé Java, vous pouvez installer les bibliothèques de client en utilisant [Maven](https://maven.apache.org/) depuis le [dépôt MVN](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker).

### <a name="create-a-new-java-application"></a>Créer une application Java

Créez un fichier nommé `quickstart.java` et importez les bibliothèques suivantes.

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

- Nous utilisons une clé d’abonnement et une clé de création de manière interchangeable. Pour plus d’informations sur la clé de création, consultez [Clés dans QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- Le format de la valeur de QNA_MAKER_ENDPOINT est `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Accédez au portail Azure, puis recherchez la ressource QnA Maker que vous avez créée à l’étape des prérequis. Sélectionnez la page **Clés et point de terminaison** sous **Gestion des ressources** pour localiser la clé de création (abonnement) et le point de terminaison QnA Maker.

 ![Point de terminaison de création QnA Maker](../media/keys-endpoint.png)

- Le format de la valeur de QNA_MAKER_RUNTIME_ENDPOINT est `https://YOUR-RESOURCE-NAME.azurewebsites.net`. Accédez au portail Azure, puis recherchez la ressource QnA Maker que vous avez créée à l’étape des prérequis. Sélectionnez la page **Exporter le modèle** sous **Automation** pour localiser le point de terminaison du runtime.

 ![Point de terminaison du runtine QnA Maker](../media/runtime-endpoint.png)
   
- Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

## <a name="object-models"></a>Modèles objet

QnA Maker utilise deux modèles objet différents :
* **[QnAMakerClient](#qnamakerclient-object-model)** est l’objet utilisé pour créer, gérer, publier et télécharger la base de connaissances.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** est l’objet utilisé pour interroger la base de connaissances avec l’API GenerateAnswer et envoyer de nouvelles questions suggérées avec l’API Train (dans le cadre de l’[apprentissage actif](../how-to/use-active-learning.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modèle objet QnAMakerClient

Le client QnA Maker de création est un objet [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) qui s’authentifie auprès d’Azure en utilisant MsRest::ServiceClientCredentials, qui contient votre clé.

Une fois le client créé, utilisez les méthodes de la propriété [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) du client pour créer, gérer et publier votre base de connaissances.

Pour les opérations immédiates, une méthode retourne généralement le résultat, le cas échéant. Pour les opérations de longue durée, la réponse est un objet [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java). Appelez la méthode [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) avec la valeur `operation.operationId` pour déterminer l’[état de la requête](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java).

### <a name="qnamakerruntimeclient-object-model"></a>Modèle objet QnAMakerRuntimeClient

Le client QnA Maker d’exécution est un objet [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java).

Une fois que vous avez publié votre base de connaissances en utilisant le client d’exécution, utilisez la méthode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) du client d’exécution pour recevoir une réponse de la base de connaissances.

Vous créez un client d’exécution en appelant [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) et en passant une clé de point de terminaison d’exécution. Pour obtenir la clé du point de terminaison d’exécution, utilisez le client de création pour appeler [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30).

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifier le client pour la création de la base de connaissances

Instanciez un client avec votre point de terminaison de création et votre clé d’abonnement.

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java).
    * Pour utiliser les métadonnées et les invites de suivi, utilisez le contexte éditorial, car cette donnée est ajoutée au niveau de chaque paire question/réponse.
* Pour les **fichiers**, utilisez l'objet [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java). FileDTO contient le nom de fichier et l’URL publique d’accès au fichier.
* Pour les **URL**, utilisez une liste de chaînes pour représenter les URL disponibles publiquement.

Appelez la méthode [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173), puis passez la propriété `operationId` de l’opération retournée à la méthode [getDetails](#get-status-of-an-operation) pour interroger l’état.

La dernière ligne du code suivant retourne l’ID de la base de connaissances.

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en appelant [update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150), et en passant l’ID de la base de connaissances et un objet [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java). Cet objet peut à son tour contenir :
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Passez la propriété `operationId` de l’opération retournée à la méthode [getDetails](#get-status-of-an-operation) pour interroger l’état.

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

## <a name="download-a-knowledge-base"></a>Télécharger une base de connaissances

Utilisez la méthode [download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) pour télécharger la base de données sous la forme d’une liste de [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java). Ceci n'est _pas_ équivalent à l’exportation à partir de la page **Paramètres** du portail QnA Maker, car le résultat de cette méthode n’est pas un fichier TSV.

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur un point de terminaison.

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Générer une réponse à partir de la base de connaissances

Une fois qu’une base de connaissances a été publiée, vous avez besoin de la clé du point de terminaison d’exécution pour interroger la base de connaissances. Ce n’est pas la même clé que la clé d’abonnement utilisée pour créer le client de création.

Utilisez la méthode [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) pour obtenir un objet [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java).

Créez un client d’exécution en appelant [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) et en passant une clé de point de terminaison d’exécution provenant de l’objet EndpointKeysDTO.

Générez une réponse d’une base de connaissances publiée en utilisant la méthode [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36). Cette méthode accepte l’ID de la base de connaissances et un objet [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

Il s’agit d’un exemple simple d’interrogation d’une base de connaissances. Pour comprendre les scénarios d’interrogation avancés, consultez ces [autres exemples de requêtes](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) avec comme paramètre l’ID de la base de connaissances.

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) est retournée. Utilisez l’ID de l’opération pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine.

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

## <a name="run-the-application"></a>Exécution de l'application

Voici la méthode principale pour l’application.

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

Exécutez l’application comme suit. Ceci suppose que le nom de votre classe est `Quickstart` et que vos dépendances se trouvent dans un sous-dossier nommé `lib` sous le dossier actif.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).
