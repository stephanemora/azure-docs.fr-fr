---
title: 'Démarrage rapide : Project URL Preview, Python'
titlesuffix: Azure Cognitive Services
description: Exemple de script pour la prise en main rapide en utilisant Project URL Preview avec Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: e9b145b0786b2ab59552e5dfda6f0db63677443e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706880"
---
# <a name="quickstart-url-preview-with-python"></a>Démarrage rapide : URL Preview avec Python

L’exemple Python suivant crée un aperçu d’Url pour le site web de SwiftKey : https://swiftkey.com/en.

## <a name="prerequisites"></a>Prérequis

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

Cet exemple utilise Python 3.6.

## <a name="code-scenario"></a>Scénario de code 

Le code suivant crée un aperçu d’URL.
Il est implémenté lors des étapes suivantes :
1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez l’URL de requête pour afficher un aperçu et ajoutez le paramètre de requête.  
3. Définissez le paramètre de requête.
4. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête *Ocp-Apim-Subscription-Key*.
5. Définissez l’en-tête *Ocp-Apim-Subscription-Key*. 
6. Établissez la connexion et envoyez la requête.
7. Imprimez les résultats JSON.

Voici le code complet pour cette démonstration :

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](csharp.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide JavaScript](javascript.md)
- [Démarrage d’URL Node](node-quickstart.md)
