---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304039"
---
>[!NOTE]
> Les points de terminaison pour les ressources créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Ink Recognizer à l’aide du [portail Azure](../../cognitive-services-apis-create-account.md).

Après avoir créé une ressource, obtenez votre point de terminaison et votre clé en ouvrant votre ressource dans le [portail Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) puis en cliquant sur **Démarrage rapide**.

Créez deux [variables d’environnement](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) :

* `INK_RECOGNITION_SUBSCRIPTION_KEY` : la clé d’abonnement pour l’authentification de vos requêtes. 

* `INK_RECOGNITION_ENDPOINT` : le point de terminaison de votre ressource. Il se présente comme suit : <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
