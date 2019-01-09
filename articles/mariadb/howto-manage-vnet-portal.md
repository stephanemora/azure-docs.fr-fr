---
title: Créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database for MariaDB à l’aide du portail Azure | Microsoft Docs
description: Créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database for MariaDB à l’aide du portail Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 77602898df7d783367ffd78328082c3edcaac83e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542337"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Créer et gérer des points de terminaison de service de réseau virtuel et des règles de réseau virtuel Azure Database for MariaDB à l’aide du portail Azure

> [!IMPORTANT]
> La fonctionnalité des points de terminaison de service de réseau virtuel est en préversion publique.

Les règles et points de terminaison de service de réseau virtuel étendent l’espace d’adressage privé d’un réseau virtuel à votre serveur Azure Database for MariaDB. Pour avoir une vue d’ensemble des points de terminaison de service de réseau virtuel Azure Database for MariaDB, y compris les limitations, consultez [Utiliser des règles et points de terminaison de service de réseau virtuel pour Azure Database for MariaDB](concepts-data-access-security-vnet.md). Les points de terminaison de service de réseau virtuel sont disponibles dans toutes les régions prenant en charge Azure Database for MariaDB.

> [!NOTE]
> Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Créer une règle de réseau virtuel et activer les points de terminaison de service

1. Dans la page du serveur MariaDB, sous le titre Paramètres, cliquez sur **Sécurité des connexions** pour ouvrir le volet Sécurité des connexions pour Azure Database for MariaDB. Cliquez ensuite sur **+ Ajout d’un réseau virtuel existant**. Si vous ne disposez d’aucun réseau virtuel, vous pouvez en créer un en cliquant sur **+ Créer un nouveau réseau virtuel**. Consultez [Démarrage rapide : Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md)

   ![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-manage-vnet-portal/1-connection-security.png)

2. Entrez un nom de règle de réseau virtuel, sélectionnez l’abonnement, le réseau virtuel et le nom du sous-réseau, puis cliquez sur **Activer**. Les points de terminaison de service de réseau virtuel sont alors automatiquement activés sur le sous-réseau à l’aide du nom de service **Microsoft.SQL**.

   ![Portail Azure - Configurer le réseau virtuel](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Le compte doit avoir les autorisations nécessaires pour créer un réseau virtuel et un point de terminaison de service.

   Les points de terminaison de service peuvent être configurés indépendamment sur les réseaux virtuels par un utilisateur avec accès en écriture au réseau virtuel.
    
   Pour sécuriser les ressources du service Azure pour un réseau virtuel, l’utilisateur doit disposer des autorisations sur « Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/ » pour les sous-réseaux à ajouter. Cette autorisation est incluse par défaut dans les rôles d’administrateur de service fédérés et peut être modifiée en créant des rôles personnalisés.
    
   Apprenez-en davantage sur les [rôles intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Les réseaux virtuels et les ressources du service Azure peuvent être dans des abonnements identiques ou différents. Si le réseau virtuel et les ressources de service Azure se trouvent dans différents abonnements, les ressources doivent être sous le même locataire Active Directory (AD).

   > [!IMPORTANT]
   > Il est vivement recommandé de lire cet article sur les configurations de point de terminaison de service et les considérations à prendre en compte avant de configurer les points de terminaison de service. **Point de terminaison de service de réseau virtuel :** Un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Les points de terminaison de service de réseau virtuel utilisent le nom de type de service **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure SQL Database, Azure Database for MariaDB et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure Database, y compris les serveurs Azure SQL Database, Azure Database pour PostgreSQL, Azure Database for MariaDB et Azure Database pour MySQL dans le sous-réseau.
   > 

3. Une fois l’activation effectuée, cliquez sur **OK** : vous verrez que les points de terminaison de service de réseau virtuel sont activés en même temps qu’une règle de réseau virtuel.

   ![Points de terminaison de service de réseau virtuel activés et règle de réseau virtuel créée](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus en détail la [configuration de SSL dans Azure Database for MariaDB](howto-configure-ssl.md).
- De la même manière, vous pouvez créer un script pour [activer des points de terminaison de service de réseau virtuel et créer une règle de réseau virtuel pour Azure Database for MariaDB en utilisant Azure CLI](howto-manage-vnet-cli.md).
