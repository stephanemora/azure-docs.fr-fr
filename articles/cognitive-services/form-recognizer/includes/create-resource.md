---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 734b548953c4ede5e7d09feb0042509ae5528e82
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108026268"
---
Accédez au portail Azure et <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="créez une ressource Form Recognizer" target="_blank">Créer une ressource Form Recognizer </a>. Dans le volet **Créer**, indiquez les informations suivantes :

| Détails du projet   | Description   |
|--|--|
| **Abonnement** | Sélectionnez l’abonnement Azure auquel l’accès a été accordé. |
| **Groupe de ressources** | [Groupe de ressources Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) comprenant votre ressource. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |
| **Région** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
| **Nom** | Nom descriptif de votre ressource. Nous recommandons d’utiliser un nom explicite, par exemple *MyNameFormRecognizer*. |
| **Niveau tarifaire** | Le coût de la ressource dépend du niveau tarifaire que vous choisissez et de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.

> [!NOTE]
> Quand vous créez une ressource Cognitive Services dans le portail Azure, vous avez la possibilité de créer une clé d’abonnement multiservice (utilisée dans plusieurs services cognitifs) ou une clé d’abonnement monoservice (utilisée avec un service cognitif spécifique). Actuellement, Form Recognizer n’est pas compris dans l’abonnement multiservice.

Lorsque le déploiement de la ressource Form Recognizer se termine, recherchez-la et sélectionnez-la dans la liste **Toutes les ressources** dans le portail. Votre clé et votre point de terminaison se trouvent dans la page de la clé et du point de terminaison de la ressource, sous Gestion des ressources. Enregistrez ces deux éléments à un emplacement temporaire avant de continuer.
