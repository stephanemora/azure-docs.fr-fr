---
title: Création et gestion de point de terminaison de service de réseau virtuel et de règles Azure Database pour PostgreSQL à l’aide du portail Azure | Microsoft Docs
description: Création et gestion de point de terminaison de service de réseau virtuel et de règles Azure Database pour PostgreSQL à l’aide du portail Azure
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 08/15/2018
ms.openlocfilehash: af5df507bd7a825ddfaaa7518691cb5fbec34d47
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42144808"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Création et gestion de point de terminaison de service de réseau virtuel et de règles de réseau virtuel Azure Database pour PostgreSQL à l’aide du portail Azure
Les règles et points de terminaison de service de réseau virtuel étendent l’espace d’adressage privé d’un réseau virtuel à votre serveur Azure Database pour PostgreSQL. Pour une vue d’ensemble des points de terminaison de service de réseau virtuel Azure Database pour PostgreSQL, y compris les limitations, consultez [Use Virtual Network service endpoints and rules for Azure Database for PostgreSQL](concepts-data-access-and-security-vnet.md) (Utiliser des règles et points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL). Les points de terminaison de service de réseau virtuel sont disponibles dans toutes les régions prises en charge pour Azure Database pour PostgreSQL.

> [!NOTE]
> Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Créer une règle de réseau virtuel et activer les points de terminaison de service dans le portail Azure

1. Dans la page du serveur PostgreSQL, sous le titre Paramètres, cliquez sur **Sécurité des connexions** afin d’ouvrir le volet correspondant pour Azure Database pour PostgreSQL. Cliquez ensuite sur **+ Ajout d’un réseau virtuel existant**. Si vous ne disposez d’aucun réseau virtuel, vous pouvez en créer un en cliquant sur **+ Créer un nouveau réseau virtuel**. Voir [Démarrage rapide : Créer un réseau virtuel à l’aide du portail Azure](../virtual-network/quick-create-portal.md)

   ![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Entrez un nom de règle de réseau virtuel, sélectionnez l’abonnement, le réseau virtuel et le nom du sous-réseau, puis cliquez sur **Activer**. Les points de terminaison de service de réseau virtuel sont alors automatiquement activés sur le sous-réseau à l’aide du nom de service **Microsoft.SQL**.

   ![Portail Azure - Configurer le réseau virtuel](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Il est vivement recommandé de lire cet article sur les configurations de point de terminaison de service et les considérations à prendre en compte avant de configurer les points de terminaison de service. **Point de terminaison de service de réseau virtuel :** un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Les points de terminaison de service de réseau virtuel utilisent le nom de type de service **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure SQL Database, Azure Database pour PostgreSQL et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure Database, y compris les serveurs Azure SQL Database, Azure Database pour PostgreSQL et Azure Database pour MySQL sur le sous-réseau. 
   > 

3. Une fois l’activation effectuée, cliquez sur **OK** : vous verrez que les points de terminaison de service de réseau virtuel sont activés en même temps qu’une règle de réseau virtuel.

   ![Points de terminaison de service de réseau virtuel activés et règle de réseau virtuel créée](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Étapes suivantes
- De la même manière, vous pouvez créer un script pour [activer des point de terminaison de service de réseau virtuel et créer une règle de réseau virtuel pour Azure Database pour PostgreSQL en utilisant Azure CLI](howto-manage-vnet-using-cli.md).
- Pour vous aider à vous connecter à un serveur Azure Database pour PostgreSQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour PostgreSQL](./concepts-connection-libraries.md)
