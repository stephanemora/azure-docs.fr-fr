---
title: 'Démarrage rapide : Obtenir des réponses d’une base de connaissances - REST, Python - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST Python vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 0647d850491f31d0f66de18a593ac7ff471e8906
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206406"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Obtenir des réponses à une question à partir d’une base de connaissance avec Python

Ce guide de démarrage rapide vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances QnA Maker publiée. QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, de [sources de données](../Concepts/data-sources-supported.md). La question, au format JSON, est envoyée dans le corps de la requête d’API. 

## <a name="prerequisites"></a>Prérequis

* [Python 3.6 ou ultérieur](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord Azure pour votre ressource QnA Maker. 
* Paramètres de la page **Publier**. Si vous ne disposez pas d’une base de connaissances publiée, créez une base de connaissances vide, importez une base de connaissances sur la page **Paramètres**, puis publiez. Vous pouvez télécharger et utiliser [cette base de connaissances](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Les paramètres de la page de publication incluent la valeur de route POST, la valeur d’hôte et la valeur EndpointKey. 

    ![Paramètres de publication](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Le code associé à ce guide de démarrage rapide est disponible dans le dépôt [https://github.com/Azure-Samples/cognitive-services-qnamaker-python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-python-file"></a>Créer un fichier Python

Ouvrez VSCode et créez un fichier nommé `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut du fichier `get-answer-3x.py`, ajoutez les dépendances nécessaires au projet :

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

L’hôte et la route ne s’affichent pas comme dans la page **Publier**. En effet, la bibliothèque Python n’autorise aucun routage dans l’hôte. Le routage qui apparaît sur la page **Publier** comme faisant partie de l’hôte a été déplacé vers la route.

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires

Ajoutez les constantes nécessaires pour accéder à QnA Maker. Ces valeurs se trouvent dans la page **Publier** une fois la base de connaissances publiée. 

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Ajouter une requête POST pour envoyer une question et obtenir une réponse

Le code suivant adresse une requête HTTPS à l’API QnA Maker afin d’envoyer la question à la base de connaissances et reçoit la réponse :

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

La valeur de l’en-tête `Authorization` inclut la chaîne `EndpointKey `. 

## <a name="run-the-program"></a>Exécuter le programme

Exécutez le programme à partir de la ligne de commande. Il envoie automatiquement la requête à l’API QnA Maker, puis affiche la réponse dans la fenêtre de console.

Exécutez le fichier :

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
