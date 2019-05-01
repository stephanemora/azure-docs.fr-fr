---
title: Fichier Include
description: Fichier Include
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 04/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 79c4530878783cbdb62cac630d81b65dc73c52a4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733735"
---
<a name="virtual-networking-limits-classic"></a>Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais du modèle de déploiement classique par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Réseaux virtuels |50 |100 |
| Sites de réseau local |20 |Contactez le support technique. |
| Serveurs DNS par réseau virtuel |20 |20 |
| Adresses IP privées par réseau virtuel |4 096 |4 096 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500 000, jusqu'à 1 000 000 de deux ou plusieurs cartes réseau. |500 000, jusqu'à 1 000 000 de deux ou plusieurs cartes réseau. |
| Groupes de sécurité réseau (NSG) |100 |200 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |200 |1 000 |
| Tables d’itinéraires définis par l’utilisateur |100 |200 |
| Itinéraires définis par l’utilisateur par table de routage |100 |400 |
| Adresses IP publiques (dynamiques) |5. |Contactez le support technique. |
| Adresses IP publiques réservées |20 |Contactez le support technique. |
| Adresse IP virtuelle publique par déploiement |5. |Contactez le support technique. |
| Adresses IP virtuelles privées (équilibrage de charge interne) par déploiement |1 |1 |
| Listes de contrôle d’accès de point de terminaison (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de mise en réseau – Azure Resource Manager
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’Azure Resource Manager par région et par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Récemment, nous avons augmenté toutes les limites par défaut à leur niveau maximal. S’il n’existe aucune colonne de limite maximale, la ressource n’a pas limites ajustables. Si vous avez ces limites augmentés de prise en charge dans le passé et que vous ne voyez pas les limites mises à jour dans les tableaux suivants, [ouvrir une demande de support clientèle en ligne sans frais](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Ressource | Limite de limite et maximales par défaut | 
| --- | --- |
| Réseaux virtuels |1 000 |
| Nombre de sous-réseaux par réseau virtuel |3 000 |
| Homologations de réseaux virtuels par réseau virtuel |500 |
| Serveurs DNS par réseau virtuel |20 |
| Adresses IP privées par réseau virtuel |65,536 |
| Adresses IP privées par interface réseau |256 |
| Adresses IP privées par machine virtuelle |256 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500 000 |
| Cartes d'interface réseau |65,536 |
| Network Security Group |5 000 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |1 000 |
| Adresses IP et plages spécifiées pour la source et la destination dans un groupe de sécurité |4 000 |
| Groupes de sécurité d’application |3 000 |
| Groupes de sécurité d’application par configuration IP, par carte réseau |20 |
| Configurations IP par groupe de sécurité d’application |4 000 |
| Groupes de sécurité d’application qui peuvent être spécifiés dans toutes les règles de sécurité d’un groupe de sécurité réseau |100 |
| Tables d’itinéraires définis par l’utilisateur |200 |
| Itinéraires définis par l’utilisateur par table de routage |400 |
| Certificats de point-to-site racine par passerelle VPN Azure |20 |
| TAP de réseau virtuel |100 |
| Configurations TAP d’interface réseau de chaque réseau virtuel TAP |100 |

#### <a name="publicip-address"></a>Limites de l’adresse IP publique
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Adresses IP publiques (dynamiques) | 1 000 pour Basic. |Contactez le support technique. |
| Adresses IP publiques (statiques) | 1 000 pour Basic. |Contactez le support technique. |
| Adresses IP publiques (statiques) | 200 pour l’Édition Standard.|Contactez le support technique. |
| Taille de préfixe IP publique (version préliminaire) | /28 | /28 |

#### <a name="load-balancer"></a>Limites de l’équilibreur de charge
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’Azure Resource Manager par région et par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

| Ressource | Limite par défaut |
| --- | --- |
| Équilibreurs de charge | 1 000 | 
| Règles par ressource, De base | 250 |
| Règles par ressource, Standard | 1 500 | 
| Règles par configuration IP | 299 |
| Règles par groupe de sécurité réseau | 500 |
| Configurations IP frontales, Basic | 200 |
| Configurations IP frontales, Standard | 600 |
| Pool back-end, Basic | 100, à haute disponibilité |
| Pool back-end, Standard | 1 000, seul réseau virtuel |
| Ressources back-end par équilibreur de charge Standard<sup>1</sup> | 150 |
| Ports de haute disponibilité, Standard | 1 par interne frontal |

<sup>1</sup>la limite est de ressources jusqu'à 150, dans n’importe quelle combinaison de ressources de machine virtuelle autonome, ressources et les ressources de jeu de mise à l’échelle de machine virtuelle à haute disponibilité.

