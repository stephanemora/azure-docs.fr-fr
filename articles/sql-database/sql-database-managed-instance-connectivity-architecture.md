---
title: Architecture de connectivité pour une instance gérée dans la base de données SQL Azure | Microsoft Docs
description: En savoir plus sur Azure SQL Database managed instance communication et architecture de connectivité, ainsi que la manière dont les composants de dirigent le trafic vers l’instance gérée.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 6ef020ff1054416e2b9af5af824b9aa27f0b1e64
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247237"
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

Instances managées dépendent des services Azure tels que le stockage Azure pour les sauvegardes, Azure Service Bus pour la télémétrie, Azure Active Directory pour l’authentification et d’Azure Key Vault pour Transparent Data Encryption (TDE). Les instances gérées établissent des connexions à ces services.

Toutes les communications utilisent des certificats pour le chiffrement et signature. Pour vérifier la fiabilité des correspondants, managé instances vérifier en permanence ces certificats en contactant une autorité de certification. Si les certificats sont révoqués ou ne peut pas être vérifiées, l’instance gérée ferme les connexions pour protéger les données.

## <a name="high-level-connectivity-architecture"></a>Architecture de la connectivité globale

À un niveau élevé, une instance gérée est un ensemble de composants de service. Ces composants sont hébergés sur un ensemble dédié de machines virtuelles isolés qui s’exécutent au sein du sous-réseau de réseau virtuel du client. Ces ordinateurs forment un cluster virtuel.

Un cluster virtuel peut héberger plusieurs instances gérées. Si nécessaire, le cluster se développe automatiquement ou les contrats lorsque le client change le nombre d’instances approvisionnés dans le sous-réseau.

Les applications client peuvent se connecter à des instances gérées et peuvent interroger et bases de données de mise à jour uniquement si elles s’exécutent au sein du réseau virtuel, virtuel homologué réseau, ou connecté par VPN ou Azure ExpressRoute. Ce réseau doit utiliser un point de terminaison et une adresse IP privée.  

