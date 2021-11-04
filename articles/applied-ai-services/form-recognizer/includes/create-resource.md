---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f4a863cb0c3ecf087d5efdcde37ce907a7a7d41
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021698"
---
Accédez au portail Azure et <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="créez une ressource Form Recognizer" target="_blank">Créer une ressource Form Recognizer </a>. Dans le volet **Créer**, indiquez les informations suivantes :

| Détails du projet   | Description   |
|--|--|
| **Abonnement** | Sélectionnez l’abonnement Azure auquel l’accès a été accordé. |
| **Groupe de ressources** | Groupe de ressources Azure qui contient votre ressource. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |
| **Région** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
| **Nom** | Nom descriptif de votre ressource. Nous recommandons d’utiliser un nom explicite, par exemple *MyNameFormRecognizer*. |
| **Niveau tarifaire** | Le coût de la ressource dépend du niveau tarifaire que vous choisissez et de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.

## <a name="retrieve-the-key-and-endpoint"></a>Récupérer la clé et le point de terminaison

Lorsque le déploiement de la ressource Form Recognizer se termine, recherchez-la et sélectionnez-la dans la liste **Toutes les ressources** dans le portail. Votre clé et votre point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. Enregistrez ces deux éléments à un emplacement temporaire avant de continuer.
