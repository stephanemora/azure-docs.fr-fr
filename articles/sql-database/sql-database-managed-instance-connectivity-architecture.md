---
title: Architecture de la connectivité d’Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article fournit une vue d’ensemble des communications d’Azure SQL Database Managed Instance et explique son architecture de connectivité, ainsi que la façon dont les différents composants fonctionnent pour diriger le trafic vers l’instance managée.
keywords: ''
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.subservice: managed instance
ms.custom: ''
ms.date: 08/16/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.topic: conceptual
ms.openlocfilehash: 54917c6548c7f0bfacad6408732c5619e6346683
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177502"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Architecture de la connectivité d’Azure SQL Database Managed Instance 

Cet article fournit une vue d’ensemble des communications d’Azure SQL Database Managed Instance et explique son architecture de connectivité, ainsi que la façon dont les différents composants fonctionnent pour diriger le trafic vers l’instance managée.  

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

Les clients se connectent à Managed Instance en utilisant le nom d’hôte qui a la forme suivante : <nom_im>.<clusterid>.database.windows.net. Ce nom d’hôte se résout en une adresse IP privée, bien qu’elle soit inscrite dans la zone DNS publique et puisse être résolue publiquement. 

Cette adresse IP privée appartient à l’équilibreur de charge interne de Managed Instance, qui dirige le trafic vers la passerelle Managed Instance. Comme plusieurs instances managées peuvent potentiellement s’exécuter à l’intérieur du même cluster, la passerelle utilise le nom d’hôte de Managed Instance pour rediriger le trafic vers le service du moteur SQL correct. 

Les services de gestion et de déploiement se connectent à Managed Instance en utilisant un point de terminaison public qui est mappé à un équilibreur de charge externe. Le trafic est routé vers les nœuds seulement s’il est reçu sur un ensemble de ports prédéfinis qui sont utilisés exclusivement par les composants de gestion de Managed Instance. Toutes les communications entre les composants de gestion et le plan de gestion sont authentifiés mutuellement par certificat. 

## <a name="next-steps"></a>Étapes suivantes 

- Pour obtenir une vue d’ensemble, consultez  [Présentation de Managed Instance](sql-database-managed-instance.md) 
- Pour plus d’informations sur la configuration du réseau virtuel, consultez  [Configuration du réseau virtuel pour Managed Instance](sql-database-managed-instance-vnet-configuration.md). 
- Pour un démarrage rapide, consultez Comment créer une instance managée 
  - à partir du [portail Azure](sql-database-managed-instance-create-tutorial-portal.md) 
  - avec [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 
  - avec [un modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 
  - avec [un modèle Azure Resource Manager (serveur jumpbox avec SSMS inclus)](https://portal.azure.com/) 

