---
title: Architecture de connectivité pour une instance gérée dans la base de données SQL Azure | Microsoft Docs
description: En savoir plus sur Azure SQL Database managed instance communication et architecture de connectivité, ainsi que la manière dont les composants de dirigent le trafic vers l’instance gérée.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 82b533f7293e00469a5b92b02e8d58967379a585
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59497064"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architecture de connectivité pour une instance gérée dans la base de données SQL Azure

Cet article explique la communication au sein d’une instance managée de base de données SQL Azure. Elle décrit également l’architecture de connectivité et la façon dont les composants de dirigent le trafic vers l’instance gérée.  

L’instance gérée de la base de données SQL est placé dans le réseau virtuel Azure et le sous-réseau dédié pour les instances gérées. Ce déploiement fournit :

- Une adresse IP privée sécurisée.
- La possibilité de connecter un réseau local à une instance gérée.
- La capacité à connecter une instance gérée sur un serveur lié ou un autre en local magasin de données.
- La possibilité de connecter une instance gérée aux ressources Azure.

## <a name="communication-overview"></a>Vue d’ensemble des communications

Le diagramme suivant montre les entités qui se connectent à une instance gérée. Il montre également les ressources qui doivent communiquer avec l’instance gérée. Le processus de communication en bas du diagramme représente les applications des clients et des outils qui se connectent à l’instance gérée en tant que sources de données.  

