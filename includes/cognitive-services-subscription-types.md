---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461511"
---
## <a name="azure-cognitive-service-subscription-types"></a>Types d’abonnement Azure Cognitive Services

> [!NOTE]
> Les propriétaires d’abonnements peuvent désactiver la création de comptes Cognitive Services pour les groupes de ressources et les abonnements, en appliquant une [stratégie Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), en attribuant la définition de stratégie « Types de ressources non autorisés » et en spécifiant **Microsoft.CognitiveServices/accounts** comme type de ressource cible.

Vous pouvez accéder à Azure Cognitive Services via deux abonnements différents : Un abonnement multiservice ou un abonnement monoservice. Ces abonnements vous permettent de vous connecter à un service unique ou plusieurs services à la fois.

### <a name="multi-service-subscription"></a>Abonnement multiservice

>[!WARNING]
> À ce stade, les services suivants **ne prennent pas en charge** les clés multiservices : QnA Maker, services Speech, Custom Vision et Détecteur d’anomalies.

Un abonnement multiservice Azure Cognitive Services vous permet d’utiliser un seul abonnement et une seule ressource Azure pour la plupart des services Microsoft Cognitive Services et consolide les services de facturation que vous utilisez. Pour plus d’informations, consultez les [tarifs de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

### <a name="single-service-subscription"></a>Abonnement monoservice

Un abonnement à un seul service, comme Vision par ordinateur ou les services Speech. Un abonnement monoservice est limité à une ressource. 
