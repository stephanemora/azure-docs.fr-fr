---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378570"
---
>[!NOTE]
> Les points de terminaison pour les ressources créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Ink Recognizer à l’aide du [portail Azure](../../cognitive-services-apis-create-account.md).

Après avoir créé une ressource, obtenez votre point de terminaison et votre clé en ouvrant votre ressource dans le [portail Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) puis en cliquant sur **Démarrage rapide**.

Créez deux [variables d’environnement](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) :

* `INK_RECOGNITION_SUBSCRIPTION_KEY` : le point de terminaison de votre ressource. Il se présente comme suit : <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` : la clé d’abonnement pour l’authentification de vos requêtes.   
