---
title: Azure Private Link pour Azure Database pour MySQL (préversion)
description: Découvrez le fonctionnement d’Azure Private Link avec Azure Database pour MySQL (préversion).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d9738a1dca39e1b43f690bd65ff05d20b6a94fa1
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898925"
---
# <a name="private-link-for-azure-database-for-mysql-preview"></a>Azure Private Link pour Azure Database pour MySQL (préversion)

Azure Private Link vous permet de vous connecter à différents services PaaS dans Azure par le biais d’un point de terminaison privé. Azure Private Link intègre essentiellement les services Azure à votre Réseau virtuel privé. Vous pouvez accéder aux ressources PaaS à l’aide de l’adresse IP privée, comme toute autre ressource dans le réseau virtuel.

Pour obtenir la liste des services PaaS prenant en charge la fonctionnalité Azure Private Link, consultez la [documentation](https://docs.microsoft.com/azure/private-link/index) Azure Private Link. Un point de terminaison privé est une adresse IP privée au sein d’un [réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) et d’un sous-réseau spécifiques.

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions Azure où Azure Database pour MySQL prend en charge les niveaux tarifaires Usage général et Mémoire optimisée.

## <a name="data-exfiltration-prevention"></a>Prévention de l’exfiltration de données

L’exfiltration de données dans Azure Database pour MySQL se produit quand un utilisateur autorisé, tel qu’un administrateur de base de données, est en mesure d’extraire des données d’un système et de les déplacer vers un autre emplacement ou système en dehors de l’organisation. C’est par exemple le cas quand un utilisateur déplace des données vers un compte de stockage détenu par un tiers.

Imaginez un scénario avec un utilisateur exécutant MySQL Workbench à l’intérieur d’une machine virtuelle Azure qui se connecte à un serveur Azure Database pour MySQL approvisionné dans la région USA Ouest. L’exemple ci-dessous montre comment utiliser des contrôles d’accès réseau pour limiter l’accès à Azure Database pour MySQL par le biais de points de terminaison publics.

* Désactivez tout le trafic des services Azure à destination d’Azure Database pour MySQL par le biais du point de terminaison public en désactivant l’option *Autoriser les services Azure*. Assurez-vous qu’aucune adresse IP ou plage n’est autorisée à accéder au serveur via des [règles de pare-feu](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) ou des [points de terminaison de service de réseau virtuel](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Autorisez uniquement le trafic à destination d’Azure Database pour MySQL utilisant l’adresse IP privée de la machine virtuelle. Pour plus d’informations, consultez les articles sur le [point de terminaison de service](concepts-data-access-and-security-vnet.md) et les [règle de pare-feu du réseau virtuel](howto-manage-vnet-using-portal.md).

* Sur la machine virtuelle Azure, limitez l’étendue de la connexion sortante à l’aide de groupes de sécurité réseau (NSG) et d’étiquettes de service comme suit.

    * Spécifiez une règle NSG pour autoriser le trafic pour *Service Tag = SQL.WestUs* (autorise uniquement la connexion à Azure Database pour MySQL dans la région USA Ouest)
    * Spécifiez une règle NSG (avec une priorité plus élevée) pour refuser le trafic pour *Service Tag = SQL* (refuse les connexions à Mettre à jour vers Azure Database pour MySQL dans toutes les régions)</br></br>

À la fin de cette configuration, la machine virtuelle Azure peut uniquement se connecter à Azure Database pour MySQL dans la région USA Ouest. Toutefois, la connectivité n’est pas limitée à une seule instance Azure Database pour MySQL. La machine virtuelle peut toujours se connecter à toute instance Azure Database pour MySQL de la région USA Ouest, même si elle ne fait pas partie de l’abonnement. Bien que nous ayons réduit l’étendue de l’exfiltration de données dans le scénario ci-dessus à une région spécifique, nous ne l’avons pas encore totalement éliminée.</br>

Grâce à Azure Private Link, vous pouvez désormais configurer des contrôles d’accès réseau comme des groupes de sécurité réseau pour restreindre l’accès au point de terminaison privé. Les ressources Azure PaaS individuelles sont ensuite mappées à des points de terminaison privés spécifiques. Un utilisateur interne malveillant peut uniquement accéder à la ressource PaaS mappée (par exemple, une instance Azure Database pour MySQL). Il n’a accès à aucune autre ressource.

## <a name="on-premises-connectivity-over-private-peering"></a>Connectivité locale sur un appairage privé

Quand vous vous connectez au point de terminaison public à partir de machines locales, votre adresse IP doit être ajoutée au pare-feu IP à l’aide d’une règle de pare-feu au niveau du serveur. Bien que ce modèle fonctionne bien pour autoriser l’accès à des machines individuelles pour des charges de travail de développement ou de test, il est difficile à gérer dans un environnement de production.

Grâce à Azure Private Link, vous pouvez activer l’accès entre différents locaux au point de terminaison privé en utilisant [ExpressRoute](https://azure.microsoft.com/services/expressroute/) (ER), un Peering privé ou un [tunnel VPN](https://docs.microsoft.com/azure/vpn-gateway/). Vous pouvez ensuite désactiver tous les accès via le point de terminaison public et ne pas utiliser le pare-feu IP.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Configurer Azure Private Link pour Azure Database pour MySQL

### <a name="creation-process"></a>Processus de création

Des points de terminaison privés sont requis pour activer Azure Private Link. Pour ce faire, vous pouvez utiliser les guides pratiques suivants.

* [Azure portal](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [INTERFACE DE LIGNE DE COMMANDE](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processus d’approbation
Une fois que l’administrateur réseau a créé le point de terminaison privé (PE), l’administrateur MySQL peut gérer la connexion de point de terminaison privé (PEC) à Azure Database pour MySQL.

> [!NOTE]
> Actuellement, Azure Database pour MySQL prend uniquement en charge l’approbation automatique pour le point de terminaison privé.

* Accédez à la ressource de serveur Azure Database pour MySQL dans le Portail Azure. 
    * Sélectionnez les connexions de point de terminaison privé dans le volet gauche
    * Liste de toutes les connexions de point de terminaison privé (PEC)
    * Point de terminaison privé (PE) correspondant créé

![sélectionnez le portail de point de terminaison privé](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Sélectionnez un PEC dans la liste.

![sélectionnez l’approbation en attente de point de terminaison privé](media/concepts-data-access-and-security-private-link/select-private-link.png)

* L’administrateur du serveur MySQL peut choisir d’approuver ou de rejeter un PEC. Il peut aussi ajouter une brève réponse sous forme de texte.

![sélectionnez le message de point de terminaison privé](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Après approbation ou rejet, la liste reflète l’état approprié et le texte de réponse

![sélectionnez l’état final du point de terminaison privé](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Cas d’usage d’Azure Private Link pour Azure Database pour MySQL

Les clients peuvent se connecter au point de terminaison privé à partir du même réseau virtuel, d’un réseau virtuel appairé dans la même région ou par le biais d’une connexion réseau virtuel à réseau virtuel entre régions. Les clients peuvent également se connecter localement avec ExpressRoute, un appairage privé ou un tunneling VPN. Vous trouverez ci-dessous un diagramme simplifié montrant les cas d’usage courants.

![sélectionnez la vue d’ensemble du point de terminaison privé](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connexion à partir d’une machine virtuelle Azure dans un réseau virtuel appairé
Configurez le [Peering de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) pour établir la connectivité à Azure Database pour MySQL à partir d’une machine virtuelle Azure dans un réseau virtuel appairé.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connexion à partir d’une machine virtuelle Azure dans un environnement de réseau virtuel à réseau virtuel
Configurez une [connexion de passerelle VPN de réseau virtuel à réseau virtuel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) pour établir la connectivité à une instance Azure Database pour MySQL à partir d’une machine virtuelle Azure dans une autre région ou un autre abonnement.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connexion à partir d’un environnement local sur un VPN
Pour établir la connectivité entre un environnement local et Azure Database pour MySQL, choisissez et implémentez l’une des options suivantes :

* [Connexion point à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Connexion VPN de site à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuit ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Private Link combiné à des règles de pare-feu

Les situations et résultats suivants sont possibles lorsque vous utilisez Private Link en association avec des règles de pare-feu :

* Si vous ne configurez pas de règles de pare-feu, par défaut, aucun trafic ne pourra accéder à Azure Database pour MySQL.

* Si vous configurez un trafic public ou un point de terminaison de service et que vous créez des points de terminaison privés, différents types de trafic entrant sont alors autorisés par le type de règle de pare-feu correspondant.

* Si vous ne configurez aucun trafic public ni point de terminaison de service et que vous créez des points de terminaison privés, Azure Database pour MySQL est alors uniquement accessible via les points de terminaison privés. Si vous ne configurez aucun trafic public ni point de terminaison de service, après le rejet ou la suppression de tous les points de terminaison privés approuvés, aucun trafic ne sera en mesure d’accéder à Azure Database pour MySQL.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité d’Azure Database pour MySQL, consultez les articles suivants :

* Pour configurer un pare-feu pour Azure Database pour MySQL, consultez [Prise en charge du pare-feu](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Pour découvrir comment configurer un point de terminaison de service de réseau virtuel pour Azure Database pour MySQL, consultez [Configurer l’accès à partir de réseaux virtuels](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet).

* Pour obtenir une vue d’ensemble de la connectivité Azure Database pour MySQL, consultez [Architecture de connectivité Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture).
