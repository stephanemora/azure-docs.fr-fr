---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.openlocfilehash: f1024edf7d05d47ba14478a1561c29f7859a245c
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111887085"
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
> Quand vous créez une ressource Cognitive Service dans le portail Azure, vous avez la possibilité de créer une clé d’abonnement multiservice (utilisée dans plusieurs Cognitive Services) ou une clé d’abonnement monoservice (utilisée avec un Cognitive Service spécifique). Actuellement, Form Recognizer n’est pas compris dans l’abonnement multiservice.

## <a name="retrieve-the-key-and-endpoint"></a>Récupérer la clé et le point de terminaison

Lorsque le déploiement de la ressource Form Recognizer se termine, recherchez-la et sélectionnez-la dans la liste **Toutes les ressources** dans le portail. Votre clé et votre point de terminaison se trouvent dans la page **Clé et point de terminaison** de la ressource, sous **Gestion des ressources**. Enregistrez ces deux éléments à un emplacement temporaire avant de continuer.

