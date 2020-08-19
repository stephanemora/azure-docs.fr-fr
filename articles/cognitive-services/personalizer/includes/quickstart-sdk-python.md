---
title: Fichier include
description: Fichier include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: aab4a59a35b098589adb462f2f0d6385802a9875
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246345"
---
[Documentation de référence](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Package (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Utilisation de ce guide de démarrage rapide


Plusieurs étapes sont nécessaires pour utiliser ce guide de démarrage rapide :

* Dans le portail Azure, créer une ressource Personalizer
* Sur le portail Azure, pour la ressource Personalizer, dans la page **Configuration**, définissez la fréquence de mise à jour des modèles sur un intervalle très court.
* Dans un éditeur de code, créer un fichier de code et le modifier
* À partir de la ligne de commande ou du terminal, installer le SDK
* À partir de la ligne de commande ou du terminal, exécuter le fichier de code

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Installer la bibliothèque Python pour Personalizer

Installez la bibliothèque de client Personalizer pour Python avec la commande suivante :

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Modèle objet

Le client Personalizer est un objet [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient votre clé.

Pour demander l’élément de contenu le mieux adapté, créez un [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python), puis passez-le à la méthode client.Rank. La méthode Rank retourne un RankResponse.

Pour envoyer un score de récompense à Personalizer, définissez l’ID d’événement et le score de récompense (valeur) à envoyer à la méthode [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) dans la classe EventOperations.

Dans le cadre de ce guide de démarrage rapide, il n’est pas important de déterminer la récompense. Dans un système de production, déterminer les éléments ayant un impact sur le [score de récompense](../concept-rewards.md) (et dans quelle mesure) peut être un processus complexe, que vous pouvez décider de modifier au fil du temps. Il doit s’agir de l’une des principales décisions de conception à prendre pour votre architecture Personalizer.

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client Personalizer pour Python :

* [Créer un client Personalizer](#create-a-personalizer-client)
* [API Rank](#request-the-best-action)
* [API Reward](#send-a-reward)

## <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python dans votre éditeur ou IDE favori et nommez-la `sample.py`.

## <a name="add-the-dependencies"></a>Ajouter les dépendances

À partir du répertoire de projet, ouvrez le fichier **sample.py** dans votre éditeur ou votre IDE favori. Ajoutez ce qui suit :

[!code-python[Add module dependencies](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Ajouter des informations sur la ressource Personalizer

Modifiez les variables de clé et de point de terminaison en haut du fichier de code selon la clé et le point de terminaison Azure de votre ressource. 

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Créer un client Personalizer

Ensuite, créez une méthode pour retourner un client Personalizer. Le paramètre de la méthode est `PERSONALIZER_RESOURCE_ENDPOINT` et l’ApiKey est `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Représenter les choix de contenu sous forme d’actions

Les actions représentent les choix de contenu parmi lesquels Personalizer doit sélectionner l’élément de contenu le mieux adapté. Ajoutez les méthodes suivantes à la classe Program pour représenter l’ensemble d’actions et leurs caractéristiques.

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Créer la boucle d’apprentissage

La boucle d’apprentissage Personalizer est un cycle d’appels aux API [Rank](#request-the-best-action) et [Reward](#send-a-reward). Dans ce guide de démarrage rapide, chaque appel Rank en vue de personnaliser le contenu est suivi d’un appel Reward qui indique à Personalizer l’efficacité du service.

Le code ci-après applique, en boucle, le cycle suivant : il demande à l’utilisateur d’indiquer ses préférences à partir de la ligne de commande, il envoie ces informations à Personalizer en vue de sélectionner le contenu le mieux adapté, il présente la sélection au client qui peut faire son choix dans la liste, puis il envoie une récompense à Personalizer, en indiquant l’efficacité du service concernant le choix du contenu.

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=mainLoop&highlight=9,10,29)]

Ajoutez les méthodes suivantes, qui [obtiennent les choix de contenu](#get-content-choices-represented-as-actions) avant d’exécuter le fichier de code :

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Demander l’action la mieux adaptée


Pour traiter la requête Rank, le programme demande les préférences de l’utilisateur afin de créer un `currentContent` avec les choix de contenu. Le processus peut créer du contenu à exclure des actions (`excludeActions`). Pour recevoir la réponse, la requête Rank a besoin des actions et de leurs caractéristiques, des caractéristiques currentContext, des excludeActions et d’un ID d’événement unique.

Ce guide de démarrage rapide utilise des caractéristiques de contexte simples basées sur l’heure de la journée et les préférences alimentaires de l’utilisateur. Dans les systèmes de production, il peut être important de déterminer et d’[évaluer](../concept-feature-evaluation.md) les [actions et caractéristiques](../concepts-features.md).

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=rank)]

## <a name="send-a-reward"></a>Envoyer une récompense


Pour obtenir le score de récompense à envoyer dans la requête Reward, le programme récupère la sélection de l’utilisateur à partir de la ligne de commande, attribue une valeur numérique à la sélection, puis envoie à l’API Reward l’ID d’événement unique et le score de récompense sous la forme d’une valeur numérique.

Dans le cadre de ce guide de démarrage rapide, un simple numéro est attribué en tant que score de récompense : 0 ou 1. Dans les systèmes de production, il peut être important de déterminer ce qui doit être envoyé à l’appel [Reward](../concept-rewards.md) (et à quel moment) selon vos besoins.

[!code-python[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Exécuter le programme

Exécutez l’application avec Python à partir de votre répertoire d’application.

```console
python sample.py
```

![Ce programme de démarrage rapide pose quelques questions pour recueillir les préférences de l’utilisateur, appelées « caractéristiques », puis fournit l’action classée en premier.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)