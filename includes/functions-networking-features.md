---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936756"
---


| Fonctionnalité |[Plan Consommation](../articles/azure-functions/consumption-plan.md)|[Plan Premium](../articles/azure-functions/functions-premium-plan.md)|[Plan dédié](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Restrictions d’adresses IP entrantes et accès aux sites privés](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Oui|✅Oui|✅Oui|✅Oui|✅Oui|
|[Intégration du réseau virtuel](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Non|✅Oui (Zones géographiques)|✅Oui (Zones géographiques et Passerelle)|✅Oui| ✅Oui|
|[Déclencheurs de réseau virtuel (non HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Non| ✅Oui |✅Oui|✅Oui|✅Oui|
|[Connexions hybrides](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (Windows uniquement)|❌Non|✅Oui|✅Oui|✅Oui|✅Oui|
|[Restrictions d’adresse IP sortantes](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Non| ✅Oui|✅Oui|✅Oui|✅Oui|