---
title: Vue d’ensemble des réseaux - Serveur flexible Azure Database pour MySQL
description: Découvrir les options de connectivité et de réseau de l’option de déploiement Serveur flexible pour Azure Database pour MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: c83f36216e7488df94c372234d0541a4ee9f99b5
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492220"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Concepts de connectivité et de réseau pour Azure Database pour MySQL - Serveur flexible (préversion)

Cet article décrit les options de connectivité publique et privée disponibles pour votre serveur. Vous allez découvrir les détails des concepts de réseau d’un serveur flexible Azure Database pour MySQL afin de créer un serveur de façon sécurisée dans Azure.

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est en préversion.

## <a name="choosing-a-networking-option"></a>Choix d’une option réseau
Vous avez deux possibilités de réseau pour votre serveur flexible Azure Database pour MySQL. Vous avez le choix entre l’**accès privé (intégration au réseau virtuel)** et l’**accès public (adresses IP autorisées)** . Lors de la création du serveur, vous devez choisir une seule option. 

> [!NOTE]
> Vous ne pourrez pas changer votre option réseau une fois le serveur créé. 

* **Accès privé (intégration au réseau virtuel)**  : vous pouvez déployer votre serveur flexible sur votre [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Les ressources incluses sur un réseau virtuel peuvent communiquer par le biais d’adresses IP privées.

   Choisissez l’option d’intégration au réseau virtuel si vous voulez avoir les capacités suivantes :
   * Connexion à partir de ressources Azure du même réseau virtuel ou d’un [réseau virtuel appairé](../../virtual-network/virtual-network-peering-overview.md) à votre serveur flexible
   * Utilisation d’un VPN ou du service ExpressRoute pour vous connecter à partir de ressources non-Azure à votre serveur flexible
   * Aucun point de terminaison public

* **Accès public (adresses IP autorisées)**  : votre serveur flexible est accessible par le biais d’un point de terminaison public. Le point de terminaison public est une adresse DNS résolvable publiquement. L’expression « adresses IP autorisées » fait référence à une plage d’adresses IP que vous choisissez d’autoriser à accéder à votre serveur. Ces autorisations sont appelées **règles de pare-feu**. 

   Choisissez la méthode d’accès public si vous voulez avoir les capacités suivantes :
   * Connexion à partir de ressources Azure qui ne prennent pas en charge les réseaux virtuels
   * Connexion à partir de ressources situées en dehors d’Azure qui ne sont pas connectées par un VPN ou ExpressRoute 
   * Le serveur flexible a un point de terminaison public.

Les caractéristiques suivantes s’appliquent si vous choisissez d’utiliser l’option d’accès privé ou d’accès public :
* Les connexions à partir d’adresses IP autorisées ont besoin de s’authentifier auprès du serveur MySQL avec des informations d’identification valides.
* Le [chiffrement de la connexion](#tls-and-ssl) est disponible pour votre trafic réseau.
* Le serveur a un nom de domaine complet (FQDN). Pour la propriété du nom d’hôte dans les chaînes de connexion, nous vous recommandons d’utiliser le nom de domaine complet plutôt qu’une adresse IP.
* Les deux options contrôlent l’accès au niveau du serveur, pas au niveau de la base de données ni de la table. Vous pouvez utiliser les propriétés des rôles de MySQL pour contrôler l’accès à la base de données, à la table et à d’autres objets.


## <a name="private-access-vnet-integration"></a>Accès privé (intégration au réseau virtuel)
L’accès privé avec intégration au réseau virtuel offre des communications privées et sécurisées à votre serveur flexible MySQL.

### <a name="virtual-network-concepts"></a>Concepts de réseau virtuel
Voici quelques concepts à connaître quand vous utilisez des réseaux virtuels avec des serveurs flexibles MySQL.

* **Réseau virtuel** : Un réseau virtuel Azure contient un espace d’adressage IP privé configuré pour votre utilisation. Pour plus d’informations sur les réseaux virtuels Azure, consultez [Vue d’ensemble de Réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md).

    Votre réseau virtuel doit se trouver dans la même région Azure que votre serveur flexible.

* **Sous-réseau délégué** : un réseau virtuel contient des sous-réseaux. Les sous-réseaux vous permettent de segmenter votre réseau virtuel en espaces d’adressage plus petits. Les ressources Azure sont déployées sur des sous-réseaux spécifiques au sein d’un réseau virtuel. 

   Votre serveur flexible MySQL doit se trouver dans un sous-réseau **délégué** à l’usage de serveur flexible MySQL uniquement. Cette délégation spécifie que seuls les serveurs flexibles Azure Database pour MySQL peuvent utiliser ce sous-réseau. Aucun autre type de ressource Azure ne peut se trouver dans le sous-réseau délégué. Pour déléguer un sous-réseau, affectez à sa propriété de délégation la valeur Microsoft.DBforMySQL/flexibleServers.

* **Groupes de sécurité réseau (NSG)**  : les règles de sécurité dans les groupes de sécurité réseau permettent de filtrer le type de trafic qui peut circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel. Pour plus d’informations, consultez [Vue d’ensemble des groupes de sécurité réseau](../../virtual-network/network-security-groups-overview.md).

* **Peering de réseaux virtuels** Le peering de réseaux virtuels vous permet de connecter de manière fluide deux réseaux virtuels ou plus dans Azure. Les réseaux virtuels appairés apparaissent comme un seul réseau à des fins de connectivité. Le trafic entre les machines virtuelles des réseaux virtuels appairés utilise l'infrastructure principale de Microsoft. Le trafic entre l’application cliente et le serveur flexible dans les réseaux virtuels appairés est routé via le réseau privé de Microsoft uniquement et est dédié uniquement à ce réseau.

Le serveur flexible prend en charge le peering de réseaux virtuels au sein de la même région Azure. Le peering de réseaux virtuels entre régions **n’est pas pris en charge**. Pour plus d’informations, passez en revue les [concepts de peering de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>Connexion à partir de réseaux virtuels appairés dans la même région Azure
Si l’application cliente qui tente de se connecter au serveur flexible se trouve dans le réseau virtuel appairé, il est possible qu’elle ne puisse pas se connecter à l’aide du nom de serveur flexible, car elle ne peut pas résoudre le nom DNS du serveur flexible à partir du réseau virtuel appairé. Deux options permettent de résoudre ce problème :
* Utiliser une adresse IP privée (recommandé pour le scénario dev/test) : cette option peut être utilisée à des fins de développement ou de test. Vous pouvez utiliser nslookup pour la recherche inversée de l’adresse IP privée du nom de votre serveur flexible (nom de domaine complet) et utiliser une adresse IP privée pour vous connecter à partir de l’application cliente. L’utilisation de l’adresse IP privée pour la connexion au serveur flexible n’est pas recommandée pour une utilisation en production, car elle peut changer pendant un événement planifié ou non planifié.
* Utiliser une zone DNS privée (recommandé pour la production) : cette option est adaptée à des fins de production. Vous provisionnez une [zone DNS privée](../../dns/private-dns-getstarted-portal.md) et vous la liez à votre réseau virtuel client. Dans la zone DNS privée, vous ajoutez un [enregistrement A](../../dns/dns-zones-records.md#record-types) pour votre serveur flexible à l’aide de son adresse IP privée. Vous pouvez ensuite utiliser l’enregistrement A pour vous connecter à partir de l’application cliente dans un réseau virtuel appairé à un serveur flexible.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Connexion à partir d’un serveur local vers un serveur flexible dans un réseau virtuel à l’aide d’ExpressRoute ou d’un réseau VPN
Pour les charges de travail nécessitant l’accès à un serveur flexible dans un réseau virtuel à partir d’un réseau local, vous aurez besoin [d’ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) ou d’un réseau [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) et d’un réseau virtuel [connecté localement](/azure/architecture/reference-architectures/hybrid-networking/). Une fois cette configuration en place, vous aurez besoin d’un redirecteur DNS pour résoudre le nom de serveur flexible si vous souhaitez effectuer la connexion à partir d’une application cliente (comme MySQL Workbench) exécutée sur un réseau virtuel local. Ce redirecteur DNS est chargé de résoudre toutes les requêtes DNS via un redirecteur au niveau du serveur vers le service DNS fourni par Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Pour la configurer correctement, vous avez besoin des ressources suivantes :

- Réseau local
- Serveur flexible MySQL provisionné avec accès privé (intégration au réseau virtuel)
- Réseau virtuel [connecté à un site local](/azure/architecture/reference-architectures/hybrid-networking/)
- Utiliser le redirecteur DNS [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) déployé dans Azure

Vous pouvez ensuite utiliser le nom de domaine complet (FQDN) pour vous connecter à un serveur flexible à partir de l’application cliente dans un réseau virtuel appairé ou un réseau local.

### <a name="unsupported-virtual-network-scenarios"></a>Scénarios de réseau virtuel non pris en charge
* Point de terminaison public (ou adresse IP publique ou DNS) : un serveur flexible déployé sur un réseau virtuel ne peut pas avoir de point de terminaison public.
* Une fois le serveur flexible déployé sur un réseau virtuel et un sous-réseau, vous ne pouvez pas le déplacer vers un autre réseau virtuel ou sous-réseau. Vous ne pouvez pas déplacer le réseau virtuel vers un autre groupe de ressources ou abonnement.
* Il est impossible d’augmenter la taille d’un sous-réseau (espaces d’adressage) une fois qu’il existe des ressources sur ce sous-réseau.
* L’appairage (peering) de réseaux virtuels entre régions n’est pas pris en charge.

Découvrez comment activer l’accès privé (intégration au réseau virtuel) à l’aide du [portail Azure](how-to-manage-virtual-network-portal.md) ou d’[Azure CLI](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Si vous utilisez le serveur DNS personnalisé, vous devez utiliser un redirecteur DNS pour résoudre le nom de domaine complet du serveur Azure Database pour MySQL – Serveur flexible. Consultez [Résolution de noms utilisant votre propre serveur DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) pour en savoir plus.

## <a name="public-access-allowed-ip-addresses"></a>Accès public (adresses IP autorisées)
Les caractéristiques de la méthode d’accès public sont les suivantes :
* Seules les adresses IP que vous autorisez peuvent accéder à votre serveur flexible MySQL. Par défaut, aucune adresse IP n’est autorisée. Vous pouvez ajouter des adresses IP lors de la création du serveur ou par la suite.
* Votre serveur MySQL porte un nom DNS résolvable publiquement.
* Votre serveur flexible ne se trouve pas dans l’un de vos réseaux virtuels Azure.
* Le trafic réseau à destination et en provenance de votre serveur ne passe pas par un réseau privé. Le trafic utilise les chemins Internet généraux.

### <a name="firewall-rules"></a>Règles de pare-feu
L’octroi d’une autorisation à une adresse IP s’appelle une règle de pare-feu. Si une tentative de connexion provient d’une adresse IP que vous n’avez pas autorisée, le client d’origine voit une erreur.


### <a name="allowing-all-azure-ip-addresses"></a>Autorisation de toutes les adresses IP Azure
Si aucune adresse IP sortante fixe n’est disponible pour votre service Azure, vous pouvez envisager d’activer les connexions à partir de toutes les adresses IP du centre de données Azure.

> [!IMPORTANT]
> L’option **Autoriser l’accès public à partir des ressources et services Azure dans Azure** permet de configurer le pare-feu afin d’autoriser toutes les connexions en provenance d’Azure, notamment les connexions à partir des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.

Découvrez comment activer et gérer l’accès public (adresses IP autorisées) à l’aide du [portail Azure](how-to-manage-firewall-portal.md) ou d’[Azure CLI](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Résolution des problèmes d’accès public
Considérez les points suivants quand l’accès au service de serveur Microsoft Azure Database pour MySQL présente un comportement anormal :

* **Les modifications apportées à la liste d’approbation n’ont pas encore pris effet :** jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu du serveur de base de données Azure pour MySQL ne soient effectives.

* **Échec de l’authentification :** Si un utilisateur n’a pas d’autorisations sur le serveur Azure Database pour MySQL ou que le mot de passe est incorrect, la connexion au serveur Azure Database pour MySQL est refusée. La création d’un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur. Chaque client doit quand même fournir les informations d’identification de sécurité nécessaires.

* **Adresse IP de client dynamique :** Si vous avez une connexion Internet avec un adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :

   * Demandez à votre fournisseur de services Internet (ISP) la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur Azure Database pour MySQL, puis ajoutez cette plage à une règle de pare-feu.
   * Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez l’adresse IP statique en tant que règle de pare-feu.
  
* **La règle de pare-feu n’est pas disponible pour le format IPv6 :** Les règles de pare-feu doivent être au format IPv4. Si vous spécifiez des règles de pare-feu au format IPv6, l’erreur de validation s’affiche.


## <a name="hostname"></a>Nom d’hôte
Quelle que soit l’option réseau choisie, nous vous recommandons de toujours utiliser un nom de domaine complet (FQDN) comme nom d’hôte lors de la connexion à votre serveur flexible. Il n’est pas garanti que l’adresse IP du serveur reste statique. L’utilisation du nom de domaine complet vous permet d’éviter d’apporter des modifications à votre chaîne de connexion. 

Exemple
* Recommandé : `hostname = servername.mysql.database.azure.com`
* Dans la mesure du possible, évitez d’utiliser `hostname = 10.0.0.4` (adresse privée) ou `hostname = 40.2.45.67` (IP publique)


## <a name="tls-and-ssl"></a>TLS et SSL
Le serveur flexible Azure Database pour MySQL prend en charge la connexion de vos applications clientes au serveur MySQL en utilisant le protocole SSL (Secure Sockets Layer) avec le protocole TLS (Transport Layer Security). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité.

Le serveur flexible Azure Database pour MySQL prend en charge par défaut les connexions chiffrées à l’aide du protocole TLS 1.2 (Transport Layer Security), donc toutes les connexions entrantes qui utilisent les protocoles TLS 1.0 et TLS 1.1 sont refusées par défaut. Vous pouvez configurer et modifier la configuration de la mise en œuvre de la connexion chiffrée ou de la version TLS sur votre serveur flexible. 

Voici les différentes configurations des paramètres SSL et TLS possibles pour votre serveur flexible :

| Scénario   | Paramètres du serveur      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|Désactiver SSL (connexions chiffrées) | require_secure_transport = OFF |Si votre application héritée ne prend pas en charge les connexions chiffrées à MySQL Server, vous pouvez désactiver l’application des connexions chiffrées à votre serveur flexible en définissant require_secure_transport = OFF.|
|Appliquer le protocole SSL avec la version TLS < 1.2 | require_secure_transport = ON et tls_version = TLSV1 ou TLSV1.1| Si votre application héritée prend en charge les connexions chiffrées mais requiert la version TLS < 1.2, vous pouvez activer les connexions chiffrées, mais configurez votre serveur flexible pour autoriser les connexions avec la version TLS (v1.0 ou v1.1) prises en charge par votre application|
|Appliquer le protocole SSL avec la version TLS = 1.2 (configuration par défaut)|require_secure_transport = ON et tls_version = TLSV1.2| Il s’agit de la configuration par défaut recommandée pour le serveur flexible.|
|Appliquer le protocole SSL avec la version TLS = 1.3 (pris en charge avec MySQL v8.0 et versions ultérieures)| require_secure_transport = ON et tls_version = TLSV1.3| Cette fonctionnalité est utile et recommandée pour le développement de nouvelles applications|

> [!Note]
> Les modifications apportées au chiffrement SSL sur le serveur flexible ne sont pas prises en charge. Les suites de chiffrement FIPS sont appliquées par défaut quand tls_version est défini sur TLS version 1.2. Pour les versions TLS autres que la version 1.2, le chiffrement SSL est défini sur les paramètres par défaut qui sont fournis avec l’installation de MySQL Community.

Pour en savoir plus, passez en revue la rubrique sur la [connexion à l’aide du protocole SSL/TLS](how-to-connect-tls-ssl.md). 


## <a name="next-steps"></a>Étapes suivantes
* Découvrir comment activer l’accès privé (intégration au réseau virtuel) à l’aide du [portail Azure](how-to-manage-virtual-network-portal.md) ou d’[Azure CLI](how-to-manage-virtual-network-cli.md)
* Découvrir comment activer l’accès public (adresses IP autorisées) à l’aide du [portail Azure](how-to-manage-firewall-portal.md) ou d’[Azure CLI](how-to-manage-firewall-cli.md)
* Découvrir comment [utiliser le protocole TLS](how-to-connect-tls-ssl.md)
