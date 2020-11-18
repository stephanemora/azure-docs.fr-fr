---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578826"
---


| Fonctionnalité |[Plan Consommation](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plan Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Plan dédié](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrictions d’adresses IP entrantes et accès aux sites privés](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Oui|✅Oui|✅Oui|✅Oui|✅Oui|
|[Intégration du réseau virtuel](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Non|✅Oui (Zones géographiques)|✅Oui (Zones géographiques et Passerelle)|✅Oui| ✅Oui|
|[Déclencheurs de réseau virtuel (non HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Non| ✅Oui |✅Oui|✅Oui|✅Oui|
|[Connexions hybrides](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (Windows uniquement)|❌Non|✅Oui|✅Oui|✅Oui|✅Oui|
|[Restrictions d’adresse IP sortantes](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Non| ✅Oui|✅Oui|✅Oui|✅Oui|