---
title: Connexion de l’indexeur à SQL Managed Instance
titleSuffix: Azure Cognitive Search
description: Activez le point de terminaison public pour autoriser les connexions à SQL Managed Instances à partir d’un indexeur dans Recherche cognitive Azure.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 12ee369bfd69e82a73ccaa766190cedf7910a7a0
ms.sourcegitcommit: 3b371af4c30fce82854b3d45fd8b8e674bbe7517
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112984891"
---
# <a name="indexer-connections-to-azure-sql-managed-instance-through-a-public-endpoint"></a>Connexions de l’indexeur à Azure SQL Managed Instance via un point de terminaison public

Si vous configurez un indexeur de Recherche cognitive Azure qui se connecte à une instance gérée azure SQL, vous devez activer un point de terminaison public sur l’instance gérée comme condition préalable. Un indexeur se connecte à une instance gérée sur un point de terminaison public.

Cet article fournit des étapes de base qui incluent la collecte des informations nécessaires à la configuration de la source de données. Pour plus d’informations, consultez [Configurer un point de terminaison public dans Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md).

## <a name="enable-a-public-endpoint"></a>Activer un point de terminaison public

Pour une nouvelle instance gérée SQL, créez la ressource avec l’option **Activer le point de terminaison public** sélectionnée.

   ![Activer le point de terminaison public](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Activer un point de terminaison public")

Sinon, si l’instance existe déjà, vous pouvez activer un point de terminaison public sur une instance gérée SQL existante sous **Sécurité** > **Réseau virtuel** > **Point de terminaison public** > **Activer**.

   ![Activer un point de terminaison public au moyen d’une instance managée de VNET](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Activer un point de terminaison public")

## <a name="verify-nsg-rules"></a>Vérifier les règles du groupe de sécurité réseau

Vérifiez que le groupe de sécurité réseau contient des **règles de sécurité de trafic entrant** appropriées qui autorisent les connexions à partir des services Azure.

   ![Règle de sécurité de trafic entrant de NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Règle de sécurité de trafic entrant de NSG")

## <a name="restrict-inbound-access-to-the-endpoint"></a>Limiter l’accès entrant au point de terminaison

Vous pouvez limiter l’accès entrant au point de terminaison public en remplaçant la règle actuelle (`public_endpoint_inbound`) par les deux règles suivantes :

* Autorisation de l’accès entrant à partir de la [balise de service](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`)

* Autorisation de l’accès entrant à partir de l’adresse IP du service de recherche, qui peut être obtenue en exécutant une commande ping sur son nom de domaine complet (par exemple, `<your-search-service-name>.search.windows.net`). ("SOURCE" = `IP address`, "NAME" = `search_service_inbound`)

Pour chaque règle, définissez « PORT » = `3342`, « PROTOCOL » = `TCP`, « DESTINATION » = `Any`, « ACTION » = `Allow`.

## <a name="get-public-endpoint-connection-string"></a>Obtenir la chaîne de connexion du point de terminaison public

Copiez la chaîne de connexion à utiliser dans la connexion à la source de données de l’indexeur de recherche. Assurez-vous de copier la chaîne de connexion pour le **point de terminaison public** (port 3342 et non port 1433).

   ![Chaîne de connexion du point de terminaison public](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Chaîne de connexion du point de terminaison public")

## <a name="next-steps"></a>Étapes suivantes

Avec la configuration isolée, vous pouvez maintenant spécifier une [Instance gérée SQL comme source de données de l’indexeur](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).