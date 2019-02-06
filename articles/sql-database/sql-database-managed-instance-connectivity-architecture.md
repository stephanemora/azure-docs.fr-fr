---
title: Architecture de la connectivité d’Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article fournit une vue d’ensemble des communications d’Azure SQL Database Managed Instance et explique son architecture de connectivité, ainsi que la façon dont les différents composants fonctionnent pour diriger le trafic vers l’instance managée.
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
ms.date: 12/10/2018
ms.openlocfilehash: b709bbacce23a89b8c60b77a524018b50ca1ca5e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245665"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Architecture de la connectivité d’Azure SQL Database Managed Instance

Cet article fournit une vue d’ensemble des communications d’Azure SQL Database Managed Instance et explique son architecture de connectivité, ainsi que la façon dont les différents composants fonctionnent pour diriger le trafic vers l’instance managée.  

Azure SQL Database Managed Instance se trouve à l’intérieur du réseau virtuel Azure et du sous-réseau dédié aux instances gérées. Ce déploiement permet les scénarios suivants : 
- Sécurisation d’une adresse IP privée.
- Connexion directe à une instance gérée à partir d’un réseau local.
- Connexion de Managed Instance à un serveur lié ou un autre magasin de données local.
- Connexion de Managed Instance à des ressources Azure.

## <a name="communication-overview"></a>Vue d’ensemble des communications

Le diagramme suivant montre les entités qui se connectent à Managed Instance, ainsi que les ressources que Managed Instance doit contacter pour fonctionner correctement.

