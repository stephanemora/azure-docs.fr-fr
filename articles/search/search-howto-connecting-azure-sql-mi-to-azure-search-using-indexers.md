---
title: Connexion Azure SQL Managed Instance pour l’indexation de recherche - Recherche Azure
description: Activez le point de terminaison public pour autoriser les connexions à SQL Managed Instances à partir d’un indexeur dans Recherche Azure.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228984"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Configurer une connexion entre un indexeur Recherche Azure et SQL Managed Instance
Comme indiqué dans [Connexion d’Azure SQL Database à Recherche Azure à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), la création d’indexeurs sur **SQL Managed Instances** est prise en charge par Recherche Azure via le point de terminaison public.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Créer une instance Azure SQL Managed Instance avec un point de terminaison public
Créez une instance SQL Managed Instance avec l’option **Activer le point de terminaison public** sélectionnée.

   ![Activer le point de terminaison public](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Activer le point de terminaison public")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Activer un point de terminaison public Azure SQL Managed Instance
Vous pouvez également activer un point de terminaison public sur une instance SQL Managed Instance existante sous **Sécurité** > **Réseau virtuel** > **Point de terminaison public** > **Activer**.

   ![Activer le point de terminaison public](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Activer le point de terminaison public")

## <a name="verify-nsg-rules"></a>Vérifier les règles du groupe de sécurité réseau
Vérifiez que le groupe de sécurité réseau contient des **règles de sécurité de trafic entrant** appropriées qui autorisent les connexions à partir des services Azure.

   ![Règle de sécurité de trafic entrant de groupe de sécurité réseau](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Règle de sécurité de trafic entrant de groupe de sécurité réseau")

## <a name="get-public-endpoint-connection-string"></a>Obtenir la chaîne de connexion du point de terminaison public
Veillez à utiliser la chaîne de connexion pour le **point de terminaison public** (le port 3342, et non le port 1433).

   ![Chaîne de connexion du point de terminaison public](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Chaîne de connexion du point de terminaison public")

## <a name="next-steps"></a>Étapes suivantes
Une fois la configuration résolue, vous pouvez spécifier une instance SQL Managed Instance comme source de données pour un indexeur Recherche Azure à l’aide du portail ou de l’API REST. Pour plus d’informations, consultez [Connexion d’Azure SQL Database à Recherche Azure à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).
