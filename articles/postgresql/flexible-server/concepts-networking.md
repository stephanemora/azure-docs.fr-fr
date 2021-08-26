---
title: Vue d’ensemble des réseaux - Azure Database pour PostgreSQL - Serveur flexible
description: Découvrez les options de connectivité et de mise en réseau de l’option de déploiement Serveur flexible pour Azure Database pour PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2021
ms.openlocfilehash: 3d35eed46082d162afed5a2c9685265812b1e2d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525009"
---
# <a name="networking-overview-for-azure-database-for-postgresql---flexible-server-preview"></a>Vue d’ensemble de la mise en réseau d’Azure Database pour PostgreSQL – Serveur flexible (préversion)

Cet article décrit les concepts de connectivité et de réseau pour Azure Database pour PostgreSQL - Serveur flexible. L’option de déploiement Serveur flexible est en préversion.

Lorsque vous créez une instance Azure Database pour PostgreSQL – Serveur flexible (autrement dit, un *serveur flexible*), vous devez choisir l’une des options de mise en réseau suivantes : **Accès privé (intégration au réseau virtuel)** ou **Accès public (adresses IP autorisées)** . 

> [!NOTE]
> Vous ne pouvez pas modifier votre option de mise en réseau après la création du serveur. 

Les caractéristiques suivantes s’appliquent si vous choisissez d’utiliser l’option d’accès privé ou d’accès public :