![Diagramme d’architecture de connectivité](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Services de gestion et le déploiement Microsoft s’exécutent en dehors du réseau virtuel. Une instance gérée et les services de Microsoft se connectent via les points de terminaison qui ont des adresses IP publiques. Quand une instance gérée crée une connexion sortante, côté réception permet de traduction d’adresses réseau (NAT) le ressemble connexion provient cette adresse IP publique.

Le trafic de gestion transite par réseau virtuel du client. Cela signifie que les éléments de l’infrastructure du réseau virtuel peuvent endommager le trafic de gestion en rendant l’instance échouent et la rendre indisponible.

> [!IMPORTANT]
> Pour améliorer l’expérience et la disponibilité des services, Microsoft applique une stratégie intentionnelle de réseau sur les éléments d’infrastructure de réseau virtuel Azure. La stratégie peut affecter le fonctionnement de l’instance gérée. Ce mécanisme de plateforme communique en toute transparence la configuration réseau requise pour les utilisateurs. Objectif principal de la stratégie est afin d’éviter une mauvaise configuration du réseau et pour assurer le fonctionnement de l’instance managée normale. Lorsque vous supprimez une instance gérée, la stratégie d’intention de réseau est également supprimée.

## <a name="virtual-cluster-connectivity-architecture"></a>Architecture de la connectivité du cluster virtuel

Prenons plus en détail dans l’architecture de connectivité pour les instances gérées. Le diagramme suivant montre l’organisation conceptuelle du cluster virtuel.

![Architecture de connectivité du cluster virtuel](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Les clients se connectent à une instance gérée à l’aide d’un nom d’hôte qui se présente sous la forme `<mi_name>.<dns_zone>.database.windows.net`. Ce nom d’hôte correspond à une adresse IP privée, bien qu’il est inscrit dans une zone de nom (DNS) de domaine public et peut être résolu publiquement. Le `zone-id` est généré automatiquement lorsque vous créez le cluster. Si un nouveau cluster héberge une instance gérée secondaire, il partage son ID de zone avec le cluster principal. Pour plus d’informations, consultez [utiliser des groupes de reprise automatique pour permettre un basculement transparent et coordonné de plusieurs bases de données](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Cette adresse IP privée appartient à l’équilibreur de charge interne de l’instance gérée. L’équilibreur de charge dirige le trafic vers passerelle de l’instance gérée. Plusieurs instances gérées pouvant s’exécuter à l’intérieur du même cluster, la passerelle utilise le nom d’hôte de l’instance gérée à rediriger le trafic vers le service de moteur SQL correct.

Les services de gestion et le déploiement se connectent à une instance gérée à l’aide un [point de terminaison de gestion](#management-endpoint) équilibreur de charge est mappé à un externe. Le trafic est acheminé vers les nœuds uniquement si elle est reçue sur un ensemble prédéfini de ports qui utilisent uniquement les composants de gestion de l’instance gérée. Un pare-feu intégré sur les nœuds est configuré pour autoriser le trafic uniquement à partir de plages d’adresses IP de Microsoft. Certificats authentifient mutuellement toutes les communications entre les composants de gestion et le plan de gestion.

## <a name="management-endpoint"></a>Point de terminaison de gestion

Microsoft gère l’instance gérée à l’aide d’un point de terminaison de gestion. Ce point de terminaison est à l’intérieur du cluster virtuel de l’instance. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau. Sur le niveau de l’application, il est protégé par la vérification de mutuelle des certificats. Pour rechercher l’adresse du point de terminaison, consultez [déterminer l’adresse du point de terminaison gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Lorsque connexions commencent à l’intérieur de l’instance gérée (comme avec les sauvegardes et les journaux d’audit), le trafic s’affiche pour démarrer à partir de l’adresse IP du point de terminaison gestion. Vous pouvez limiter l’accès aux services publics à partir d’une instance gérée en définissant des règles de pare-feu pour autoriser uniquement les adresse IP de l’instance gérée. Pour plus d’informations, consultez [Vérifiez le pare-feu intégré de l’instance gérée](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Contrairement aux pare-feu pour les connexions qui démarrent à l’intérieur de l’instance gérée, les services Azure qui se trouvent dans la région de l’instance managée ont un pare-feu qui est optimisé pour le trafic qui transite entre ces services.

## <a name="network-requirements"></a>Configuration requise pour le réseau

Déployer une instance gérée dans un sous-réseau dédié à l’intérieur du réseau virtuel. Le sous-réseau doit avoir les caractéristiques suivantes :

- **Sous-réseau dédié :** Sous-réseau de l’instance managée ne peut pas contenir n’importe quel autre service cloud qui est associé, et il ne peut pas être un sous-réseau de passerelle. Le sous-réseau ne peut pas contenir n’importe quelle ressource, mais l’instance gérée, et vous ne pouvez pas ajouter ultérieurement de ressources dans le sous-réseau.
- **Groupe de sécurité réseau :** Un groupe de sécurité réseau associé avec le réseau virtuel doit définir [règles de sécurité entrantes](#mandatory-inbound-security-rules) et [règles de sécurité sortantes](#mandatory-outbound-security-rules) avant toute autre règle. Vous pouvez utiliser un groupe de sécurité réseau pour contrôler l’accès au point de terminaison de données de l’instance gérée en filtrant le trafic sur le port 1433.
- **Table d’itinéraire défini (UDR) utilisateur :** Une table d’UDR associé avec le réseau virtuel doit inclure spécifique [entrées](#user-defined-routes).
- **Aucun point de terminaison de service :** Aucun point de terminaison de service ne doit être associé à sous-réseau de l’instance gérée. Assurez-vous que l’option de points de terminaison de service est désactivée lorsque vous créez le réseau virtuel.
- **Suffisamment d’adresses IP :** Le sous-réseau de l’instance managée doit avoir au moins 16 adresses IP. La valeur minimale recommandée est de 32 adresses IP. Pour plus d’informations, consultez [déterminer la taille du sous-réseau pour les instances gérées](sql-database-managed-instance-determine-size-vnet-subnet.md). Vous pouvez déployer des instances gérées dans [le réseau existant](sql-database-managed-instance-configure-vnet-subnet.md) après avoir configuré les satisfaire [la configuration réseau requise pour les instances gérées](#network-requirements). Sinon, créez un [nouveau réseau et sous-réseau](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Vous ne pouvez pas déployer une nouvelle instance managée si le sous-réseau de destination ne dispose pas de ces caractéristiques. Lorsque vous créez une instance gérée, une stratégie intentionnelle de réseau est appliquée sur le sous-réseau pour empêcher les modifications non conformes au programme d’installation de mise en réseau. Une fois que la dernière instance est supprimée à partir du sous-réseau, la stratégie d’intention de réseau est également supprimée.

### <a name="mandatory-inbound-security-rules"></a>Règles de sécurité du trafic entrant obligatoires

| Nom       |Port                        |Protocole|Source           |Destination|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestion  |9000, 9003, 1438, 1440, 1452|TCP     |Quelconque              |Quelconque        |AUTORISER |
|mi_subnet   |Quelconque                         |Quelconque     |SOUS-RÉSEAU MI        |Quelconque        |AUTORISER |
|health_probe|Quelconque                         |Quelconque     |AzureLoadBalancer|Quelconque        |AUTORISER |

### <a name="mandatory-outbound-security-rules"></a>Règles de sécurité du trafic sortant obligatoires

| Nom       |Port          |Protocole|Source           |Destination|Action|
|------------|--------------|--------|-----------------|-----------|------|
|gestion  |80, 443, 12000|TCP     |Quelconque              |Internet   |AUTORISER |
|mi_subnet   |Quelconque           |Quelconque     |Quelconque              |SOUS-RÉSEAU MI *  |AUTORISER |

\* MI sous-réseau fait référence à la plage d’adresses IP pour le sous-réseau dans le formulaire 10.x.x.x/y. Vous pouvez trouver ces informations dans le portail Azure, dans les propriétés du sous-réseau.

> [!IMPORTANT]
> Bien que les règles de sécurité de trafic entrant requise autorisent le trafic à partir de _tout_ de code source sur les ports 9000, 9003, 1438, 1440 et 1452, ces ports sont protégés par un pare-feu intégré. Pour plus d’informations, consultez [déterminer l’adresse de point de terminaison de gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Si vous utilisez la réplication transactionnelle dans une instance gérée, et si vous utilisez une base de données d’instance comme un serveur de publication ou un serveur de distribution, ouvrez le port 445 (TCP sortant) dans les règles de sécurité du sous-réseau. Ce port autorise l’accès au partage de fichiers Azure.

### <a name="user-defined-routes"></a>itinéraires définis par l’utilisateur

|Nom|Préfixe de l’adresse|Tronçon suivant|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Réseau virtuel|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

En outre, vous pouvez ajouter des entrées à la table de routage pour acheminer le trafic qui a des plages d’adresses IP privées en local en tant que destination via la passerelle de réseau virtuel ou d’une appliance de réseau virtuel (NVA).

Si le réseau virtuel comprend un DNS personnalisé, ajoutez une entrée pour l’adresse du programme de résolution récursive d’Azure (168.63.129.16). Pour plus d’informations, consultez [configurer un DNS personnalisé](sql-database-managed-instance-custom-dns.md). Le serveur DNS personnalisé doit être en mesure de résoudre les noms d’hôte dans ces domaines et leurs sous-domaines : *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, et *microsoftonline-p.com*.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble, consultez [fonctions avancées de sécurité des données de la base de données SQL](sql-database-managed-instance.md).
- Découvrez comment [configurer un réseau virtuel Azure](sql-database-managed-instance-create-vnet-subnet.md) ou un [réseau virtuel Azure existant](sql-database-managed-instance-configure-vnet-subnet.md) où vous pouvez déployer les instances gérées.
- [Calculer la taille du sous-réseau](sql-database-managed-instance-determine-size-vnet-subnet.md) où vous souhaitez déployer les instances gérées.
- Découvrez comment créer une instance managée :
  - À partir du [portail Azure](sql-database-managed-instance-get-started.md).
  - À l’aide de [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).
  - À l’aide de [un modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - À l’aide de [un modèle Azure Resource Manager (à l’aide du serveur de rebond, avec SSMS inclus)](https://portal.azure.com/).
