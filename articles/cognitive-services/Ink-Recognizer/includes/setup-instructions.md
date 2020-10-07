---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327349"
---
>[!NOTE]
> Les points de terminaison pour les ressources créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Ink Recognizer à l’aide du [portail Azure](../../cognitive-services-apis-create-account.md).

Après avoir créé une ressource, obtenez votre point de terminaison et votre clé en ouvrant votre ressource dans le [portail Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) puis en cliquant sur **Démarrage rapide**.

Créez deux [variables d’environnement](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) :

* `INK_RECOGNITION_SUBSCRIPTION_KEY` : la clé d’abonnement pour l’authentification de vos requêtes. 

* `INK_RECOGNITION_ENDPOINT` : le point de terminaison de votre ressource. Il se présente comme suit : <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
