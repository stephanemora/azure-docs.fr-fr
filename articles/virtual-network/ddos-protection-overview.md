---
title: Vue d’ensemble du service Protection DDos Standard Azure
description: Découvrez comment la norme Azure DDoS Protection, combinée avec les meilleures pratiques de conception d’application, fournit une protection contre les attaques DDoS.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: af10ec10a1622f32dbc34a607b1200cb4cff8b59
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168046"
---
# <a name="azure-ddos-protection-standard-overview"></a>Vue d’ensemble du service Protection DDos Standard Azure

Les attaques par déni de service distribué (DDoS) représentent certains des problèmes de disponibilité et de sécurité majeurs auxquels sont confrontés les clients qui déplacent leurs applications vers le cloud. Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet.

Combiné aux bonnes pratiques de conception d’application, le service de protection DDoS Azure assure une excellente protection contre les attaques DDoS. Azure DDoS Protection fournit les niveaux de service suivants :

- **De base** : automatiquement activé dans le cadre de la plateforme Azure. La supervision permanente du trafic et l’atténuation en temps réel des attaques courantes au niveau du réseau fournissent les mêmes défenses que celles utilisées par les services en ligne de Microsoft. La distribution et l’atténuation du trafic d’attaque peuvent être réalisées entre différentes régions à l’échelle du réseau global d’Azure. La protection est assurée pour les[adresses IP publiques](virtual-network-public-ip-address.md) IPv4 et IPv6 Azure.
- **Standard** : fournit des fonctionnalités d’atténuation supplémentaires par rapport au niveau de service De base destinées spécifiquement aux ressources de réseau virtuel Azure. Le service DDoS Protection Standard est facile à activer et ne nécessite aucune modification de l’application. Les stratégies de protection sont paramétrées par le biais d’algorithmes de surveillance du trafic et d’apprentissage automatique dédiés. Elles sont appliquées aux adresses IP publiques associées aux ressources déployées sur des réseaux virtuels, telles que les instances Azure Service Fabric, Azure Load Balancer et Azure Application Gateway, mais cette protection ne s’applique pas aux environnements App Service. Les données de télémétrie en temps réel sont disponibles par le biais d’affichages Azure Monitor pendant une attaque et à des fins d’historique. Des analyses avancées de la prévention des attaques sont disponibles par le biais de paramètres de diagnostic. Vous pouvez ajouter une protection de la couche Application par le biais du [pare-feu d’applications web Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou en installant un pare-feu tiers à partir de la Place de marché Azure. La protection est assurée pour les[adresses IP publiques](virtual-network-public-ip-address.md) IPv4 et IPv6 Azure.

|Fonctionnalité                                         |DDoS Protection Basic                 |DDoS Protection Standard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Supervision du trafic actif et détection Always On |Oui                                   |Oui                                           |
|Atténuation automatique des attaques                    |Oui                                   |Oui                                           |
|Garantie de disponibilité                          |Région Azure                          |Application                                   |
|Stratégies d’atténuation des risques                             |Optimisé pour le volume de la région du trafic Azure |Optimisé pour le volume du trafic des applications          |
|Métriques et alertes                                |Non                                    |Journaux des métriques et ressources des attaques en temps réel via                                                                                            Azure Monitor                                 |
|Rapports d’atténuation des risques                              |Non                                    |Rapports d’atténuation des attaques après l’attaque                |
|Journaux des flux d’atténuation des risques                            |Non                                    |Flux de journal NRT pour l’intégration de SIEM           |
|Personnalisation de la stratégie d’atténuation des risques                 |Non                                    |Engager des experts DDos                           |
|Support                                         |Meilleur travail                           |Accès à des experts DDos pendant une attaque active|
|Contrat SLA                                             |Région Azure                          |Garantie d’application et protection des coûts       |
|Tarifs                                         |Gratuit                                  |Par mois et basé sur l’utilisation                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Types d’attaques DDoS atténuées par le service Protection DDos Standard

Le service Protection DDoS Standard peut atténuer les types d’attaques suivants :

- **Attaques volumétriques** : L’objectif de ces attaques est de submerger la couche réseau d’une quantité substantielle de trafic apparemment légitime. Elles incluent les saturations UDP, les saturations par amplification et autres saturations par falsification de paquets. Le service Protection DDoS Standard atténue ces attaques potentielles de plusieurs gigaoctets en les absorbant et en les purgeant, grâce à l’échelle du réseau global d’Azure, automatiquement.
- **Attaques de protocole** : ces attaques rendent une cible inaccessible, en exploitant une faille dans la pile de protocole des couches 3 et 4. Elles incluent les attaques par saturation SYN, les attaques par réflexion et d’autres attaques de protocole. Le service Protection DDoS Standard atténue ces attaques en faisant la distinction entre le trafic légitime et le trafic malveillant, qu’il bloque par l’intermédiaire du client. 
- **Attaques de la couche Ressource (Application)** : ces attaques ciblent les paquets d’application web pour interrompre la transmission des données entre des hôtes. Elles incluent les violations de protocole HTTP, l’injection SQL, les scripts de site à site et autres attaques de la couche 7. Utilisez un pare-feu d'applications web, tel que le [pare-feu d’applications web Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure, ainsi que le service Protection DDos Standard pour offrir une défense contre ces attaques. Il existe également des offres de pare-feu d’application web tiers sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Le service Protection DDos Standard protège les ressources d’un réseau virtuel, y compris les adresses IP publiques associées aux machines virtuelles, les équilibreurs de charge internes et les passerelles d’application. Associé au pare-feu d'applications web sur Application Gateway ou à un pare-feu d'applications web tiers déployé dans un réseau virtuel avec une adresse IP publique, le service Protection DDos Standard peut fournir une fonctionnalité d’atténuation complète pour les couches 3 à 7.

## <a name="ddos-protection-standard-features"></a>Fonctionnalités du service Protection DDos Standard

![Fonctionnalités de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Les fonctionnalités du service Protection DDos Standard sont les suivantes :

- **Intégration de la plateforme native :** Intégré en natif dans Azure. Inclut la configuration par le biais du portail Azure. Le service Protection DDos Standard comprend vos ressources et leur configuration.
- **Protection clé en main :** La configuration simplifiée protège immédiatement toutes les ressources situées sur un réseau virtuel dès que DDoS Protection Standard est activé. Aucune définition ou intervention de l’utilisateur n’est nécessaire. Le service Protection DDoS Standard atténue de façon instantanée et automatique l’attaque une fois que celle-ci est détectée.
- **Surveillance permanente du trafic :** Vos modèles de trafic d’application sont surveillés 24h/24 et 7j/7, à la recherche d’indicateurs d’attaques DDoS. L’atténuation est effectuée en cas de dépassement des stratégies de protection.
- **Optimisation adaptative :** Le profilage intelligent du trafic étudie le trafic de votre application au fil du temps pour sélectionner et mettre à jour le profil le plus adapté pour votre service. Le profil s’ajuste en fonction des modifications du trafic au fil du temps.
- **Protection multi-couches :** Offre une protection DDoS de pile complète, quand elle est utilisée avec un pare-feu d’application web.
- **Échelle de prévention étendue :** Plus de 60 types d’attaques différents peuvent être contrées, avec une protection globale contre les attaques DDoS les plus connues.
- **Analytique des attaques :** Recevez des rapports détaillés toutes les cinq minutes pendant une attaque, et un résumé complet une fois l’attaque terminée. Transmettez en continu les journaux de flux de prévention des attaques à un système hors ligne de gestion des informations et des événements de sécurité (SIEM) pour une supervision en temps quasi-réel pendant une attaque.
- **Métriques des attaques :** Des métriques récapitulatives de chaque attaque sont accessibles via Azure Monitor.
- **Alerte d’attaque :** Vous pouvez configurer des alertes pour le début et la fin d’une attaque, ainsi que pendant qu’elle se produit, avec des métriques d’attaque intégrées. Les alertes s’intègrent à vos logiciels opérationnels, comme les journaux d’activité Microsoft Azure Monitor, Splunk, Stockage Azure, votre messagerie électronique et le portail Azure.
- **Maîtrise des coûts :** Si vous documentez les attaques DDoS, vous bénéficiez en retour de crédits pour les services de scale-out d’application et de transfert de données.

## <a name="ddos-protection-standard-mitigation"></a>Mitigation avec le service Protection DDos Standard

Le service Protection DDoS Standard surveille l’utilisation du trafic réelle et la compare en permanence aux seuils définis dans la stratégie DDoS. Si le seuil de trafic est dépassé, l’atténuation DDoS est lancée automatiquement. Quand le trafic repasse sous le seuil, l’atténuation est supprimée.

![Limitation des risques](./media/ddos-protection-overview/mitigation.png)

Pendant l’atténuation, le trafic envoyé vers la ressource protégée est redirigé par le service de protection DDoS et plusieurs vérifications sont effectuées, telles que les suivantes :

- Vérifier que les paquets sont conformes aux spécifications de l’Internet et qu’ils ne sont pas mal formés.
- Interagir avec le client pour déterminer s’il s’agit éventuellement d’un paquet falsifié (par exemple, au moyen des techniques SYN Auth ou SYN Cookie ou en supprimant un paquet afin que la source le retransmette).
- Limiter le débit des paquets si aucune autre méthode de mise en œuvre ne peut être effectuée.

Le service de protection DDoS bloque le trafic d’attaque et transfère le trafic restant vers la destination prévue. Dans les quelques minutes qui suivent la détection d’une attaque, vous êtes informé grâce aux métriques Azure Monitor. Vous pouvez configurer la journalisation des données de télémétrie du service Protection DDos Standard de manière à écrire les journaux d’activité des options disponibles en vue d’une analyse future. Les données des métriques dans Azure Monitor pour le service Protection DDoS Standard sont conservées pendant 30 jours.

Microsoft travaille en collaboration avec [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) pour créer une interface à l’aide de laquelle vous pouvez générer du trafic sur les adresses IP publiques dotées de DDoS Protection à des fins de simulation. La simulation BreakPoint Cloud vous permet d’effectuer les tâches suivantes :

- Valider la façon dont Microsoft Azure DDoS Protection Standard protège vos ressources Azure contre les attaques DDoS
- Optimiser votre processus de réponse aux incidents en cas d’attaque DDoS
- Documenter la conformité DDoS
- Former des équipes de sécurité réseau

## <a name="next-steps"></a>Étapes suivantes

- [Configuration le service DDoS Protection Standard](manage-ddos-protection.md)
