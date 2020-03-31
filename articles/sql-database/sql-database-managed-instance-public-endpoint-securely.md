---
title: Sécuriser des points de terminaison publics Managed Instance
description: Utilisez en toute sécurité des points de terminaison publics dans Azure avec une instance gérée
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 6dfeab3530445f8f9a102f47039d15b04fdf134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821740"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Utilisez une instance gérée Azure SQL Database en toute sécurité avec des points de terminaison publics

Les instances gérées Azure SQL Database peuvent à l’utilisateur une connectivité sur des [points de terminaison publics](../virtual-network/virtual-network-service-endpoints-overview.md). Cet article explique comment renforcer la sécurité de cette configuration.

## <a name="scenarios"></a>Scénarios

Une instance gérée SQL Database fournit un point de terminaison privé pour permettre la connectivité à partir de l’intérieur de son réseau virtuel. L’option par défaut consiste à assurer une isolation maximale. Toutefois, il existe des scénarios dans lesquels vous avez besoin de fournir une connexion à un point de terminaison public :

- L’instance gérée doit s’intégrer à des offres PaaS (platform-as-a-service) mutualisées uniquement.
- Vous avez besoin d’un débit d’échange de données plus élevé que celui dont vous pouvez disposer lorsque vous utilisez un VPN.
- Les stratégies d’entreprise interdisent les plateformes PaaS dans les réseaux d’entreprise.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Déployer une instance gérée pour l’accès aux points de terminaison publics

Bien que non obligatoire, le modèle de déploiement courant pour une instance gérée avec un accès aux points de terminaison publics consiste à créer l’instance dans un réseau virtuel isolé dédié. Dans cette configuration, le réseau virtuel est utilisé uniquement pour l’isolation de cluster virtuelle. Peu importe si l’espace d’adressage IP de l’instance gérée chevauche celui du réseau d’entreprise.

## <a name="secure-data-in-motion"></a>Sécuriser les données en mouvement

Le trafic de données des instances gérées est toujours chiffré si le pilote client prend en charge le chiffrement. Les données envoyées entre l’instance gérée et d’autres machines virtuelles ou services Azure ne quittent jamais le réseau principal d’Azure. S’il existe une connexion entre l’instance gérée et un réseau local, nous vous recommandons d’utiliser Azure ExpressRoute avec un peering Microsoft. ExpressRoute vous permet d’éviter le déplacement de données via l’Internet public. Pour la connectivité privée de l’instance gérée, seul le peering privé peut être utilisé.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Verrouiller la connectivité entrante et sortante

Le schéma suivant illustre les configurations de sécurité recommandées :

![Configurations de sécurité pour le verrouillage de connectivité entrante et sortante](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Une instance gérée a une [adresse de point de terminaison public dédiée](sql-database-managed-instance-find-management-endpoint-ip-address.md). Au niveau du pare-feu de trafic sortant côté client et dans les règles de groupe de sécurité réseau, définissez cette adresse IP de point de terminaison public de façon à limiter la connectivité sortante.

Pour garantir que le trafic vers l’instance gérée provient de sources approuvées, nous vous recommandons d’établir une connexion à partir de sources dont les adresses IP sont bien connues. Utilisez un groupe de sécurité réseau pour limiter l’accès au point de terminaison public de l’instance gérée sur le port 3342.

Lorsque les clients doivent établir une connexion à partir d’un réseau local, assurez-vous que l’adresse d’origine est traduite en un ensemble d’adresses IP bien connu. Si ce n’est pas possible (par exemple, dans le scénario classique d’un personnel mobile), nous vous recommandons d’utiliser [des connexions VPN point à site et un point de terminaison privé](sql-database-managed-instance-configure-p2s.md).

Si les connexions sont établies à partir d’Azure, il est recommandé que le trafic provienne d’une [adresse IP virtuelle](../virtual-network/virtual-networks-reserved-public-ip.md) affectée bien connue (par exemple, une machine virtuelle). Pour faciliter la gestion des adresses IP virtuelles, vous pouvez utiliser [des préfixes d’adresse IP publique](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer un point de terminaison public pour gérer les instances : [Configurer un point de terminaison public](sql-database-managed-instance-public-endpoint-configure.md)
