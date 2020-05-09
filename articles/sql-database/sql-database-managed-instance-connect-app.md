---
title: Connecter une application à Managed instance
description: Cet article explique comment connecter votre application à Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 8d920fb7815e5a9fe30d8f3b4e40f36133d83222
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538084"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Connecter votre application à l’instance gérée Azure SQL Database

Aujourd’hui, plusieurs choix s’offrent à vous pour déterminer comment et où héberger votre application.

Vous pouvez choisir le cloud en utilisant soit Azure App Service, soit certaines options intégrées de réseau virtuel Azure comme l’environnement Azure App Service, une machine virtuelle ou un groupe de machines virtuelles identiques. Vous pouvez également adopter une approche du cloud hybride pour conserver vos applications localement.

Quel que soit le choix effectué, vous pouvez le connecter à Managed Instance.  

![haute disponibilité](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Connecter une application à l’intérieur du même réseau virtuel

Ce scénario est le plus simple. Des machines virtuelles à l’intérieur du réseau virtuel peuvent se connecter entre elles directement même si elles sont dans des sous-réseaux différents. Cela signifie que pour connecter une application dans un environnement Azure Application ou une machine virtuelle, il vous suffit de définir correctement la chaîne de connexion.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Connecter une application à l’intérieur d’un autre réseau virtuel

Ce scénario est un peu plus complexe, car Managed Instance a une adresse IP privée dans son propre réseau virtuel. Pour établir la connexion, une application a besoin d’accéder au réseau virtuel dans lequel Managed Instance est déployé. Ainsi, vous devez d’abord établir une connexion entre l’application et le réseau virtuel Managed Instance. Les réseaux virtuels ne doivent pas nécessairement être dans le même abonnement pour que ce scénario fonctionne.

Il existe deux options pour connecter des réseaux virtuels :

- [Peering de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md)
- Passerelle VPN de réseau virtuel à réseau virtuel ([portail Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

L’option de peering est préférable car elle utilise le réseau principal de Microsoft, donc du point de vue de la connectivité, il n’y a pas de différence notable de latence entre les machines virtuelles dans le réseau virtuel appairé et dans le même réseau virtuel. Le peering de réseau virtuel est limité aux réseaux d’une même région.  

> [!IMPORTANT]
> Le scénario de peering de réseau virtuel pour Managed Instance est limité aux réseaux de la même région en raison de [contraintes du peering de réseau virtuel global](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Consultez également la section appropriée de l’article [Forum Aux Questions sur les réseaux virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) pour plus d’informations. 

## <a name="connect-an-on-premises-application"></a>Connecter une application locale

Managed Instance est uniquement accessible par le biais d’une adresse IP privée. Afin d’y accéder localement, vous devez établir une connexion de site à site entre l’application et le réseau virtuel Managed Instance.

Vous avez deux options pour la connexion locale à un réseau virtuel Azure :

- Connexion VPN de site à site ([portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- Connexion [ExpressRoute](../expressroute/expressroute-introduction.md)  

Si vous avez établi une connexion locale à Azure et que vous ne parvenez pas à établir une connexion à Managed Instance, vérifiez si votre pare-feu dispose d’une connexion sortante ouverte sur le port SQL 1433 et la plage de ports 11000 à 11999 à des fins de redirection.

## <a name="connect-an-application-on-the-developers-box"></a>Connecter une application dans la box de développeur

Managed Instance est uniquement accessible par le biais d’une adresse IP privée, donc pour y accéder à partir de votre box de développeur, vous devez d’abord établir une connexion entre cette dernière et le réseau virtuel Managed Instance. Pour cela, configurez une connexion point à site à un réseau virtuel à l’aide de l’authentification par certificat Azure native. Pour plus d’informations, consultez [Configurer une connexion point à site pour se connecter à Azure SQL Database Managed Instance à partir d’un ordinateur local](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Se connecter à partir d’un ordinateur local avec le peering VNet

Un autre scénario utilisé par les clients est celui où la passerelle VPN est installée sur un réseau virtuel et dans un abonnement différents de ceux où est hébergé Managed Instance. Les deux réseaux virtuels sont ensuite appairés. Le diagramme d’architecture suivant montre comment ce scénario peut être implémenté.

![Peering de réseaux virtuels](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Une fois que vous avez configuré l’infrastructure de base, vous devez modifier certains paramètres afin que la passerelle VPN puisse voir les adresses IP dans le réseau virtuel qui héberge Managed Instance. Pour ce faire, apportez les modifications très spécifiques qui suivent dans **Paramètres de peering**.

1. Dans le réseau virtuel qui héberge la passerelle VPN, accédez à **Peerings**, puis à la connexion de réseau virtuel appairée à l’instance managée, et cliquez sur **Autoriser le transit par passerelle**.
2. Dans le réseau virtuel qui héberge l’instance managée, accédez à **Peerings**, puis à la connexion de réseau virtuel appairée à la passerelle VPN, et cliquez sur **Utiliser des passerelles distantes**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Connecter une application hébergée Azure App Service

Managed Instance est uniquement accessible par le biais d’une adresse IP privée, donc pour y accéder à partir d’Azure App Service, vous devez d’abord établir une connexion entre l’application et le réseau virtuel Managed Instance. Consultez [Intégrer une application à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md).  

Pour résoudre les problèmes, consultez [Résolution des problèmes liés aux réseaux virtuels et aux applications](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Si aucune connexion ne peut être établie, essayez de [synchroniser la configuration de la mise en réseau](sql-database-managed-instance-sync-network-configuration.md).

L’intégration d’Azure App Service à un réseau homologué avec un réseau virtuel Managed Instance constitue un cas spécial de connexion entre Azure App Service et Managed Instance. Ce cas nécessite la configuration suivante :

- Le réseau virtuel Managed Instance ne doit PAS avoir de passerelle.  
- Le réseau virtuel Managed Instance doit utiliser des passerelles distantes.
- Le réseau virtuel homologué doit autoriser le transit par passerelle.

Ce scénario est illustré dans le diagramme suivant :

![peering d’applications intégrées](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>La fonctionnalité d’intégration au réseau virtuel n’intègre pas d’application à un réseau virtuel doté d’une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en mode de coexistence, l’intégration du réseau virtuel ne fonctionnera pas. S'il vous faut accéder aux ressources via une connexion ExpressRoute, vous pouvez utiliser un App Service Environment s’exécutant dans votre réseau virtuel.

## <a name="troubleshooting-connectivity-issues"></a>Résolution des problèmes de connectivité

Pour résoudre les problèmes de connectivité, lisez ce qui suit :

- Si vous ne parvenez pas à vous connecter à Managed Instance à partir d’une machine virtuelle Azure appartenant au même réseau virtuel mais à un sous-réseau différent, vérifiez qu’un groupe de sécurité réseau défini dans le sous-réseau de la machine virtuelle ne bloque pas l’accès. En outre, notez que vous devez ouvrir une connexion sortante sur le port SQL 1433, ainsi que des ports dans la plage 11000-11999, car ceux-ci sont nécessaires pour se connecter via une redirection à l’intérieur des limites Azure.
- Pour la table de routage associée au réseau virtuel, vérifiez que la propagation BGP est définie sur **Activé**.
- Si vous utilisez une connexion VPN point à site, accédez à la configuration dans le portail Azure pour voir si les sections **Entrée/Sortie** contiennent des chiffres. La présence de chiffres autres que zéro indiquent qu’Azure achemine le trafic entrant et sortant sur l’ordinateur local.

   ![Chiffres d’entrée et de sortie](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Vérifiez que la machine cliente (qui exécute le client VPN) affiche des entrées de routage pour tous les réseaux virtuels auxquels vous avez besoin d’accéder. Les itinéraires sont stockés dans `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Comme le montre cette image, il y a deux entrées pour chaque réseau virtuel impliqué, et une troisième entrée pour le point de terminaison VPN qui est configuré dans le portail.

   Une autre façon de vérifier les itinéraires est d’utiliser la commande suivante. La sortie montre les itinéraires vers les différents sous-réseaux :

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Si vous utilisez le peering VNet, suivez les instructions concernant la configuration des options [Autoriser le transit par passerelle et Utiliser des passerelles distantes](#connect-from-on-premises-with-vnet-peering).

- Si vous utilisez le Peering de réseaux virtuels pour connecter une application hébergée Azure App Service et que le réseau virtuel Managed Instance a une plage d’IP publiques, assurez-vous que les paramètres de votre application hébergée autorisent le routage du trafic sortant vers des réseaux IP publics. Suivez les instructions de la section [Intégration au réseau virtuel régional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Versions exigées de pilotes et d’outils

Les versions minimales suivantes des outils et des pilotes sont recommandées si vous voulez vous connecter à Managed Instance :

| Pilote/outil | Version |
| --- | --- |
|.NET Framework | 4.6.1 (ou .NET Core) |
|Pilote ODBC| v17 |
|Pilote PHP| 5.2.0 |
|Pilote JDBC| 6.4.0 |
|Pilote Node.js| 2.1.1 |
|Pilote OLEDB| 18.0.2.0 |
|SSMS| 18.0 ou [ultérieur](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) ou ultérieur |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’option Managed Instance, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md).
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md).
