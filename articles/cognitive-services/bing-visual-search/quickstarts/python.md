---
title: 'Démarrage rapide : Créer une requête de recherche visuelle, Python - Recherche visuelle Bing'
titleSuffix: Azure Cognitive Services
description: Montre comment charger une image dans l’API Recherche visuelle Bing pour récupérer des informations sur celle-ci.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3a0d92e42eed097e244118a60ec0a4223c9cedf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440939"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Démarrage rapide : Votre première requête Recherche visuelle Bing en Python

L’API Recherche visuelle Bing renvoie des informations sur une image que vous fournissez. Cette image est accessible via son URL, un jeton insights ou par chargement. Pour plus d’informations sur ces options, consultez la section [Qu’est-ce que l’API Recherche visuelle Bing ?](../overview.md) Cet article explique comment charger une image. Il peut se révéler utile de charger une image prise sur un appareil mobile. Par exemple, vous photographiez un monument très connu pour obtenir en retour des informations sur celui-ci. Ces informations sont parfois très variées. 

Si vous chargez une image enregistrée en local, voici les données de formulaire que vous devez inclure dans le corps du POST. Les données du formulaire doivent inclure l’en-tête Content-Disposition. Son paramètre `name` doit être défini sur « image » tandis que le paramètre `filename` peut être défini sur une chaîne quelle qu’elle soit. Le contenu du formulaire correspond au code binaire de l’image. La taille maximale de l’image que vous chargez est de 1 Mo. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Cet article inclut une application console qui envoie une requête d’API Recherche visuelle Bing et affiche les résultats de la recherche au format JSON. Alors que cette application est écrite en Python, l’API est un service web RESTful compatible avec n’importe quel langage de programmation qui peut formuler des requêtes HTTP et analyser JSON. 

## <a name="prerequisites"></a>Prérequis

Vous devez disposer de [Python 3](https://www.python.org/) pour exécuter ce code.

Pour ce guide de démarrage rapide, vous devez démarrer un abonnement au niveau tarifaire S9 comme indiqué dans [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Pour démarrer un abonnement dans le portail Azure :
1. Entrez « BingSearchV7 » dans la zone de texte en haut du portail Azure indiquant `Search resources, services, and docs`.  
2. Sous Place de marché dans la liste déroulante, sélectionnez `Bing Search v7`.
3. Entrez `Name` pour la nouvelle ressource.
4. Sélectionnez l’abonnement `Pay-As-You-Go`.
5. Sélectionnez le niveau tarifaire `S9`.
6. Cliquez sur `Enable` pour démarrer l’abonnement.

## <a name="running-the-walkthrough"></a>Exécution de la procédure pas à pas

Pour exécuter cette application, suivez les étapes ci-dessous :

1. Créez un projet Python dans votre IDE ou votre éditeur favori.
2. Créez un fichier nommé visualsearch.py et ajoutez le code indiqué dans ce démarrage rapide.
3. Remplacez la valeur `SUBSCRIPTION_KEY` par votre clé d’abonnement.
3. Remplacez la valeur `imagePath` par le chemin d’accès de l’image à charger.
4. Exécutez le programme.



La procédure suivante montre comment envoyer le message à l’aide de données de formulaire fractionnées dans Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Étapes suivantes

[Obtenir des informations sur une image à l’aide d’un jeton insights](../use-insights-token.md)  
[Tutoriel de chargement d’images pour Recherche visuelle Bing](../tutorial-visual-search-image-upload.md)
[Tutoriel de l’application à page unique Recherche visuelle Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Présentation de l’API Recherche visuelle Bing](../overview.md)  
[Essayer](https://aka.ms/bingvisualsearchtryforfree)  
[Obtenir une clé d’accès d’essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informations de référence sur l’API Recherche visuelle Bing](https://aka.ms/bingvisualsearchreferencedoc)
