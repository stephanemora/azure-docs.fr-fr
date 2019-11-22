---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 14740e780f6143f19f843891c16b25df761d0b4f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931244"
---
Quand un accès vous autorisant à utiliser Form Recognizer vous est accordé, vous recevez un e-mail d’accueil contenant plusieurs liens et ressources. Utilisez le lien « Portail Azure » dans ce message pour ouvrir le portail Azure et créer une ressource Form Recognizer. Dans le volet **Créer**, indiquez les informations suivantes :

|    |    |
|--|--|
| **Nom** | Nom descriptif de votre ressource. Nous recommandons d’utiliser un nom explicite, par exemple *MyNameFormRecognizer*. |
| **Abonnement** | Sélectionnez l’abonnement Azure auquel l’accès a été accordé. |
| **Lieu** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
| **Niveau tarifaire** | Le coût de la ressource dépend du niveau tarifaire que vous choisissez et de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.
| **Groupe de ressources** | [Groupe de ressources Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) comprenant votre ressource. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |

> [!IMPORTANT]
> Normalement, lorsque vous créez une ressource Cognitive Services dans le portail Azure, vous avez la possibilité de créer une clé d’abonnement multiservice (utilisée dans plusieurs services cognitifs) ou une clé d’abonnement à un seul service (utilisée uniquement avec un service cognitif spécifique). Toutefois, étant donné que Form Recognizer est en préversion, il n’est pas inclus dans l’abonnement multiservice et vous ne pouvez pas créer l’abonnement à un service unique, sauf si vous utilisez le lien fourni dans l’e-mail de bienvenue.

Lorsque le déploiement de la ressource Form Recognizer se termine, recherchez-la et sélectionnez-la dans la liste **Toutes les ressources** dans le portail. Ensuite, sélectionnez l’onglet **Démarrage rapide** pour voir les données de votre abonnement. Enregistrez les valeurs de **Key1** et de **Endpoint** dans un emplacement temporaire. Vous les utiliserez dans les étapes qui suivent.