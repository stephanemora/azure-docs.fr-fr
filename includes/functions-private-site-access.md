---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 5e0cff7bde6e80a776d694820ca7b69dafa7c0d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83648821"
---
L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple, à partir d’un réseau virtuel Azure.

* L’accès aux sites privés est disponible dans les plans [Premium](../articles/azure-functions/functions-premium-plan.md), [Consommation](../articles/azure-functions/functions-scale.md#consumption-plan) et [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) quand des points de terminaison de service sont configurés.
    * Les points de terminaison de service peuvent être configurés pour chaque application, sous **Fonctionnalités de la plateforme** > **Mise en réseau** > **Configurer des restrictions d’accès** > **Ajouter une règle**. Les réseaux virtuels peuvent maintenant être sélectionnés comme un type de règle.
    * Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../articles/virtual-network/virtual-network-service-endpoints-overview.md).
    * N'oubliez pas qu'avec les points de terminaison de service, votre fonction dispose toujours d'un accès sortant complet à Internet, même si l'intégration au réseau virtuel est configurée.
* L'accès aux sites privés est également disponible via une instance d'Azure App Service Environment configurée avec un équilibreur de charge interne (ILB). Pour plus d’informations, consultez [Créer et utiliser un équilibreur de charge interne avec un Azure App Service Environment](../articles/app-service/environment/create-ilb-ase.md).

Pour apprendre à configurer l’accès privé aux site, consultez [Établir l’accès privé aux sites avec Azure Functions](../articles/azure-functions/functions-create-private-site-access.md).