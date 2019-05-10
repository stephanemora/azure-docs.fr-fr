---
title: Sécuriser les points de terminaison publics instance gérée - Azure SQL Database instance managée | Microsoft Docs
description: Utiliser en toute sécurité des points de terminaison publics dans Azure avec une instance gérée
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470311"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Utiliser une instance gérée Azure SQL Database en toute sécurité avec les points de terminaison publics

Azure SQL Database, les instances gérées peuvent fournir une connectivité de l’utilisateur sur [points de terminaison publics](../virtual-network/virtual-network-service-endpoints-overview.md). Cet article explique comment renforcer la sécurité de cette configuration.

## <a name="scenarios"></a>Scénarios

Une instance gérée de SQL Database fournit un point de terminaison pour permettre la connectivité à partir d’à l’intérieur de son réseau virtuel privé. L’option par défaut consiste à assurer l’isolation maximale. Toutefois, il existe des scénarios où vous avez besoin pour fournir une connexion de point de terminaison public :

- L’instance managée doit intégrer les offres de (PaaS) de platform-as-a-service multi-mutualisée uniquement.
- Vous avez besoin d’un débit plus élevé d’échange de données que possible lorsque vous utilisez un réseau privé virtuel.
- Stratégies d’entreprise interdisent PaaS à l’intérieur des réseaux d’entreprise.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Déployer une instance gérée pour l’accès de point de terminaison public

Bien que non obligatoire, le modèle de déploiement courantes pour une instance gérée avec un accès de point de terminaison public consiste à créer l’instance d’un réseau virtuel isolé dédié. Dans cette configuration, le réseau virtuel est utilisé uniquement pour l’isolation de cluster virtuel. Peu importe si l’espace d’adressage IP de l’instance managée se chevauche avec l’espace d’adressage IP d’un réseau d’entreprise.

## <a name="secure-data-in-motion"></a>Sécuriser les données en mouvement

Trafic de données d’instance gérée est toujours chiffré si le pilote client prend en charge le chiffrement. Les données envoyées entre l’instance gérée et d’autres machines virtuelles ou des services Azure ne quittent jamais dorsale principale d’Azure. S’il existe une connexion entre l’instance gérée et un réseau local, nous vous recommandons de qu'utiliser Azure ExpressRoute avec l’homologation Microsoft. ExpressRoute vous permet d’éviter le déplacement des données via l’internet public. Pour la connectivité privée instance gérée, uniquement l’homologation privée peut être utilisé.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Verrouiller la connectivité entrante et sortante

Le diagramme suivant montre les configurations de sécurité recommandées :

![Configurations de sécurité pour le verrouillage de connectivité entrante et sortante](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Une instance gérée a un [adresse de point de terminaison public dédiée](sql-database-managed-instance-find-management-endpoint-ip-address.md). Dans le pare-feu de trafic sortant du côté client et les règles de groupe de sécurité réseau, définissez cette adresse IP de point de terminaison public pour limiter la connectivité sortante.

Pour garantir que le trafic vers l’instance gérée provenant de sources approuvées, nous vous recommandons de connexion à partir de sources avec des adresses IP connues. Utilisez un groupe de sécurité réseau pour limiter l’accès au point de terminaison public instance gérée sur le port 3342.

Lorsque les clients doivent établir une connexion à partir d’un réseau local, assurez-vous que l’adresse d’origine est traduite en un ensemble connu d’adresses IP. Si vous ne pouvez pas le faire (par exemple, un personnel mobile en cours d’un scénario classique), nous vous recommandons d’utiliser [connexions de point-to-site VPN et un point de terminaison privé](sql-database-managed-instance-configure-p2s.md).

Si les connexions sont démarrées à partir d’Azure, nous recommandons que le trafic proviennent d’un bien connu affecté [adresse IP virtuelle](../virtual-network/virtual-networks-reserved-public-ip.md) (par exemple, une machine virtuelle). Pour faciliter la gestion des adresses IP virtuelles (VIP) plus facile, vous souhaiterez peut-être utiliser [des préfixes d’adresse IP publiques](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer le point de terminaison public pour gérer les instances : [Configurer le point de terminaison public](sql-database-managed-instance-public-endpoint-configure.md)
