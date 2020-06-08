---
title: Configurer des règles d’application de pare-feu Azure avec des noms de domaine complets SQL
description: Dans cet article, vous allez apprendre à configurer des noms de domaine complets SQL dans les règles d’application de pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: 1195bb2676634dd3e7c9310da4e8855c4ff814f3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84015962"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurer des règles d’application de pare-feu Azure avec des noms de domaine complets SQL

Vous pouvez maintenant configurer des règles d’application de pare-feu Azure avec des noms de domaine complets SQL. Cela vous permet de limiter l’accès à partir de vos réseaux virtuels aux seules instances de serveur SQL spécifiées.

Grâce aux noms de domaine complets SQL, vous pouvez filtrer le trafic :

- À partir de vos réseaux virtuels pour une Azure SQL Database ou un Azure SQL Data Warehouse. Par exemple : Autoriser uniquement l’accès à *sql-server1.database.windows.net*.
- Envoi local vers Azure SQL Managed Instances ou IaaS SQL en cours d’exécution dans vos réseaux virtuels.
- Envoi rayon à rayon vers Azure SQL Managed Instances ou IaaS SQL en cours d’exécution dans vos réseaux virtuels.

Le filtrage FQDN SQL est pris en charge uniquement en [mode proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Si vous utilisez SQL dans le mode de redirection par défaut, vous pouvez filtrer les accès à l’aide de la balise de service SQL dans le cadre des [règles de réseau](overview.md#network-traffic-filtering-rules).
Si vous utilisez des ports qui ne sont pas par défaut pour le trafic IaaS SQL, vous pouvez configurer ces ports dans les règles d’application de pare-feu.

## <a name="configure-using-azure-cli"></a>Configurer à l’aide d’Azure CLI

1. Déployer un [pare-feu Azure à l’aide de Azure CLI](deploy-cli.md).
2. Si vous filtrez le trafic vers Azure SQL Database, SQL Data Warehouse ou SQL Managed Instance, assurez-vous que le mode de connectivité SQL est défini sur **Proxy**. Pour savoir comment modifier de mode de connectivité SQL, voir [Paramètres de connectivité Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > Le mode *proxy* de SQL peut entraîner une plus grande latence par rapport au mode de *redirection*. Si vous souhaitez continuer à utiliser le mode de redirection, qui est le mode par défaut pour les clients se connectant à Azure, vous pouvez filtrer l’accès en utilisant la [balise de service](service-tags.md) SQL dans les [règles de réseau](tutorial-firewall-deploy-portal.md#configure-a-network-rule) du pare-feu.

3. Configurer une règle d’application avec des noms de domaine complet SQL pour autoriser l’accès à un serveur SQL :

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurer à l’aide du portail Azure
1. Déployer un [pare-feu Azure à l’aide de Azure CLI](deploy-cli.md).
2. Si vous filtrez le trafic vers Azure SQL Database, SQL Data Warehouse ou SQL Managed Instance, assurez-vous que le mode de connectivité SQL est défini sur **Proxy**. Pour savoir comment modifier de mode de connectivité SQL, voir [Paramètres de connectivité Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Le mode *proxy* de SQL peut entraîner une plus grande latence par rapport au mode de *redirection*. Si vous souhaitez continuer à utiliser le mode de redirection, qui est le mode par défaut pour les clients se connectant à Azure, vous pouvez filtrer l’accès en utilisant la [balise de service](service-tags.md) SQL dans les [règles de réseau](tutorial-firewall-deploy-portal.md#configure-a-network-rule) du pare-feu.
3. Ajouter la règle d’application avec le protocole approprié, le port et le nom de domaine complet SQL, puis sélectionnez **Enregistrer**.
   ![règle d’application avec des noms de domaine complet SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Accédez à SQL à partir d’une machine virtuelle dans un réseau virtuel qui filtre le trafic à l’aide du pare-feu. 
5. Vérifiez que les [journaux du pare-feu d’Azure](log-analytics-samples.md) sont autorisés à afficher le trafic.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le proxy de SQL et sur les modes de redirection, consultez [Architecture de connectivité de base de données SQL Azure](../azure-sql/database/connectivity-architecture.md).