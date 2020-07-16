---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906710"
---


| Fonctionnalité |[Plan Consommation](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plan dédié](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrictions d’adresses IP entrantes et accès aux sites privés](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Oui|✅Oui|✅Oui|✅Oui|✅Oui|
|[Intégration du réseau virtuel](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Non|✅Oui (Zones géographiques)|✅Oui (Zones géographiques et Passerelle)|✅Oui| ✅Oui|
|[Déclencheurs de réseau virtuel (non HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Non| ✅Oui |✅Oui|✅Oui|✅Oui|
|[Connexions hybrides](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (Windows uniquement)|❌Non|✅Oui|✅Oui|✅Oui|✅Oui|
|[Restrictions d’adresse IP sortantes](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Non| ✅Oui|✅Oui|✅Oui|✅Oui|