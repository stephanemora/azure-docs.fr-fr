---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996791"
---
Accédez au portail Azure et <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="créez une ressource Form Recognizer" target="_blank">Créer une ressource Form Recognizer <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Dans le volet **Créer**, indiquez les informations suivantes :

|    |    |
|--|--|
| **Nom** | Nom descriptif de votre ressource. Nous recommandons d’utiliser un nom explicite, par exemple *MyNameFormRecognizer*. |
| **Abonnement** | Sélectionnez l’abonnement Azure auquel l’accès a été accordé. |
| **Lieu** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
| **Niveau tarifaire** | Le coût de la ressource dépend du niveau tarifaire que vous choisissez et de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.
| **Groupe de ressources** | [Groupe de ressources Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) comprenant votre ressource. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |

> [!NOTE]
> Normalement, lorsque vous créez une ressource Cognitive Services dans le portail Azure, vous avez la possibilité de créer une clé d’abonnement multiservice (utilisée dans plusieurs services cognitifs) ou une clé d’abonnement à un seul service (utilisée uniquement avec un service cognitif spécifique). Toutefois, étant donné que Form Recognizer est en préversion, il n’est pas inclus dans l’abonnement multiservice et vous ne pouvez pas créer l’abonnement à un service unique, sauf si vous utilisez le lien fourni dans l’e-mail de bienvenue.

Lorsque le déploiement de la ressource Form Recognizer se termine, recherchez-la et sélectionnez-la dans la liste **Toutes les ressources** dans le portail. Votre clé et votre point de terminaison se trouvent dans la page de la clé et du point de terminaison de la ressource, sous Gestion des ressources. Enregistrez ces deux éléments à un emplacement temporaire avant de continuer.