* Les connexions à partir d’adresses IP autorisées doivent s’authentifier auprès du serveur PostgreSQL avec des informations d’identification valides.
* Le [chiffrement de la connexion](#tls-and-ssl) est appliqué pour votre trafic.
* Le serveur a un nom de domaine complet (FQDN). Pour la propriété `hostname` dans les chaînes de connexion, nous vous recommandons d’utiliser le FQDN plutôt qu’une adresse IP.
* Les deux options contrôlent l’accès au niveau du serveur, et non au niveau de la base de données ou de la table. Vous utiliserez les propriétés des rôles de PostgreSQL pour contrôler l’accès aux bases de données, aux tables et aux autres objets.

> [!NOTE]
> Azure Database pour PostgreSQL étant un service de base de données managée, les utilisateurs ne disposent pas d’un accès à l’hôte ou au système d’exploitation pour afficher ou modifier les fichiers config tels que `pg_hba.conf`. Le contenu de ces fichier est automatiquement mis à jour en fonction des paramètres du réseau.

## <a name="private-access-vnet-integration"></a>Accès privé (intégration au réseau virtuel)

Vous pouvez déployer un serveur flexible sur votre [réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md). Les réseaux virtuels Azure offrent des communications réseau privées et sécurisées. Les ressources d’un réseau virtuel peuvent communiquer via des adresses IP privées qui ont été attribuées sur ce réseau.

Choisissez cette option de mise en réseau si vous souhaitez bénéficier des capacités suivantes :

* Connexion à partir de ressources Azure du même réseau virtuel à votre serveur flexible à l’aide d’adresses IP privées.
* Utilisation d’un VPN ou du service Azure ExpressRoute pour vous connecter à partir de ressources non Azure à votre serveur flexible.
* Garantie que le serveur flexible n’a pas de point de terminaison public accessible via Internet.

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="Diagramme montrant le fonctionnement de l’appairage de réseaux virtuels, dont l’un comprend un serveur flexible.":::

Dans le diagramme ci-dessus :
- Les serveurs flexibles sont injectés dans le sous-réseau 10.0.1.0/24 du réseau virtuel VNet-1.
- Les applications qui sont déployées sur des sous-réseaux différents au sein du même réseau virtuel peuvent accéder directement aux serveurs flexibles.
- Les applications qui sont déployées sur un autre réseau virtuel (VNet-2) n’ont pas d’accès direct aux serveurs flexibles. Vous devez effectuer un [appairage de réseaux virtuels pour une zone DNS privée](#private-dns-zone-and-virtual-network-peering) avant qu’elles puissent accéder au serveur flexible.
   
### <a name="virtual-network-concepts"></a>Concepts de réseau virtuel

Un réseau virtuel Azure contient un espace d’adressage IP privé configuré pour votre usage. Votre réseau virtuel doit se trouver dans la même région Azure que votre serveur flexible. Pour en savoir plus sur les réseaux virtuels, consultez la [vue d’ensemble de Réseau virtuel Azure](../../virtual-network/virtual-networks-overview.md).

Voici quelques concepts à connaître lorsque vous utilisez des réseaux virtuels avec des serveurs flexibles PostgreSQL :

* **Sous-réseau délégué**. Un réseau virtuel contient des sous-réseaux. Les sous-réseaux vous permettent de segmenter votre réseau virtuel en espaces d’adressage plus petits. Les ressources Azure sont déployées sur des sous-réseaux spécifiques au sein d’un réseau virtuel. 

  Votre serveur flexible doit se trouver dans un sous-réseau *délégué*. Autrement dit, seules les instances d’Azure Database pour PostgreSQL – Serveur flexible peuvent utiliser ce sous-réseau. Aucun autre type de ressource Azure ne peut se trouver dans le sous-réseau délégué. Pour déléguer un sous-réseau, définissez sa propriété de délégation sur `Microsoft.DBforPostgreSQL/flexibleServers`.

  > [!IMPORTANT]
  > Les noms `AzureFirewallSubnet`, `AzureFirewallManagementSubnet`, `AzureBastionSubnet` et `GatewaySubnet` sont réservés à Azure. N’utilisez aucun d’entre eux comme nom de sous-réseau.

* **Groupe de sécurité réseau**. Les règles de sécurité dans les groupes de sécurité réseau permettent de filtrer le type de trafic qui peut circuler vers et depuis les interfaces réseau et les sous-réseaux de réseau virtuel. Pour plus d’informations, consultez la [présentation des groupes de sécurité réseau](../../virtual-network/network-security-groups-overview.md).

  Les groupes de sécurité d’application facilitent le contrôle de la sécurité de couche 4 en utilisant des groupes de sécurité réseau pour les réseaux plats. Vous pouvez rapidement :
  
  - Joindre des machines virtuelles à un groupe de sécurité d’application ou supprimer des machines virtuelles d’un groupe de sécurité d’application.
  - Appliquer dynamiquement des règles à ces machines virtuelles ou supprimer des règles de ces machines virtuelles. 
  
  Pour plus d’informations, consultez la [présentation des groupes de sécurité d’application](../../virtual-network/application-security-groups.md). 
  
  À ce stade, Azure Database pour PostgreSQL – Serveur flexible ne prend pas en charge les groupes de sécurité réseau dans lesquels un groupe de sécurité d’application fait partie de la règle. Nous conseillons actuellement d’utiliser le [filtrage de la source ou de la destination basé sur l’adresse IP](../../virtual-network/network-security-groups-overview.md#security-rules) dans un groupe de sécurité réseau. 

  > [!IMPORTANT]
  > Les fonctionnalités d’Azure Database pour PostgreSQL – Serveur flexible nécessitent la possibilité d’envoyer le trafic sortant vers les ports de destination 5432, 6432. Si vous créez des groupes de sécurité réseau pour refuser le trafic sortant de votre instance Azure Database pour PostgreSQL – Serveur flexible, veillez à autoriser le trafic vers ces ports de destination. 

* **Intégration d’une zone DNS privée**. L’intégration de zones DNS privées Azure permet de résoudre le DNS privé au sein du réseau virtuel actuel ou de tout réseau virtuel appairé dans la région où la zone DNS privée est liée. 

### <a name="using-a-private-dns-zone"></a>Utilisation d’une zone DNS privée

Si vous utilisez le portail Azure ou Azure CLI pour créer des serveurs flexibles avec un réseau virtuel, une nouvelle zone DNS privée est automatiquement approvisionnée pour chaque serveur de votre abonnement en utilisant le nom du serveur que vous avez fourni. 

Si vous utilisez une API Azure, un modèle Azure Resource Manager (modèle ARM) ou Terraform, créez des zones DNS privées qui se terminent par `postgres.database.azure.com`. Utilisez ces zones lors de la configuration de serveurs flexibles avec un accès privé. Pour plus d’informations, consultez la [présentation des zones DNS privées](../../dns/private-dns-overview.md).


### <a name="integration-with-a-custom-dns-server"></a>Intégration avec un serveur DNS personnalisé

Si vous utilisez un serveur DNS personnalisé, vous devez utiliser un redirecteur DNS pour résoudre le FQDN d’Azure Database pour PostgreSQL – Serveur flexible. L’adresse IP du redirecteur doit être [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md). 

Le serveur DNS personnalisé doit se trouver à l’intérieur du réseau virtuel ou être accessible via le paramètre de serveur DNS du réseau virtuel. Pour en savoir plus, consultez [Résolution de noms utilisant votre propre serveur DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="private-dns-zone-and-virtual-network-peering"></a>Zone DNS privée et appairage de réseaux virtuels

Les paramètres de zone DNS privée et l’appairage de réseaux virtuels sont indépendants les uns des autres. Si vous voulez vous connecter au serveur flexible depuis un client approvisionné dans un autre réseau virtuel de la même région ou d’une autre région, vous devez lier la zone DNS privée au réseau virtuel. Pour plus d’informations, consultez [Lier le réseau virtuel](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
> Seules les zones DNS privées dont le nom se termine par `postgres.database.azure.com` peuvent être liées.

### <a name="unsupported-virtual-network-scenarios"></a>Scénarios de réseau virtuel non pris en charge

Voici quelques limitations à l’utilisation de réseaux virtuels :

* Un serveur flexible déployé sur un réseau virtuel ne peut pas avoir de point de terminaison public (ou d’IP publique ou de DNS public).
* Une fois qu’un serveur flexible est déployé sur un réseau et un sous-réseau virtuels, vous ne pouvez pas le déplacer vers un autre réseau ou sous-réseau virtuel. Vous ne pouvez pas déplacer le réseau virtuel vers un autre groupe de ressources ou un autre abonnement.
* Il est impossible d’augmenter la taille d’un sous-réseau (espaces d’adressage) une fois que des ressources existent dans ce sous-réseau.
* Un serveur flexible ne prend pas en charge Azure Private Link. Au lieu de cela, il utilise l’injection de réseau virtuel pour rendre le serveur flexible disponible au sein d’un réseau virtuel. 


## <a name="public-access-allowed-ip-addresses"></a>Accès public (adresses IP autorisées)

Lorsque vous choisissez la méthode d’accès public, votre serveur flexible est accessible via un point de terminaison public sur Internet. Le point de terminaison public est une adresse DNS résolvable publiquement. L’expression *adresses IP autorisées* fait référence à une plage d’adresses IP à laquelle vous choisissez de donner l’autorisation d’accéder à votre serveur. Ces autorisations sont appelées *règles de pare-feu*. 

Choisissez cette option de mise en réseau si vous souhaitez bénéficier des capacités suivantes :

* Connexion à partir de ressources Azure qui ne prennent pas en charge les réseaux virtuels.
* Connexion à partir de ressources situées en dehors d’Azure qui ne sont pas connectées par un VPN ou un circuit ExpressRoute.
* Garantie que le serveur flexible n’a pas de point de terminaison public accessible via Internet.

Les caractéristiques de la méthode d’accès public sont les suivantes :

* Seules les adresses IP que vous autorisez peuvent accéder à votre serveur flexible PostgreSQL. Par défaut, aucune adresse IP n’est autorisée. Vous pouvez ajouter des adresses IP lors de la création du serveur ou par la suite.
* Votre serveur PostgreSQL a un nom DNS résoluble publiquement.
* Votre serveur flexible ne se trouve pas sur l’un de vos réseaux virtuels Azure.
* Le trafic réseau à destination et en provenance de votre serveur ne passe pas par un réseau privé. Le trafic utilise les chemins Internet généraux.

### <a name="firewall-rules"></a>Règles de pare-feu

Si une tentative de connexion provient d’une adresse IP que vous n’avez pas autorisée par le biais d’une règle de pare-feu, le client d’origine recevra une erreur.

### <a name="allowing-all-azure-ip-addresses"></a>Autorisation de toutes les adresses IP Azure

Si aucune adresse IP sortante fixe n’est disponible pour votre service Azure, vous pouvez envisager d’autoriser les connexions depuis toutes les adresses IP pour les centres de données Azure.

> [!IMPORTANT]
> L’option **Autoriser l’accès public à partir des ressources et services Azure dans Azure** permet de configurer le pare-feu afin d’autoriser toutes les connexions en provenance d’Azure, notamment les connexions à partir des abonnements d’autres clients. Lorsque vous sélectionnez cette option, assurez-vous que vos autorisations de connexion et d’utilisation limitent l’accès aux seuls utilisateurs autorisés.

### <a name="troubleshooting-public-access-issues"></a>Résolution des problèmes d’accès public
Considérez les points suivants lorsque l’accès au service Azure Database pour PostgreSQL ne se comporte pas comme vous le souhaitez :

* **Les modifications apportées à la liste d’autorisation n’ont pas encore pris effet**. Jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu du serveur Azure Database pour PostgreSQL prennent effet.

* **Échec de l’authentification**. Si un utilisateur n’a pas d’autorisation sur le serveur Azure Database pour PostgreSQL ou que le mot de passe est incorrect, la connexion au serveur Azure Database pour PostgreSQL est refusée. La création d’un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur. Chaque client doit quand même fournir les informations d’identification de sécurité nécessaires.

* **L’adresse IP dynamique du client empêche l’accès**. Si vous avez une connexion Internet avec un adressage IP dynamique et que le pare-feu demeure infranchissable, essayez l’une des solutions suivantes :

   * Demandez à votre fournisseur d’accès à Internet la plage d’adresses IP attribuée à vos ordinateurs clients qui accèdent au serveur Azure Database pour PostgreSQL. Ajoutez ensuite cette plage dans une règle de pare-feu.
   * Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez l’adresse IP statique en tant que règle de pare-feu.

* **La règle de pare-feu n’est pas disponible au format IPv6**. Les règles de pare-feu doivent être au format IPv4. Si vous spécifiez des règles de pare-feu au format IPv6, vous obtiendrez une erreur de validation.

## <a name="host-name"></a>Nom de l’hôte

Quelle que soit l’option de mise en réseau choisie, nous vous recommandons de toujours utiliser un FQDN comme nom d’hôte lorsque vous vous connectez à votre serveur flexible. Il n’est pas garanti que l’adresse IP du serveur reste statique. L’utilisation du nom de domaine complet vous permet d’éviter d’apporter des modifications à votre chaîne de connexion. 

Par exemple, `hostname = servername.postgres.database.azure.com` utilise un FQDN comme nom d’hôte. Dans la mesure du possible, évitez d’utiliser `hostname = 10.0.0.4` (adresse privée) ou `hostname = 40.2.45.67` (adresse publique).


## <a name="tls-and-ssl"></a>TLS et SSL

Azure Database pour PostgreSQL – Serveur flexible impose la connexion de vos applications clientes au service PostgreSQL à l’aide du protocole TLS. TLS est un protocole standard du secteur qui garantit des connexions réseau chiffrées entre votre serveur de base de données et vos applications clientes. Il s’agit d’une mise à jour du protocole SSL (Secure Sockets Layer). 

Azure Database pour PostgreSQL prend en charge TLS 1.2 et les versions ultérieures. Dans la norme [RFC 8996](https://datatracker.ietf.org/doc/rfc8996/), l’organisation Internet Engineering Task Force (IETF) stipule explicitement que les protocoles TLS 1.0 et TLS 1.1 ne doivent pas être utilisés. Ces deux protocoles sont déconseillés depuis la fin de l’année 2019.

Toutes les connexions entrantes qui utilisent des versions antérieures du protocole TLS, comme TLS 1.0 et TLS 1.1, seront refusées.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer un serveur flexible en utilisant l’option **Accès privé (intégration au réseau virtuel)** dans le [portail Azure](how-to-manage-virtual-network-portal.md) ou [Azure CLI](how-to-manage-virtual-network-cli.md).
* Découvrez comment créer un serveur flexible en utilisant l’option **Accès public (adresses IP autorisées)** dans le [portail Azure](how-to-manage-firewall-portal.md) ou [Azure CLI](how-to-manage-firewall-cli.md).