![Entités de l’architecture de la connectivité](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

La communication qui est représentée dans la partie inférieure du diagramme représente les applications et les outils des clients qui se connectent à Managed Instance en tant que source de données.  

Comme Managed Instance est une offre PaaS, Microsoft gère ce service avec des agents automatisés (gestion, déploiement et maintenance) en se basant sur des flux de données de télémétrie. Comme la gestion de Managed Instance est de la responsabilité exclusive de Microsoft, les clients ne peuvent pas accéder aux machines du cluster Managed Instance via RDP.

Certaines opérations de SQL Server lancées par les applications ou les utilisateurs finaux peuvent nécessiter l’interaction de Managed Instance avec la plateforme. C’est le cas lors de la création d’une base de données Managed Instance, une ressource qui est exposée via le portail, PowerShell et Azure CLI.

Managed Instance dépend d’autres services Azure pour fonctionner correctement (par exemple Stockage Azure pour les sauvegardes, Azure Service Bus pour la télémétrie, Azure AD pour l’authentification, Azure Key Vault pour TDE, etc.) et établit des connexions avec ceux-ci selon les besoins.

Toutes les communications mentionnées ci-dessus sont chiffrées et signés avec des certificats. Pour garantir que les parties communicantes sont approuvées, Managed Instance vérifie en permanence ces certificats en contactant l’autorité de certification. Si les certificats sont révoqués ou si Managed Instance ne peut pas les vérifier, il ferme les connexions pour protéger les données.

## <a name="high-level-connectivity-architecture"></a>Architecture de la connectivité globale

Globalement, Managed Instance est un ensemble de composants de service, hébergés sur un ensemble dédié de machines virtuelles isolées qui s’exécutent dans le sous-réseau du réseau virtuel du client et qui forment un cluster virtuel.

Plusieurs instances de Managed Instance peuvent être hébergées dans un même cluster virtuel. Le cluster est automatiquement étendu ou réduit si nécessaire quand le client change le nombre d’instances provisionnées dans le sous-réseau.

Les applications du client peuvent se connecter à Managed Instance, et interroger et mettre à jour des bases de données, seulement si elles s’exécutent à l’intérieur du réseau virtuel, du réseau virtuel appairé ou du réseau connecté VPN / Express Route en utilisant un point de terminaison avec une adresse IP privée.  

![Diagramme de l’architecture de la connectivité](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Les services de gestion et de déploiement Microsoft s’exécutent en dehors du réseau virtuel : la connexion entre Managed Instance et les services Microsoft passe donc par les points de terminaison avec des adresses IP publiques. Quand Managed Instance crée une connexion sortante, du côté de la réception, elle paraît provenir de cette adresse IP publique en raison de la traduction d’adresses réseau (NAT, Network Address Translation).

Le trafic de gestion transite via le réseau virtuel du client. Cela signifie que les éléments de l’infrastructure de réseau virtuel affectent et peuvent potentiellement endommager le trafic de gestion, ce qui a pour résultat que l’instance passe à un état défectueux et devient indisponible.

> [!IMPORTANT]
> Pour améliorer l’expérience du client et la disponibilité du service, Microsoft applique une stratégie destinée au réseau (Network Intent Policy) sur les éléments de l’infrastructure de réseau virtuel Azure, qui peut affecter le fonctionnement de Managed Instance. Il s’agit d’un mécanisme de la plateforme pour communiquer les spécifications de la mise en réseau de façon transparente aux utilisateurs finaux, avec comme objectif principal d’éviter une configuration réseau incorrecte et de garantir une exploitation normale de Managed Instance. Lors de la suppression de Managed Instance, la stratégie destinée au réseau (Network Intent Policy) est également supprimée.

## <a name="virtual-cluster-connectivity-architecture"></a>Architecture de la connectivité du cluster virtuel

Examinons plus en détail dans l’architecture de la connectivité de Managed Instance. Le diagramme suivant montre l’organisation conceptuelle du cluster virtuel.

![Diagramme de l’architecture de la connectivité du cluster virtuel](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Les clients se connectent à Managed Instance en utilisant le nom d’hôte qui a la forme suivante : `<mi_name>.<dns_zone>.database.windows.net`. Ce nom d’hôte se résout en une adresse IP privée, bien qu’elle soit inscrite dans la zone DNS publique et puisse être résolue publiquement. `zone-id` est généré automatiquement lors de la création du cluster. Si un nouveau cluster héberge une instance gérée secondaire, il partage son ID de zone avec le cluster principal. Pour plus d’informations, consultez [Groupes de basculement automatique](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Cette adresse IP privée appartient à l’équilibreur de charge interne de Managed Instance, qui dirige le trafic vers la passerelle Managed Instance. Comme plusieurs instances managées peuvent potentiellement s’exécuter à l’intérieur du même cluster, la passerelle utilise le nom d’hôte de Managed Instance pour rediriger le trafic vers le service du moteur SQL correct.

Les services de gestion et de déploiement se connectent à Managed Instance en utilisant un [point de terminaison de gestion](#management-endpoint) qui est mappé à un équilibreur de charge externe. Le trafic est routé vers les nœuds seulement s’il est reçu sur un ensemble de ports prédéfinis qui sont utilisés exclusivement par les composants de gestion de Managed Instance. Le pare-feu intégré sur les nœuds est configuré pour autoriser le trafic uniquement à partir de plages d’adresses IP spécifiques de Microsoft. Toutes les communications entre les composants de gestion et le plan de gestion sont authentifiés mutuellement par certificat.

## <a name="management-endpoint"></a>Point de terminaison de gestion

Le cluster virtuel Azure SQL Database Managed Instance contient un point de terminaison de gestion que Microsoft utilise pour l’instance gérée. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau et par la vérification de certificat mutuelle au niveau de l’application. Vous pouvez [rechercher l’adresse IP du point de terminaison de gestion](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Lorsque les connexions sont lancées à partir de Managed Instance (sauvegarde, journal d’audit), le trafic provient d’une adresse IP publique du point de terminaison de gestion. Vous pouvez limiter l’accès aux services publics à partir de Managed Instance en définissant des règles de pare-feu pour autoriser l’adresse IP Managed Instance uniquement. Découvrez des informations supplémentaires sur la méthode permettant de [vérifier le pare-feu intégré Managed Instance](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Cela ne s’applique pas à la définition des règles de pare-feu pour les services Azure qui se trouvent dans la même région que Managed Instance, car la plateforme Azure optimise le trafic entre les services colocalisés.

## <a name="network-requirements"></a>Configuration requise pour le réseau

Vous pouvez déployer Managed Instance dans un sous-réseau dédié (sous-réseau Managed Instance) à l’intérieur du réseau virtuel qui respecte les exigences suivantes :
- **Un sous-réseau dédié** : le sous-réseau Managed Instance ne doit contenir aucun autre service cloud et ne doit pas être un sous-réseau de passerelle. Vous ne pouvez pas créer d’instance managée dans un sous-réseau qui contient des ressources autres qu’une instance managée, ni ajouter ultérieurement d’autres ressources à l’intérieur du sous-réseau.
- **Un groupe de sécurité réseau (NSG) compatible** : un groupe de sécurité réseau associé à un sous-réseau Managed Instance doit contenir les règles répertoriées dans les tableaux suivants (règles de sécurité de trafic entrant obligatoires et règles de sécurité de trafic sortant obligatoires), qui doivent être prioritaires par rapport aux autres règles. Vous pouvez utiliser un groupe de sécurité réseau pour contrôler entièrement l’accès au point de terminaison de données de l’instance managée en filtrant le trafic sur le port 1433. 
- **Une table de routage défini par l’utilisateur compatible** : le sous-réseau Managed Instance doit comprendre une table UDR à laquelle est affecté le routage UDR obligatoire **0.0.0.0/0 Next Hop Internet**. En outre, vous pouvez utiliser la passerelle de réseau virtuel ou une appliance de réseau virtuel pour ajouter en tant que destination un routage UDR qui achemine le trafic comprenant des plages d’adresses IP privées locales. 
- **Un DNS personnalisé facultatif** : si un DNS personnalisé est spécifié sur le réseau virtuel, vous devez ajouter l’adresse IP du programme de résolution récursif d’Azure (par exemple, 168.63.129.16) à la liste. Pour plus d’informations, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md). Le serveur DNS personnalisé doit pouvoir résoudre les noms d’hôtes dans les domaines suivants et leurs sous-domaines : *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* et *microsoftonline-p.com*. 
- **Aucun point de terminaison de service** : le sous-réseau Managed Instance ne doit pas être associé à un point de terminaison de service. Vérifiez que l’option Points de terminaison de service est désactivée quand vous créez le réseau virtuel.
- **Un nombre d’adresses IP suffisant** : le sous-réseau Managed Instance doit avoir 16 adresses IP au minimum (le minimum recommandé est de 32 adresses IP). Pour plus d’informations, consultez [Déterminer la taille du sous-réseau pour les options Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Vous pouvez déployer des options Managed Instance dans [le réseau existant](sql-database-managed-instance-configure-vnet-subnet.md) une fois que vous l’avez configuré pour satisfaire les [exigences réseau de Managed Instance](#network-requirements), ou créer [un réseau et un sous-réseau](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Vous ne pouvez pas déployer une instance managée si le sous-réseau de destination ne répond pas à toutes les exigences précédentes. Lorsqu’une instance managée est créée, une *stratégie d’intention réseau* est appliquée au sous-réseau pour empêcher toute modification non conforme de la configuration réseau. Lorsque la dernière instance restante est supprimée du sous-réseau, la *stratégie d’intention réseau* est elle aussi supprimée.

### <a name="mandatory-inbound-security-rules"></a>Règles de sécurité du trafic entrant obligatoires 

| NOM       |Port                        |Protocole|Source           |Destination|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|gestion  |9000, 9003, 1438, 1440, 1452|TCP     |Quelconque              |Quelconque        |AUTORISER |
|mi_subnet   |Quelconque                         |Quelconque     |SOUS-RÉSEAU MI        |Quelconque        |AUTORISER |
|health_probe|Quelconque                         |Quelconque     |AzureLoadBalancer|Quelconque        |AUTORISER |

### <a name="mandatory-outbound-security-rules"></a>Règles de sécurité du trafic sortant obligatoires 

| NOM       |Port          |Protocole|Source           |Destination|Action|
|------------|--------------|--------|-----------------|-----------|------|
|gestion  |80, 443, 12000|TCP     |Quelconque              |Internet   |AUTORISER |
|mi_subnet   |Quelconque           |Quelconque     |Quelconque              |SOUS-RÉSEAU MI  |AUTORISER |

  > [!Note]
  > SOUS-RÉSEAU MI fait référence à la plage d’adresses IP du sous-réseau sous la forme 10.x.x.x/y. Vous trouverez ces informations dans le portail Azure (via les propriétés du sous-réseau).
  
  > [!Note]
  > Bien que les règles de sécurité de trafic entrant obligatoires autorisent le trafic à partir de _n’importe quelle_ source vers les ports 9000, 9003, 1438, 1440 et 1452, ces ports sont protégés par un pare-feu intégré. Cet [article](sql-database-managed-instance-find-management-endpoint-ip-address.md) explique comment détecter l’adresse IP de point de terminaison de gestion et comment vérifier les règles de pare-feu. 
  
  > [!Note]
  > Si vous utilisez une réplication transactionnelle dans Managed Instance et qu’une base de données quelconque dans Managed Instance est utilisée en tant que serveur de publication ou de distribution, le port 445 (TCP sortant) doit également être ouvert dans les règles de sécurité du sous-réseau pour accéder au partage de fichiers Azure.
  
## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez  [Présentation de Managed Instance](sql-database-managed-instance.md)
- Découvrez comment [configurer un nouveau réseau virtuel](sql-database-managed-instance-create-vnet-subnet.md) ou [configurer un réseau virtuel existant](sql-database-managed-instance-configure-vnet-subnet.md) où vous pouvez déployer Managed Instance.
- [Calculez la taille du sous-réseau](sql-database-managed-instance-determine-size-vnet-subnet.md) où vous déploierez Managed Instance. 
- Pour un démarrage rapide, consultez Comment créer une instance managée
  - à partir du [Portail Azure](sql-database-managed-instance-get-started.md)
  - à l’aide de [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - avec [un modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - avec [un modèle Azure Resource Manager (serveur jumpbox avec SSMS inclus)](https://portal.azure.com/)
