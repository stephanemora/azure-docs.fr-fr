---
title: Application de connexion Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article explique comment connecter votre application à Azure SQL Database Managed Instance.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 82e8836892b033ccbb3c3ad9806257348afe3702
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818400"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Connecter votre application à Azure SQL Database Managed Instance

Aujourd’hui, plusieurs choix s’offrent à vous pour déterminer comment et où héberger votre application. 
 
Vous pouvez choisir le cloud en utilisant soit Azure App Service, soit certaines options intégrées de réseau virtuel Azure comme l’environnement Azure App Service, une machine virtuelle ou un groupe de machines virtuelles identiques. Vous pouvez également adopter une approche du cloud hybride pour conserver vos applications localement. 
 
Quel que soit le choix effectué, vous pouvez le connecter à Managed Instance (préversion).  

![haute disponibilité](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Connecter une application à l’intérieur du même réseau virtuel 

Ce scénario est le plus simple. Des machines virtuelles à l’intérieur du réseau virtuel peuvent se connecter entre elles directement même si elles sont dans des sous-réseaux différents. Cela signifie que pour connecter une application dans un environnement Azure Application ou une machine virtuelle, il vous suffit de définir correctement la chaîne de connexion.  
 
Si vous ne parvenez pas à établir la connexion, vérifiez si un groupe de sécurité réseau est défini sur le sous-réseau de l’application. Le cas échéant, vous devez ouvrir une connexion sortante sur le port SQL 1 433 ainsi que la plage des ports 11 000 à 12 000 à des fins de redirection. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Connecter une application à l’intérieur d’un autre réseau virtuel 

Ce scénario est un peu plus complexe, car Managed Instance a une adresse IP privée dans son propre réseau virtuel. Pour établir la connexion, une application a besoin d’accéder au réseau virtuel dans lequel Managed Instance est déployé. Ainsi, vous devez d’abord établir une connexion entre l’application et le réseau virtuel Managed Instance. Les réseaux virtuels ne doivent pas nécessairement être dans le même abonnement pour que ce scénario fonctionne. 
 
Il existe deux options pour connecter des réseaux virtuels : 
- [Homologation de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md) 
- Passerelle VPN de réseau virtuel à réseau virtuel ([portail Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
L’option d’homologation est préférable car elle utilise le réseau principal de Microsoft, donc du point de vue de la connectivité, il n’y a pas de différence notable de latence entre les machines virtuelles dans le réseau virtuel homologué et dans le même réseau virtuel. L’homologation de réseau virtuel est limité aux réseaux d’une même région.  
 
> [!IMPORTANT]
> Le scénario d’homologation de réseau virtuel pour Managed Instance est limité aux réseaux de la même région en raison de [contraintes de l’homologation de réseau virtuel globale](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Connecter une application locale 

Managed Instance est uniquement accessible par le biais d’une adresse IP privée. Afin d’y accéder localement, vous devez établir une connexion de site à site entre l’application et le réseau virtuel Managed Instance. 
 
Vous avez deux options pour la connexion locale à un réseau virtuel Azure : 
- Connexion VPN de site à site ([portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- Connexion [ExpressRoute](../expressroute/expressroute-introduction.md)  
 
Si vous avez établi une connexion locale à Azure et que vous ne parvenez pas à établir une connexion à Managed Instance, vérifiez si votre pare-feu dispose d’une connexion sortante ouverte sur le port SQL 1 433 et la plage de ports 11 000 à 12 000 à des fins de redirection. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Connecter une application hébergée Azure App Service 

Managed Instance est uniquement accessible par le biais d’une adresse IP privée, donc pour y accéder à partir d’Azure App Service, vous devez d’abord établir une connexion entre l’application et le réseau virtuel Managed Instance. Consultez [Intégrer une application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Pour résoudre les problèmes, consultez [Résolution des problèmes liés aux réseaux virtuels et aux applications](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Si aucune connexion ne peut être établie, essayez de [synchroniser la configuration de la mise en réseau](sql-database-managed-instance-sync-network-configuration.md). 
 
L’intégration d’Azure App Service à un réseau homologué avec un réseau virtuel Managed Instance constitue un cas spécial de connexion entre Azure App Service et Managed Instance. Ce cas nécessite la configuration suivante : 

- Le réseau virtuel Managed Instance ne doit PAS avoir de passerelle.  
- Le réseau virtuel Managed Instance doit utiliser des passerelles distantes. 
- Le réseau virtuel homologué doit autoriser le transit par passerelle. 
 
Ce scénario est illustré dans le diagramme suivant :

![homologation d’applications intégrées](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Connecter une application dans la box de développeur 

Managed Instance est uniquement accessible par le biais d’une adresse IP privée, donc pour y accéder à partir de votre box de développeur, vous devez d’abord établir une connexion entre cette dernière et le réseau virtuel Managed Instance.  
 
Configurez une connexion point à site à un réseau virtuel à l’aide des articles sur l’authentification par certificat Azure native ([portail Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)). 

## <a name="required-versions-of-drivers-and-tools"></a>Versions exigées de pilotes et d’outils

Les versions minimales suivantes des outils et des pilotes sont recommandées si vous voulez vous connecter à Managed Instance :

| Pilote/outil | Version |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) | 
|Pilote ODBC    | v17 |
|Pilote PHP | 5.2.0 |
|Pilote JDBC    | 6.4.0 |
|Pilote Node.js | 2.1.1 |
|Pilote OLEDB   | 18.0.2.0 |
|SSMS   | 17.8.1 ou [version ultérieure](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’option Managed Instance, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md).
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md).
