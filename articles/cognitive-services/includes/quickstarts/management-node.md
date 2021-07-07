---
title: 'Démarrage rapide : Bibliothèque de client de gestion Azure pour Node.js'
description: Dans ce guide de démarrage rapide, vous allez vous familiariser avec la bibliothèque de client de gestion Azure pour Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 2fd0ce34d79ac168acc87ae5b425527c256816de
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593523"
---
[Documentation de référence](/javascript/api/@azure/arm-cognitiveservices/) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Package (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>Prérequis pour JavaScript

* Un abonnement Azure valide - [Créer un abonnement gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org/)
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

Avant de continuer, créez un fichier nommé _index.js_.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM suivants :

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

### <a name="import-libraries"></a>Importer des bibliothèques

Ouvrez votre script _index.js_ et importez les bibliothèques suivantes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Authentifier le client

Ajoutez les champs suivants à la racine de votre script et complétez-les avec les valeurs appropriées à l'aide du principal de service que vous avez créé et des informations de votre compte Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Ajoutez ensuite la fonction `quickstart` suivante pour gérer le travail principal de votre programme. Le premier bloc de code génère un objet **CognitiveServicesManagementClient** à l'aide des variables d'identification que vous avez entrées ci-dessus. Cet objet est nécessaire pour toutes vos opérations de gestion Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Appeler les fonctions de gestion

Ajoutez le code suivant à la fin de votre fonction `quickstart` pour répertorier les ressources disponibles, créer un exemple de ressource, répertorier les ressources que vous possédez, puis supprimer l'exemple de ressource. Vous définirez ces fonctions au cours des étapes suivantes.

## <a name="create-a-cognitive-services-resource-nodejs"></a>Créer une ressource Cognitive Services (Node.js)

Pour créer une ressource Cognitive Services et s'y abonner, utilisez la fonction **Create**. Cette fonction ajoute une nouvelle ressource facturable au groupe de ressources que vous transmettez. Lorsque vous créez votre nouvelle ressource, vous devez connaître le « type » du service que vous souhaitez utiliser, ainsi que son niveau tarifaire (ou référence SKU) et un emplacement Azure. La fonction suivante utilise tous ces arguments et crée une ressource.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Choisir un service et son niveau tarifaire

Lorsque vous créez une nouvelle ressource, vous devez connaître le « type » du service que vous souhaitez utiliser, ainsi que le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou référence SKU) souhaité. Ces informations et d'autres vous serviront de paramètres lors de la création de la ressource. La fonction suivante répertorie les « types » de ressources Cognitive Services disponibles.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Afficher vos ressources

Pour afficher toutes les ressources disponibles sous votre compte Azure (dans tous les groupes de ressources), utilisez la fonction suivante :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Supprimer une ressource

La fonction suivante supprime la ressource spécifiée du groupe de ressources donné.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

Si vous devez récupérer une ressource supprimée, consultez [Récupérer des ressources Cognitive Services supprimées](../../manage-resources.md).

## <a name="run-the-application"></a>Exécution de l'application

Ajoutez le code suivant en bas de votre script pour appeler votre fonction `quickstart` principale avec gestion des erreurs.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Puis, dans la fenêtre de votre console, exécutez l'application à l'aide de la commande `node`.

```console
node index.js
```

## <a name="see-also"></a>Voir aussi

* Consultez **[Authentifier des requêtes auprès d’Azure Cognitive Services](../../authentication.md)** qui explique la façon de travailler en toute sécurité avec Cognitive Services.
* Consultez **[Présentation d’Azure Cognitive Services](../../what-are-cognitive-services.md)** pour obtenir la liste des différentes catégories présentes dans Cognitive Services.
* Consultez **[Prise en charge du langage naturel](../../language-support.md)** pour afficher la liste des langages naturels pris en charge par Cognitive Services.
* Consultez **[Utiliser Cognitive Services en tant que conteneurs](../../cognitive-services-container-support.md)** pour comprendre comment utiliser Cognitive Services en local.
* Consultez **[Planifier et gérer les coûts pour Cognitive Services](../../plan-manage-costs.md)** afin d’estimer le coût d’utilisation de Cognitive Services.
* Pour plus d’informations sur le kit de développement logiciel (SDK) de gestion, consultez la **[documentation de référence du kit de développement logiciel (SDK) de gestion Azure](/javascript/api/@azure/arm-cognitiveservices/)** .
