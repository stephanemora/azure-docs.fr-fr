---
title: Passerelle d’application redondante interzone et avec mise à l’échelle automatique dans Azure (préversion publique)
description: Cet article présente la référence SKU Azure Application v2, qui inclut des fonctionnalités de mise à l’échelle automatique et de redondance interzone.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/31/2019
ms.author: victorh
ms.openlocfilehash: 0c8a600342e0240d435999b1b5ddabc0234c142f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461438"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Passerelle d’application redondante interzone et avec mise à l’échelle automatique (préversion publique)

Application Gateway et le pare-feu d’applications web (WAF) sont désormais disponibles en préversion publique sous une nouvelle référence SKU v2 qui offre des améliorations des performances et ajoute la prise en charge de nouvelles fonctionnalités critiques telles que la mise à l’échelle automatique, la redondance de zone et la prise en charge des adresses IP virtuelles statiques. Les fonctionnalités existantes sous la référence SKU à disponibilité générale continuent à être prises en charge dans la nouvelle référence SKU v2, à quelques exceptions près que vous trouverez dans la section relative aux limitations connues. Les nouvelles références SKU v2 incluent les améliorations suivantes :

- **Mise à l’échelle automatique** : les déploiements d’Application Gateway ou du WAF sous la référence SKU de la mise à l’échelle automatique peuvent augmenter ou diminuer en fonction de l’évolution des modèles de charge du trafic. La mise à l’échelle automatique vous évite aussi d’avoir à choisir une taille de déploiement ou un nombre d’instances au moment du provisionnement. Ainsi, la référence SKU offre une véritable élasticité. Dans la nouvelle référence SKU, Application Gateway peut fonctionner à la fois en mode de capacité fixe (mise à l’échelle automatique désactivée) et de mise à l’échelle automatique. Le mode de capacité fixe est utile pour les scénarios avec des charges de travail cohérentes et prévisibles. Le mode de mise à l’échelle automatique est utile dans les applications qui connaissent un trafic très variable.

   > [!NOTE]
   > La mise à l’échelle automatique n’est pas disponible actuellement pour la référence SKU du WAF. Configurez le WAF en mode de capacité fixe, plutôt qu’en mode de mise à l’échelle automatique.
- **Redondance de zone** : un déploiement d’Application Gateway ou de WAF peut désormais couvrir plusieurs zones de disponibilité, ce qui évite d’avoir à provisionner et épingler plusieurs instances d’Application Gateway dans chaque zone à l’aide d’une instance Traffic Manager. Vous pouvez choisir une ou plusieurs zones où les instances d’Application Gateway sont déployées, assurant ainsi une résilience en cas d’échec de la zone. Le pool principal pour les applications peut être distribué de la même façon entre les différentes zones de disponibilité.
- **Amélioration des performances** : la référence SKU de la mise à l’échelle automatique offre jusqu’à 5 fois plus de déchargement SSL que la référence SKU en disponibilité générale.
- **Déploiement et mise à jour plus rapides** La référence SKU de la mise à l’échelle automatique fournit un déploiement et une mise à jour plus rapides que la référence SKU en disponibilité générale.
- **Adresse IP virtuelle statique** : l’adresse IP virtuelle de la passerelle d’application prend désormais exclusivement en charge le type d’adresse IP virtuelle statique. Cela garantit que l’adresse IP virtuelle associée à la passerelle d’application ne change pas même après un redémarrage.

> [!IMPORTANT]
> La référence SKU de la passerelle d’application redondante interzone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Régions prises en charge

La référence SKU de mise à l’échelle automatique est disponible dans les régions suivantes : eastus2, westus2, westeurope, southeastasia, centralus, francecentral, eastus, japaneast, northeurope, southcentralus, ukwest, northcentralus, westus, eastus (BL), centralus (DM), japanwest (OS).

## <a name="pricing"></a>Tarifs

Pendant la préversion, aucuns frais ne s’appliquent. Vous êtes facturé pour les ressources autres que la passerelle d’application, telles que le coffre de clés, les machines virtuelles, etc.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

|Problème|Détails|
|--|--|
|Certificat d’authentification|Non pris en charge.<br>Pour plus d’informations, consultez [Présentation du chiffrement SSL de bout en bout sur la passerelle Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinaison de Standard_v2 et Standard Application Gateway sur le même sous-réseau|Non pris en charge.<br>De plus, si la mise à l’échelle automatique est activée, un sous-réseau ne peut avoir qu’une seule passerelle d’application.|
|Route définie par l’utilisateur sur le sous-réseau d’Application Gateway|Non pris en charge|
|Groupe de sécurité réseau pour plage de ports entrants| - 65 200 à 65 535 pour référence (SKU) Standard_v2<br>- 65 503 à 65 534 pour référence (SKU) Standard<br>Pour plus d’informations, visitez le [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Journaux de performances dans les diagnostics Azure|Non pris en charge.<br>Les métriques Azure doivent être utilisées.|
|Facturation|Aucune facturation.|
|Mode FIPS, WebSocket|Ils ne sont pas pris en charge.|
|Mode ILB uniquement|Non pris en charge actuellement. Les modes public et ILB sont pris en charge ensemble.|
|Mise à l’échelle automatique du pare-feu d’application web|Le WAF ne prend pas en charge le mode de mise à l’échelle automatique. Le mode de capacité fixe est pris en charge.|
|Intégration de Network Watcher|Non prise en charge dans la Préversion publique.|

## <a name="next-steps"></a>Étapes suivantes
- [Créer une passerelle d’application redondante dans une zone, avec mise à l’échelle automatique et avec une adresse IP virtuelle réservée à l’aide d’Azure PowerShell](tutorial-autoscale-ps.md)
- Découvrez [Application Gateway](overview.md).
- Découvrez le [Pare-feu Azure](../firewall/overview.md).