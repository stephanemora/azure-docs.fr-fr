---
title: Appeler un point de terminaison à l’aide de Python - Recherche personnalisée Bing - Microsoft Cognitive Services
description: Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Python pour appeler le point de terminaison Recherche personnalisée Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "35370744"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Appeler le point de terminaison Recherche personnalisée Bing (Python)

Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Python pour appeler le point de terminaison Recherche personnalisée Bing. 

## <a name="prerequisites"></a>Prérequis
Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Une instance de recherche personnalisée. Consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).

-  [Python](https://www.python.org/) (déjà installé).

- Un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. Vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) pour suivre ce guide de démarrage rapide. Vous aurez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou de la clé d’un abonnement payant présente sur votre tableau de bord Azure. 

## <a name="run-the-code"></a>Exécuter le code

Pour appeler le point de terminaison Recherche personnalisée Bing, effectuez les étapes suivantes :

1. Créez un dossier pour votre code.
2. À partir d’un terminal ou d’une invite de commandes en mode administrateur, accédez au dossier que vous venez de créer.
3. Installez le module Python **requests** :
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Créez le fichier BingCustomSearch.py et copiez-y le code ci-dessous.
8. Remplacez **YOUR-SUBSCRIPTION-KEY** et **YOUR-CUSTOM-CONFIG-ID** par votre clé et votre ID de configuration (voir l’étape 1).

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. Exécutez le code à l’aide de la commande suivante.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Étapes suivantes
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)