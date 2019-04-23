---
title: Sécuriser les points de terminaison publics instance gérée - Azure SQL Database instance managée | Microsoft Docs
description: Utiliser en toute sécurité des points de terminaison publics dans Azure avec l’instance managée
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014024"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>À l’aide d’Azure SQL Database managed instance en toute sécurité avec le point de terminaison public

Azure SQL Database instance managée peut être activée pour fournir une connectivité de l’utilisateur via [point de terminaison public](../virtual-network/virtual-network-service-endpoints-overview.md). Cet article explique comment renforcer la sécurité de cette configuration.

## <a name="scenarios"></a>Scénarios

Instance managée fournit un point de terminaison pour activer la connectivité à partir d’à l’intérieur de son réseau virtuel privé. L’option par défaut consiste à assurer l’isolation maximale. Toutefois, il existe des scénarios où une connexion de point de terminaison public est nécessaire :

- Intégration avec les offres PaaS seules architecture mutualisées.
- Débit plus élevé d’échange de données que l’utilisation de VPN.
- Stratégies d’entreprise interdisent PaaS à l’intérieur des réseaux d’entreprise.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Déploiement d’une instance managée pour l’accès de point de terminaison public

Bien que non obligatoire, le modèle de déploiement courantes pour une instance gérée avec un accès de point de terminaison public consiste à créer l’instance d’un réseau virtuel isolé dédié. Dans cette configuration, le réseau virtuel est utilisé uniquement pour l’isolation de cluster virtuel. Il n’est pas pertinente si l’espace d’adressage IP instance managée chevauche un espace d’adressage IP réseau d’entreprise.

## <a name="securing-data-in-motion"></a>Sécurisation des données en mouvement

Trafic de données d’instance gérée est toujours chiffré si le pilote client prend en charge le chiffrement. Les données entre l’instance gérée et d’autres Machines virtuelles ou des services Azure ne quittent jamais dorsale principale d’Azure. S’il existe une instance gérée à une connexion de réseau local, il est recommandé d’utiliser Express Route avec l’homologation Microsoft. Express Route permettra d’éviter le déplacement des données via Internet public (pour la connectivité privée instance gérée, uniquement l’homologation privée peut être utilisé).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Verrouillage de connectivité entrante et sortante

Le diagramme suivant montre les configurations de sécurité recommandées.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Managed Instance a un [adresse de point de terminaison public dédiée](sql-database-managed-instance-find-management-endpoint-ip-address.md). Cette adresse IP doit être définie dans le pare-feu de trafic sortant de côté client et les règles de groupe de sécurité réseau pour limiter la connectivité sortante.

Pour garantir que le trafic vers l’instance gérée provenant de sources approuvées, il est recommandé de vous connecter à partir de sources avec des adresses IP connues. Limiter l’accès au point de terminaison public instance gérée sur le port 3342 à l’aide d’un groupe de sécurité réseau.

Lorsque les clients doivent établir une connexion à partir d’un réseau local, assurez-vous que l’adresse d’origine est traduite en un ensemble connu d’adresses IP. Si qui n’est pas possible (par exemple, le personnel mobile en cours d’un scénario classique), il est recommandé d’utiliser [Point-to-site VPN connexions et un point de terminaison privé](sql-database-managed-instance-configure-p2s.md).

Si les connexions sont démarrées à partir d’Azure, il est recommandé que le trafic provient bien connu attribué [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (par exemple, les Machines virtuelles). Pour faciliter la gestion des adresses IP virtuelles, les clients peuvent prendre en compte à l’aide de [le préfixe d’adresse IP publique](../virtual-network/public-ip-address-prefix.md).