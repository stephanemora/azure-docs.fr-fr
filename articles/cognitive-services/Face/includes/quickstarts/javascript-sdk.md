---
title: Démarrage rapide de la bibliothèque de client JavaScript Visage
description: Utilisez la bibliothèque de client Visage pour JavaScript afin de détecter des visages, rechercher des visages semblables (recherche faciale par image), identifier des visages (recherche avec reconnaissance faciale) et migrer vos données de visage.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: b4a63f76cbcd9e98295f5edcf7ff2d06979e6556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244781"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Démarrage rapide : Bibliothèque de client Visage pour JavaScript

Commencez à utiliser la reconnaissance faciale avec la bibliothèque de client Visage pour JavaScript. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service Visage vous donne accès à des algorithmes avancés pour la détection et la reconnaissance des visages dans des images.

Utilisez la bibliothèque de client Visage pour JavaScript afin d’effectuer les opérations suivantes :

* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer un groupe de personnes](#create-a-person-group)
* [Identifier un visage](#identify-a-face)

[Documentation de référence](/javascript/api/@azure/cognitiveservices-face/) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [Exemples](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* Dernière version de [Node.js](https://nodejs.org/en/)
* Une fois que vous avez votre abonnement Azure, [créez une ressource Visage](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API Visage. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client 

Installez les packages NPM `ms-rest-azure` et `azure-cognitiveservices-face` :

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

Créez un fichier nommé `index.js` et importez les bibliothèques suivantes :

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. 

> [!IMPORTANT]
> Accédez au portail Azure. Si la ressource Visage que vous avez créée dans la section **Prérequis** a été déployée correctement, cliquez sur le bouton **Accéder à la ressource** sous **Étapes suivantes**. La clé et le point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. 
>
> N’oubliez pas de supprimer la clé de votre code une fois que vous avez terminé, et ne la postez jamais publiquement. Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Pour plus d’informations, consultez l’article sur la [sécurité](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) de Cognitive Services.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client .NET Visage :

|Nom|Description|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Cette classe représente votre autorisation d’utiliser le service Visage. Vous en avez besoin pour toutes les fonctionnalités de Visage. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes. |
|[Visage](/javascript/api/@azure/cognitiveservices-face/face)|Cette classe gère les tâches de détection et de reconnaissance de base que vous pouvez effectuer avec les visages. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Cette classe représente toutes les données détectées à partir d’un visage unique dans une image. Vous pouvez l’utiliser pour récupérer des informations détaillées sur le visage.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Cette classe gère les constructions **FaceList** stockées dans le cloud, comprenant un ensemble de visages assortis. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Cette classe gère les constructions **Person** stockées dans le cloud, comprenant un ensemble de visages appartenant à une même personne.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Cette classe gère les constructions **PersonGroup** stockées dans le cloud, comprenant un ensemble d’objets **Person** assortis. |

## <a name="code-examples"></a>Exemples de code

Les extraits de code ci-dessous vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Visage pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer un groupe de personnes](#create-a-person-group)
* [Identifier un visage](#identify-a-face)

> [!TIP]
> Vous voulez voir l’intégralité du fichier de code de démarrage rapide à la fois ? Vous le trouverez sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), qui contient les exemples de code utilisés dans ce guide de démarrage rapide.

## <a name="authenticate-the-client"></a>Authentifier le client

Instanciez un client avec votre point de terminaison et la clé. Créez un objet **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials)** avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient)** .

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Déclarer les valeurs globales et la fonction d’assistance

Les valeurs globales suivantes sont nécessaires pour plusieurs des opérations Visage que vous ajouterez ultérieurement.

L’URL pointe vers un dossier d’exemples d’images. L’UUID sert à la fois de nom et d’ID pour le PersonGroup que vous allez créer.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Vous allez utiliser la fonction suivante pour attendre la fin de l’entraînement du PersonGroup.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

### <a name="get-detected-face-objects"></a>Recevoir les objets de visage détectés

Créez une méthode pour détecter les visages. La méthode `DetectFaceExtract` traite trois des images à l’URL donnée, puis crée une liste d’objets **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** dans la mémoire du programme. La liste des valeurs **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** spécifie les fonctionnalités à extraire. 

Ensuite, la méthode `DetectFaceExtract` analyse et imprime les données d’attribut pour chaque visage détecté. Chaque attribut doit être spécifié séparément dans l’appel initial de l’API de détection des visages (dans la liste **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** ). Le code suivant traite chaque attribut, mais vous n’aurez probablement besoin d’en utiliser qu’un ou plusieurs.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Vous pouvez également détecter les visages dans une image locale. Consultez les méthodes [Visage](/javascript/api/@azure/cognitiveservices-face/face) comme [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Rechercher des visages semblables

Le code suivant utilise un visage unique détecté (la source) et recherche un ensemble d’autres visages (la cible) pour trouver des correspondances (recherche faciale par image). Quand il trouve une correspondance, il affiche l’ID du visage correspondant sur la console.

### <a name="detect-faces-for-comparison"></a>Détecter des visages pour les comparer

Tout d’abord, définissez une deuxième méthode de détection de visage. Vous devez détecter des visages dans des images avant de pouvoir les comparer. Cette méthode de détection est optimisée pour les opérations de comparaison. Elle n’extrait pas les attributs de visage détaillés comme dans la section ci-dessus, et utilise un modèle de reconnaissance différent.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Rechercher des correspondances

La méthode suivante détecte des visages dans un ensemble d’images cibles et dans une image source unique. Elle les compare ensuite et recherche toutes les images cibles semblables à l’image source. Enfin, elle affiche les détails des correspondances dans la console.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identifier un visage

L’opération [Identify](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) prend une image d’une personne (ou de plusieurs personnes) et recherche l’identité de chaque visage dans l’image (recherche avec reconnaissance faciale). Il compare chaque visage détecté à un [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup), une base de données comprenant différents objets [Person](/javascript/api/@azure/cognitiveservices-face/person) dont les caractéristiques du visage sont connues. Pour effectuer l’opération d’identification, vous devez d’abord créer et entraîner un [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

### <a name="add-faces-to-person-group"></a>Ajouter des visages au groupe de personnes

Créez la fonction suivante pour ajouter des visages au [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Attendre l’entraînement du groupe de personnes

Créez la fonction d’assistance suivante pour attendre la fin de l’entraînement du groupe de personnes.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Créer un groupe de personnes

Le code suivant :
- Crée un [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).
- Ajoute des visages au groupe de personnes en appelant `AddFacesToPersonGroup`, que vous avez défini précédemment.
- Entraîne le groupe de personnes.
- Identifie les visages dans le groupe de personnes.

Ce groupe **Person** et ses objets **Person** associés sont maintenant prêts à être utilisés dans les opérations de vérification, d’identification ou de groupe.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Vous pouvez également créer un **PersonGroup** à partir d’images locales. Consultez les méthodes [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) comme [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Principal

Enfin, créez la fonction `main` et appelez-la.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque de client Visage pour JavaScript afin d’effectuer des tâches de reconnaissance faciale simples. Pour plus d’informations sur la bibliothèque, reportez-vous à la documentation de référence.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Visage (JavaScript)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Qu’est ce que le service Visage ?](../../overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
