---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 93ee37d6ef607a65f5b8be68d71c46fc258387f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096557"
---
### <a name="create-a-new-resource-from-the-azure-portal"></a>Créer une ressource à partir du portail Azure

Accédez au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) pour créer une ressource Azure Language. Si vous êtes invité à sélectionner des fonctionnalités supplémentaires, sélectionnez **Ignorer cette étape**. Quand vous créez votre ressource, vérifiez qu’elle a les paramètres suivants.  

|Condition requise  |Valeur requise  |
|---------|---------|
|Location | « USA Ouest 2 » ou « Europe Ouest »         |
|Niveau tarifaire     | Niveau tarifaire Standard (**S**)        |

> [!IMPORTANT]
> Dans la section **Reconnaissance d’entité nommée (NER) personnalisée et Classification personnalisée (préversion)** , veillez à choisir un compte de stockage existant, ou créez-en un. Un compte de stockage est requis pour utiliser la classification de texte personnalisée. Bien que vous puissiez spécifier un compte de stockage ultérieurement, il est plus facile de le faire maintenant. 
