---
title: Création et gestion de point de terminaison de service de réseau virtuel et de règles Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs
description: Création et gestion de point de terminaison de service de réseau virtuel et de règles Azure Database pour MySQL à l’aide du portail Azure
services: mysql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7520868fd6bd349043ad2c53e62de5db978db8b1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267218"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Création et gestion de point de terminaison de service de réseau virtuel et de règles de réseau virtuel Azure Database pour MySQL à l’aide du portail Azure
Les règles et points de terminaison de service de réseau virtuel étendent l’espace d’adressage privé d’un réseau virtuel à votre serveur Azure Database pour MySQL. Pour une vue d’ensemble des points de terminaison de service de réseau virtuel Azure Database pour MySQL, y compris les limitations, consultez [Use Virtual Network service endpoints and rules for Azure Database for MySQL](concepts-data-access-and-security-vnet.md) (Utiliser des règles et points de terminaison de service de réseau virtuel pour Azure Database pour MySQL). Les points de terminaison de service de réseau virtuel sont disponibles en préversion publique dans toutes les régions prises en charge pour Azure Database pour MySQL.

> [!NOTE]
> Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Créer une règle de réseau virtuel et activer les points de terminaison de service dans le portail Azure

1. Dans la page du serveur MySQL, sous le titre Paramètres, cliquez sur **Sécurité des connexions** pour ouvrir le volet Sécurité des connexions pour Azure Database pour MySQL. Cliquez ensuite sur **+ Ajout d’un réseau virtuel existant**. Si vous ne disposez d’aucun réseau virtuel, vous pouvez en créer un en cliquant sur **+ Créer un nouveau réseau virtuel**. Voir [Démarrage rapide : Créer un réseau virtuel à l’aide du portail Azure](../virtual-network/quick-create-portal.md)

   ![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Entrez un nom de règle de réseau virtuel, sélectionnez l’abonnement, le réseau virtuel et le nom du sous-réseau, puis cliquez sur **Activer**. Les points de terminaison de service de réseau virtuel sont alors automatiquement activés sur le sous-réseau à l’aide du nom de service **Microsoft.SQL**.

   ![Portail Azure - Configurer le réseau virtuel](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Il est vivement recommandé de lire cet article sur les configurations de point de terminaison de service et les considérations à prendre en compte avant de configurer les points de terminaison de service. **Point de terminaison de service de réseau virtuel :** un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Les points de terminaison de service de réseau virtuel utilisent le nom de type de service **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure SQL Database, Azure Database pour PostgreSQL et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure Database, y compris les serveurs Azure SQL Database, Azure Database pour PostgreSQL et Azure Database pour MySQL sur le sous-réseau. 
   > 

3. Une fois l’activation effectuée, cliquez sur **OK** : vous verrez que les points de terminaison de service de réseau virtuel sont activés en même temps qu’une règle de réseau virtuel.

   ![Points de terminaison de service de réseau virtuel activés et règle de réseau virtuel créée](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Étapes suivantes
- De la même manière, vous pouvez créer un script pour [activer des points de terminaison de service de réseau virtuel et créer une règle de réseau virtuel pour Azure Database pour MySQL en utilisant Azure CLI](howto-manage-vnet-using-cli.md).
- Pour vous aider à vous connecter à un serveur Azure Database pour MySQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour MySQL](./concepts-connection-libraries.md)
