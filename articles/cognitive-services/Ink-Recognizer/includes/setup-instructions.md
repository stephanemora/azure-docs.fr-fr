---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996899"
---
>[!NOTE]
> Les points de terminaison pour les ressources créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Ink Recognizer à l’aide du [portail Azure](../../cognitive-services-apis-create-account.md). 

* Vous pouvez aussi obtenir une [clé pour un essai gratuit](https://azure.microsoft.com/try/cognitive-services/#decision) valide pendant sept jours. Une fois l’inscription terminée, la clé et un point de terminaison seront disponibles sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).

Après avoir créé une ressource, obtenez votre point de terminaison et votre clé en ouvrant votre ressource dans le [portail Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) puis en cliquant sur **Démarrage rapide**.

Créez deux [variables d’environnement](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) :

* `INK_RECOGNITION_SUBSCRIPTION_KEY` : le point de terminaison de votre ressource. Il se présente comme suit : <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` : la clé d’abonnement pour l’authentification de vos requêtes.   
