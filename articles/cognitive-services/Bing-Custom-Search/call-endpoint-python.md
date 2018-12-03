---
title: 'Démarrage rapide : Appel d’un point de terminaison à l’aide de Python - Recherche personnalisée Bing'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Python pour appeler le point de terminaison Recherche personnalisée Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: d7977f49a484665f133a119f491bd8986c0b6fc4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310717"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Démarrage rapide : Appel du point de terminaison Recherche personnalisée Bing (Python)

Ce guide de démarrage rapide montre comment demander les résultats de la recherche à partir de votre instance de recherche personnalisée en utilisant Python pour appeler le point de terminaison Recherche personnalisée Bing. 

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Une instance de recherche personnalisée prête à l’emploi. Consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).
- [Python](https://www.python.org/) (déjà installé).
- Une clé d’abonnement Vous pouvez obtenir une clé d’abonnement quand vous activez votre [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), ou vous pouvez utiliser une clé d’abonnement payant de votre tableau de bord Azure (voir [Compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).   Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="run-the-code"></a>Exécuter le code

Pour exécuter cet exemple, suivez ces étapes :

1. Créez un dossier pour votre code.  
  
2. À partir d’un terminal ou d’une invite de commandes en mode administrateur, accédez au dossier que vous venez de créer.  
  
3. Installez le module Python **requests** :  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Créez un fichier nommé BingCustomSearch.py dans le dossier que vous avez créé et copiez-y le code suivant. Remplacez **YOUR-SUBSCRIPTION-KEY** et **YOUR-CUSTOM-CONFIG-ID** par votre clé d’abonnement et votre ID de configuration.  
  
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
  
7. Exécutez le code à l’aide de la commande suivante.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Étapes suivantes
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)
