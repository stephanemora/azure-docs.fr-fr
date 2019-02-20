---
title: Fichier Include
description: Fichier Include
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 02/07/2019
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: fe1993a914ea4d3bd8ab9116748198cbb0c1c43c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905261"
---
<a name="virtual-networking-limits-classic"></a>Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais du modèle de déploiement classique par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Réseaux virtuels |50 |100 |
| Sites de réseau local |20 |contacter le support |
| Serveurs DNS par réseau virtuel |20 |20 |
| Adresses IP privées par réseau virtuel |4096 |4096 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500K |500K |
| Groupes de sécurité réseau (NSG) |100 |200 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |200 |1 000 |
| Tables d'itinéraires définis par l'utilisateur |100 |200 |
| Itinéraires définis par l'utilisateur par table d'itinéraire |100 |400 |
| Adresses IP publiques (dynamiques) |5. |contacter le support |
| Adresses IP publiques réservées |20 |contacter le support |
| Adresse IP virtuelle publique par déploiement |5. |contacter le support |
| Adresse IP virtuelle privée (ILB) par déploiement |1 |1 |
| Listes de contrôle d'accès (ACL) par point de terminaison |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de mise en réseau – Azure Resource Manager
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’Azure Resource Manager par région et par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Récemment, nous avons augmenté toutes les limites par défaut à leur niveau maximal. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables. Si ces limites ont été augmentées par le support dans le passé et si vous ne voyez pas les limites mises à jour ci-dessous, vous pouvez [ouvrir une demande de support clientèle en ligne sans frais](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Ressource | Limite par défaut | 
| --- | --- |
| Réseaux virtuels |1 000 |
| Nombre de sous-réseaux par réseau virtuel |3000 |
| Homologations VNet par réseau virtuel |100 |
| Serveurs DNS par réseau virtuel |20 |
| Adresses IP privées par réseau virtuel |65536 |
| Adresses IP privées par interface réseau |256 |
| Adresses IP privées par machine virtuelle |256 |
| Flux TCP ou UDP simultanés par carte réseau de machine virtuelle ou instance de rôle |500K |
| Interfaces réseau (NIC) |65536 |
| Groupes de sécurité réseau (NSG) |5 000 |
| Règles de groupe de sécurité réseau par groupe de sécurité réseau |1 000 |
| Adresses IP et plages spécifiées pour la source et la destination dans un groupe de sécurité |4000 |
| Groupes de sécurité d’application |3000 |
| Groupes de sécurité d’application par configuration IP, par carte réseau |20 |
| Configurations IP par groupe de sécurité d’application |4000 |
| Groupes de sécurité d’application qui peuvent être spécifiés dans toutes les règles de sécurité d’un groupe de sécurité réseau |100 |
| Tables d'itinéraires définis par l'utilisateur |200 |
| Itinéraires définis par l'utilisateur par table d'itinéraire |400 |
| Certificats racines point à site pour chaque passerelle VPN |20 |
| TAP de réseau virtuel |100 |
| Configurations TAP d’interface réseau de chaque réseau virtuel TAP |100 |

#### <a name="publicip-address"></a>Limites de l’adresse IP publique
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Adresses IP publiques (dynamiques) |(De base) 1 000 |contacter le support |
| Adresses IP publiques (statiques) |(De base) 200 |contacter le support |
| Adresses IP publiques (statiques) |(Standard) 200 |contacter le support |
| Taille du préfixe de l'adresse IP publique (préversion) | /28 | /28 |

#### <a name="load-balancer"></a>Limites de l’équilibreur de charge
Les limites suivantes s’appliquent uniquement aux ressources de réseau gérées par le biais d’Azure Resource Manager par région et par abonnement. Découvrez comment [afficher l’utilisation actuelle de vos ressources par rapport aux limites de votre abonnement](../articles/networking/check-usage-against-limits.md)

| Ressource | Limite par défaut |
| --- | --- |
| Équilibreurs de charge | 1 000 | 
| Règles par ressource, De base | 250 |
| Règles par ressource, Standard | 1 500 | 
| Règles par configuration IP | 299 |
| Règles par groupe de sécurité réseau | 500 |
| Configurations d’adresses IP frontales, De base | 200 |
| Configurations d’adresses IP frontales, Standard | 600 |
| Pool principal, De base | 100, un seul groupe à haute disponibilité |
| Pool principal, Standard | 1000, un seul réseau virtuel |
| Ressources backend par équilibreur de charge, Standard* | 150 |
| Ports à haute disponibilité, Standard | 1 par serveur frontal interne |

** Jusqu’à 150 ressources, toute combinaison de ressources de machines virtuelles autonomes, ressources de groupes à haute disponibilité et ressources de groupes de machines virtuelles identiques.