![Entités dans l’architecture de connectivité](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Une instance gérée est une plateforme en tant qu’une offre de service (PaaS). Microsoft utilise des agents automatisés (gestion, déploiement et la maintenance) pour gérer ce service basé sur les flux de données de télémétrie. Étant donné que Microsoft est responsable de la gestion, les clients ne peut pas accéder aux ordinateurs du cluster virtuel instance gérée via protocole RDP (Remote Desktop).

Certaines opérations lancées par les utilisateurs finaux ou les applications peuvent nécessiter de SQL Server managed instances pour interagir avec la plateforme. Un cas est la création d’une base de données d’instance gérée. Cette ressource est exposée via le portail Azure, PowerShell, Azure CLI et l’API REST.

Instances managées dépendent des services Azure tels que le stockage Azure pour les sauvegardes, Azure Event Hubs pour les données de télémétrie, Azure Active Directory pour l’authentification, Azure Key Vault pour Transparent Data Encryption (TDE) et deux services de plateforme Azure qui fournissent fonctionnalités de sécurité et de prise en charge. Les instances gérées établit des connexions à ces services.

Toutes les communications sont chiffrées et signés à l’aide de certificats. Pour vérifier la fiabilité des correspondants, managé instances vérifier en permanence ces certificats par le biais des listes de révocation de certificats. Si les certificats sont révoqués, l’instance gérée ferme les connexions pour protéger les données.

## <a name="high-level-connectivity-architecture"></a>Architecture de la connectivité globale

À un niveau élevé, une instance gérée est un ensemble de composants de service. Ces composants sont hébergés sur un ensemble dédié de machines virtuelles isolés qui s’exécutent au sein du sous-réseau de réseau virtuel du client. Ces ordinateurs forment un cluster virtuel.

Un cluster virtuel peut héberger plusieurs instances gérées. Si nécessaire, le cluster se développe automatiquement ou les contrats lorsque le client change le nombre d’instances approvisionnés dans le sous-réseau.

Les applications client peuvent se connecter à des instances gérées et peuvent interroger et mettre à jour des bases de données à l’intérieur du réseau virtuel, un réseau virtuel homologué, ou un réseau connecté par VPN ou Azure ExpressRoute. Ce réseau doit utiliser un point de terminaison et une adresse IP privée.  

![Diagramme d’architecture de connectivité](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Services de gestion et le déploiement Microsoft s’exécutent en dehors du réseau virtuel. Une instance gérée et les services de Microsoft se connectent via les points de terminaison qui ont des adresses IP publiques. Quand une instance gérée crée une connexion sortante, côté réception permet de traduction d’adresses réseau (NAT) le ressemble connexion provient cette adresse IP publique.

Le trafic de gestion transite par réseau virtuel du client. Cela signifie que les éléments de l’infrastructure du réseau virtuel peuvent endommager le trafic de gestion en rendant l’instance échouent et la rendre indisponible.

> [!IMPORTANT]
> Pour améliorer l’expérience et la disponibilité des services, Microsoft applique une stratégie intentionnelle de réseau sur les éléments d’infrastructure de réseau virtuel Azure. La stratégie peut affecter le fonctionnement de l’instance gérée. Ce mécanisme de plateforme communique en toute transparence la configuration réseau requise pour les utilisateurs. Objectif principal de la stratégie est afin d’éviter une mauvaise configuration du réseau et pour assurer le fonctionnement de l’instance managée normale. Lorsque vous supprimez une instance gérée, la stratégie d’intention de réseau est également supprimée.

## <a name="virtual-cluster-connectivity-architecture"></a>Architecture de la connectivité du cluster virtuel

Prenons plus en détail dans l’architecture de connectivité pour les instances gérées. Le diagramme suivant montre l’organisation conceptuelle du cluster virtuel.

![Architecture de connectivité du cluster virtuel](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Les clients se connectent à une instance gérée à l’aide d’un nom d’hôte qui se présente sous la forme `<mi_name>.<dns_zone>.database.windows.net`. Ce nom d’hôte correspond à une adresse IP privée, bien qu’il est inscrit dans une zone de nom (DNS) de domaine public et peut être résolu publiquement. Le `zone-id` est généré automatiquement lorsque vous créez le cluster. Si un nouveau cluster héberge une instance gérée secondaire, il partage son ID de zone avec le cluster principal. Pour plus d’informations, consultez [utiliser des groupes de basculement automatique pour permettre un basculement transparent et coordonné de plusieurs bases de données](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Cette adresse IP privée appartient à l’équilibreur de charge interne de l’instance gérée. L’équilibreur de charge dirige le trafic vers passerelle de l’instance gérée. Plusieurs instances gérées pouvant s’exécuter à l’intérieur du même cluster, la passerelle utilise le nom d’hôte de l’instance gérée à rediriger le trafic vers le service de moteur SQL correct.

Les services de gestion et le déploiement se connectent à une instance gérée à l’aide un [point de terminaison de gestion](#management-endpoint) équilibreur de charge est mappé à un externe. Le trafic est acheminé vers les nœuds uniquement si elle est reçue sur un ensemble prédéfini de ports qui utilisent uniquement les composants de gestion de l’instance gérée. Un pare-feu intégré sur les nœuds est configuré pour autoriser le trafic uniquement à partir de plages d’adresses IP de Microsoft. Certificats authentifient mutuellement toutes les communications entre les composants de gestion et le plan de gestion.

## <a name="management-endpoint"></a>Point de terminaison de gestion

Microsoft gère l’instance gérée à l’aide d’un point de terminaison de gestion. Ce point de terminaison est à l’intérieur du cluster virtuel de l’instance. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau. Sur le niveau de l’application, il est protégé par la vérification de mutuelle des certificats. Pour rechercher l’adresse du point de terminaison, consultez [déterminer l’adresse du point de terminaison gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Lorsque connexions commencent à l’intérieur de l’instance gérée (comme avec les sauvegardes et les journaux d’audit), le trafic s’affiche pour démarrer à partir de l’adresse IP du point de terminaison gestion. Vous pouvez limiter l’accès aux services publics à partir d’une instance gérée en définissant des règles de pare-feu pour autoriser uniquement les adresse IP de l’instance gérée. Pour plus d’informations, consultez [Vérifiez le pare-feu intégré de l’instance gérée](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Le trafic qui accède à des services Azure qui se trouvent dans la région de l’instance managée est optimisé et pour cette raison pas transformée en adresse IP publique d’instance managées management point de terminaison. C’est pourquoi si vous devez utiliser des règles de pare-feu basé sur IP, plus souvent pour le stockage, service doit être dans une autre région à partir de l’instance gérée.

## <a name="network-requirements"></a>Configuration requise pour le réseau

Déployer une instance gérée dans un sous-réseau dédié à l’intérieur du réseau virtuel. Le sous-réseau doit avoir les caractéristiques suivantes :

- **Sous-réseau dédié :** Sous-réseau de l’instance managée ne peut pas contenir n’importe quel autre service cloud qui est associé, et il ne peut pas être un sous-réseau de passerelle. Le sous-réseau ne peut pas contenir n’importe quelle ressource, mais l’instance gérée, et vous ne pouvez pas ajouter ultérieurement de ressources dans le sous-réseau.
- **Groupe de sécurité réseau :** Un groupe de sécurité réseau associé avec le réseau virtuel doit définir [règles de sécurité entrantes](#mandatory-inbound-security-rules) et [règles de sécurité sortantes](#mandatory-outbound-security-rules) avant toute autre règle. Vous pouvez utiliser un groupe de sécurité réseau pour contrôler l’accès au point de terminaison de données de l’instance gérée en filtrant le trafic sur le port 1433 et ports 11000-11999 lors de l’instance managée est configuré pour rediriger les connexions.
- **Table d’itinéraire défini (UDR) utilisateur :** Une table d’UDR associé avec le réseau virtuel doit inclure spécifique [entrées](#user-defined-routes).
- **Aucun point de terminaison de service :** Aucun point de terminaison de service ne doit être associé à sous-réseau de l’instance gérée. Assurez-vous que l’option de points de terminaison de service est désactivée lorsque vous créez le réseau virtuel.
- **Suffisamment d’adresses IP :** Le sous-réseau de l’instance managée doit avoir au moins 16 adresses IP. La valeur minimale recommandée est de 32 adresses IP. Pour plus d’informations, consultez [déterminer la taille du sous-réseau pour les instances gérées](sql-database-managed-instance-determine-size-vnet-subnet.md). Vous pouvez déployer des instances gérées dans [le réseau existant](sql-database-managed-instance-configure-vnet-subnet.md) après avoir configuré les satisfaire [la configuration réseau requise pour les instances gérées](#network-requirements). Sinon, créez un [nouveau réseau et sous-réseau](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Vous ne pouvez pas déployer une nouvelle instance managée si le sous-réseau de destination ne dispose pas de ces caractéristiques. Lorsque vous créez une instance gérée, une stratégie intentionnelle de réseau est appliquée sur le sous-réseau pour empêcher les modifications non conformes au programme d’installation de mise en réseau. Une fois que la dernière instance est supprimée à partir du sous-réseau, la stratégie d’intention de réseau est également supprimée.

### <a name="mandatory-inbound-security-rules"></a>Règles de sécurité du trafic entrant obligatoires

| Nom       |Port                        |Protocole|Source           |Destination|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestion  |9000, 9003, 1438, 1440, 1452|TCP     |Quelconque              |SOUS-RÉSEAU MI  |AUTORISER |
|mi_subnet   |Quelconque                         |Quelconque     |SOUS-RÉSEAU MI        |SOUS-RÉSEAU MI  |AUTORISER |
|health_probe|Quelconque                         |Quelconque     |AzureLoadBalancer|SOUS-RÉSEAU MI  |AUTORISER |

### <a name="mandatory-outbound-security-rules"></a>Règles de sécurité du trafic sortant obligatoires

| Nom       |Port          |Protocole|Source           |Destination|Action|
|------------|--------------|--------|-----------------|-----------|------|
|gestion  |80, 443, 12000|TCP     |SOUS-RÉSEAU MI        |AzureCloud |AUTORISER |
|mi_subnet   |Quelconque           |Quelconque     |SOUS-RÉSEAU MI        |SOUS-RÉSEAU MI  |AUTORISER |

> [!IMPORTANT]
> Vérifiez qu’il n'existe qu’une seule règle de trafic entrant pour les ports 9000, 9003, 1438, 1440, 1452 et une règle de trafic sortant pour les ports 80, 443, 12000. Managed Instance approvisionnement via Azure Resource Manager déploiements échouent si les règles de trafic entrants et de sortie sont configurés séparément pour chaque port. Si ces ports sont dans des règles distinctes, le déploiement échoue avec le code d’erreur `VnetSubnetConflictWithIntendedPolicy`

\* MI sous-réseau fait référence à la plage d’adresses IP pour le sous-réseau dans le formulaire 10.x.x.x/y. Vous pouvez trouver ces informations dans le portail Azure, dans les propriétés du sous-réseau.

> [!IMPORTANT]
> Bien que les règles de sécurité de trafic entrant requise autorisent le trafic à partir de _tout_ de code source sur les ports 9000, 9003, 1438, 1440 et 1452, ces ports sont protégés par un pare-feu intégré. Pour plus d’informations, consultez [déterminer l’adresse de point de terminaison de gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Si vous utilisez la réplication transactionnelle dans une instance gérée, et si vous utilisez une base de données d’instance comme un serveur de publication ou un serveur de distribution, ouvrez le port 445 (TCP sortant) dans les règles de sécurité du sous-réseau. Ce port autorise l’accès au partage de fichiers Azure.

### <a name="user-defined-routes"></a>itinéraires définis par l’utilisateur

|Nom|Préfixe de l’adresse|Tronçon suivant|
|----|--------------|-------|
|subnet_to_vnetlocal|SOUS-RÉSEAU MI|Réseau virtuel|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

En outre, vous pouvez ajouter des entrées à la table de routage pour acheminer le trafic qui a des plages d’adresses IP privées en local en tant que destination via la passerelle de réseau virtuel ou d’une appliance de réseau virtuel (NVA).

Si le réseau virtuel comprend un DNS personnalisé, le serveur DNS personnalisé doit être en mesure de résoudre les noms d’hôte en \*. core.windows.net zone. À l’aide des fonctionnalités supplémentaires telles que l’authentification Azure AD peut nécessiter la résolution de noms de domaine complets supplémentaires. Pour plus d’informations, consultez [configurer un DNS personnalisé](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble, consultez [fonctions avancées de sécurité des données de la base de données SQL](sql-database-managed-instance.md).
- Découvrez comment [configurer un réseau virtuel Azure](sql-database-managed-instance-create-vnet-subnet.md) ou un [réseau virtuel Azure existant](sql-database-managed-instance-configure-vnet-subnet.md) où vous pouvez déployer les instances gérées.
- [Calculer la taille du sous-réseau](sql-database-managed-instance-determine-size-vnet-subnet.md) où vous souhaitez déployer les instances gérées.
- Découvrez comment créer une instance managée :
  - À partir du [portail Azure](sql-database-managed-instance-get-started.md).
  - À l’aide de [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - À l’aide de [un modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - À l’aide de [un modèle Azure Resource Manager (à l’aide du serveur de rebond, avec SSMS inclus)](https://portal.azure.com/). 
