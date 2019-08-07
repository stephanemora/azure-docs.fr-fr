---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334234"
---
## <a name="azure-cognitive-service-resource-types"></a>Types de ressource Azure Cognitive Services

> [!NOTE]
> Les propriétaires d’abonnements peuvent désactiver la création de ressources Cognitive Services pour des groupes de ressources et des abonnements en appliquant une [stratégie Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), en affectant la définition de stratégie « Types de ressources non autorisés » et en spécifiant **Microsoft.CognitiveServices/accounts** comme type de ressource cible.

Vous pouvez accéder à Azure Cognitive Services via deux ressources différentes : Une ressource multiservice ou une ressource monoservice. Ces abonnements vous permettent de vous connecter à un service unique ou plusieurs services à la fois.

### <a name="multi-service-resource"></a>Ressource multiservice

>[!WARNING]
> À ce stade, les services suivants **ne prennent pas en charge** les clés multiservices : QnA Maker, services Speech, Custom Vision et Détecteur d’anomalies.

Abonnement à une ressource Cognitive Services multiservice :
* Vous permet d’utiliser une même ressource Azure pour la plupart des services Azure Cognitive Services.
* Regroupe la facturation des services que vous utilisez. Pour plus d’informations, consultez les [tarifs de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

### <a name="single-service-resource"></a>Ressource monoservice

Les ressources monoservices (comme Vision par ordinateur ou les services Speech) sont limitées au service spécifié.