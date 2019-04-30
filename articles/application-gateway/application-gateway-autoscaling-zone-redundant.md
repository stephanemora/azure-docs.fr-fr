---
title: Passerelle d’application redondante interzone et avec mise à l’échelle automatique dans Azure (préversion publique)
description: Cet article présente la référence SKU Azure Application v2, qui inclut des fonctionnalités de mise à l’échelle automatique et de redondance interzone.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 95b14a0028134e522206f3595bc3b9ebf9aaf396
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128470"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Passerelle d’application redondante interzone et avec mise à l’échelle automatique (préversion publique)

Application Gateway et le pare-feu d’applications web (WAF) sont désormais disponibles en préversion publique sous une nouvelle référence SKU v2 qui offre des améliorations des performances et ajoute la prise en charge de nouvelles fonctionnalités critiques telles que la mise à l’échelle automatique, la redondance de zone et la prise en charge des adresses IP virtuelles statiques. Les fonctionnalités existantes sous la référence SKU à disponibilité générale continuent à être prises en charge dans la nouvelle référence SKU v2, à quelques exceptions près que vous trouverez dans la section relative aux limitations connues. Les nouvelles références SKU v2 incluent les améliorations suivantes :

- **Mise à l’échelle automatique** : les déploiements d’Application Gateway ou du WAF sous la référence SKU de la mise à l’échelle automatique peuvent augmenter ou diminuer en fonction de l’évolution des modèles de charge du trafic. La mise à l’échelle automatique vous évite aussi d’avoir à choisir une taille de déploiement ou un nombre d’instances au moment du provisionnement. Cette référence (SKU) offre l’élasticité true. Dans la nouvelle référence SKU, Application Gateway peut fonctionner à la fois en mode de capacité fixe (mise à l’échelle automatique désactivée) et de mise à l’échelle automatique. Le mode de capacité fixe est utile pour les scénarios avec des charges de travail cohérentes et prévisibles. Le mode de mise à l’échelle automatique est utile dans les applications qui connaissent un trafic très variable.

- **Redondance de zone** : un déploiement d’Application Gateway ou de WAF peut désormais couvrir plusieurs zones de disponibilité, ce qui évite d’avoir à provisionner et épingler plusieurs instances d’Application Gateway dans chaque zone à l’aide d’une instance Traffic Manager. Vous pouvez choisir une ou plusieurs zones où les instances d’Application Gateway sont déployées, assurant ainsi une résilience en cas d’échec de la zone. Le pool principal pour les applications peut être distribué de la même façon entre les différentes zones de disponibilité.
- **Amélioration des performances** : la référence SKU de la mise à l’échelle automatique offre jusqu’à 5 fois plus de déchargement SSL que la référence SKU en disponibilité générale.
- **Déploiement et mise à jour plus rapides** La référence SKU de la mise à l’échelle automatique fournit un déploiement et une mise à jour plus rapides que la référence SKU en disponibilité générale.
- **Adresse IP virtuelle statique** : l’adresse IP virtuelle de la passerelle d’application prend désormais exclusivement en charge le type d’adresse IP virtuelle statique. Cela garantit que l’adresse IP virtuelle associé avec la passerelle d’application ne change pas même après un redémarrage.

> [!IMPORTANT]
> La référence SKU de la passerelle d’application redondante interzone et avec mise à l’échelle automatique est disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> La mise à l’échelle et la passerelle d’application redondant référence (SKU) prend désormais en charge [sonde d’intégrité](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) pour surveiller l’intégrité de toutes les ressources dans son pool back-end et mettez en surbrillance ces membres principaux qui sont considérés comme automatiquement défectueux. La sonde de contrôle d’intégrité par défaut finiront par être automatiquement configurés pour ces serveurs principaux pour lesquels vous n’avez pas configuré de n’importe quelle configuration de sonde personnalisée. Pour plus d’informations, consultez [sondes d’intégrité dans application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparaison des fonctionnalités de la référence (SKU) v1 et v2 référence (SKU)

Le tableau suivant compare les fonctionnalités disponibles avec chaque référence (SKU).

|                                                   | référence (SKU) v1   | référence (SKU) v2   |
| ------------------------------------------------- | -------- | -------- |
| Mise à l’échelle automatique                                       |          | &#x2713; |
| Redondance de zone                                   |          | &#x2713; |
| &nbsp;Adresse IP virtuelle statique&nbsp;&nbsp;                      |          | &#x2713; |
| Routage basé sur des URL                                 | &#x2713; | &#x2713; |
| Hébergement de plusieurs sites                             | &#x2713; | &#x2713; |
| Redirection du trafic                               | &#x2713; | &#x2713; |
| Pare-feu d’applications web (WAF)                    | &#x2713; | &#x2713; |
| Terminaison SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Chiffrement SSL de bout en bout                         | &#x2713; | &#x2713; |
| Affinité de session                                  | &#x2713; | &#x2713; |
| Pages d’erreur personnalisées                                | &#x2713; | &#x2713; |
| Réécrire les en-têtes HTTP (S)                           |          | &#x2713; |
| Prise en charge de WebSocket                                 | &#x2713; | &#x2713; |
| Assistance HTTP/2                                    | &#x2713; | &#x2713; |
| Vidage des connexions                               | &#x2713; | &#x2713; |
| Azure contrôleur d’entrée de Kubernetes Service (AKS) |          | &#x2713; |

## <a name="supported-regions"></a>Régions prises en charge

La référence SKU de la mise à l'échelle automatique est disponible dans les régions suivantes : USA Centre Nord, USA Centre Sud, USA Ouest, USA Ouest 2, USA Est, USA Est 2, USA Centre, Europe Nord, Europe Ouest, Asie Sud-Est, France Centre, Royaume-Uni Ouest, Japon Est, Japon Ouest.

## <a name="pricing"></a>Tarifs

La version préliminaire, aucun frais n’est. Vous êtes facturé pour les ressources autres que la passerelle d’application, telles que Key Vault, les machines virtuelles et ainsi de suite.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

|Problème|Détails|
|--|--|
|Certificat d’authentification|Non pris en charge.<br>Pour plus d’informations, consultez [Présentation du chiffrement SSL de bout en bout sur la passerelle Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinaison de Standard_v2 et Standard Application Gateway sur le même sous-réseau|Non pris en charge|
|Route définie par l’utilisateur sur le sous-réseau d’Application Gateway|Non pris en charge|
|Groupe de sécurité réseau pour plage de ports entrants| - 65 200 à 65 535 pour référence (SKU) Standard_v2<br>- 65 503 à 65 534 pour référence (SKU) Standard<br>Pour plus d’informations, visitez le [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Journaux d’activité de performances dans les diagnostics Azure|Non pris en charge.<br>Les métriques Azure doivent être utilisées.|
|Facturation|Aucune facturation.|
|Mode FIPS|Ils ne sont pas pris en charge.|
|Mode ILB uniquement|Non pris en charge actuellement. Les modes public et ILB sont pris en charge ensemble.|
|Intégration de Network Watcher|Non prise en charge dans la Préversion publique.|

## <a name="next-steps"></a>Étapes suivantes
- [Créer une passerelle d’application redondante dans une zone, avec mise à l’échelle automatique et avec une adresse IP virtuelle réservée à l’aide d’Azure PowerShell](tutorial-autoscale-ps.md)
- Découvrez [Application Gateway](overview.md).
- Découvrez le [Pare-feu Azure](../firewall/overview.md).
