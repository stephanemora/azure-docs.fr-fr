---
title: Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel
description: Découvrez comment joindre un runtime d’intégration Azure-SSIS à un réseau virtuel Azure.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c89dd88c7163f74abb9246d57d72c485c1ed7efc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218879"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Lorsque vous utilisez SQL Server Integration Services (SSIS) dans Azure Data Factory , vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel Azure dans les scénarios suivants :

- Vous souhaitez vous connecter à des magasins de données locaux à partir de packages SSIS en cours d’exécution sur votre Azure-SSIS IR sans configurer ou gérer un IR auto-hébergé en tant que proxy. 

- Vous souhaitez héberger une base de données de catalogue SSIS (SSISDB) dans Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou dans SQL Managed Instance avec un point de terminaison privé. 

- Vous souhaitez vous connecter à des ressources Azure configurées avec des points de terminaison de service de réseau virtuel à partir de packages SSIS exécutés sur votre Azure-SSIS IR.

- Vous souhaitez vous connecter à des magasins de données/ressources Azure configurés avec des règle de pare-feu IP à partir de packages SSIS exécutés sur votre Azure-SSIS IR.

Data Factory vous permet de joindre votre Azure-SSIS IR à un réseau virtuel créé via le modèle de déploiement classique ou via le modèle de déploiement Azure Resource Manager.

> [!IMPORTANT]
> Le réseau virtuel classique est déconseillé. Par conséquent, utilisez plutôt le réseau virtuel Azure Resource Manager.  Si vous utilisez déjà le réseau virtuel classique, basculez dès que possible vers le réseau virtuel Azure Resource Manager.

Le tutoriel de [configuration d’un runtime d’intégration (IR) Azure-SQL Server Integration Services (SSIS) pour joindre un réseau virtuel](tutorial-deploy-ssis-virtual-network.md) décrit les étapes minimales de l’utilisation du Portail Azure. Cet article s’appuie sur ce tutoriel et explique toutes les tâches facultatives :

- Si vous utilisez un réseau virtuel (classique).
- Si vous apportez vos propres adresses IP publiques pour le runtime Azure-SSIS IR.
- Si vous utilisez votre propre serveur DNS (Domain Name System).
- Si vous utilisez un groupe de sécurité réseau sur le sous-réseau.
- Si vous utilisez Azure ExpressRoute ou une route définie par l’utilisateur (UDR).
- Si vous utilisez un Azure-SSIS IR personnalisé.
- Si vous utilisez l’approvisionnement Azure PowerShell.

## <a name="access-to-on-premises-data-stores"></a>Accéder aux magasins de données locaux

Si vos packages SSIS accèdent à des magasins de données locaux, vous pouvez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel qui est connecté au réseau local. Vous pouvez également configurer ou gérer un IR auto-hébergé en tant que proxy pour votre Azure-SSIS IR. Pour plus d’informations, consultez [Configurer l’IR auto-hébergé comme proxy pour Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Lorsque vous joignez votre Azure-SSIS IR à un réseau virtuel, rappelez-vous ces points importants : 

- Si aucun réseau virtuel n’est connecté à votre réseau local, créez d’abord un réseau virtuel [Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) auquel votre runtime d’intégration Azure-SSIS IR pourra se joindre. Ensuite, configurez une [connexion de passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de site à site ou une connexion [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) de ce réseau virtuel vers votre réseau local. 

- Si un réseau virtuel classique ou Azure Resource Manager est déjà connecté à votre réseau local, au même emplacement que votre runtime d’intégration Azure-SSIS IR, vous pouvez joindre ce dernier à ce réseau virtuel. 

- Si un réseau virtuel classique est déjà connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS IR, vous devez créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pour le runtime d’intégration Azure-SSIS IR à joindre. Ensuite, configurez une connexion du type [réseau virtuel classique vers Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Si un réseau virtuel Azure Resource Manager est déjà connecté à votre réseau local, à un autre emplacement que celui de votre runtime d’intégration Azure-SSIS IR, vous devez commencer par créer un [réseau virtuel Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pour le runtime d’intégration Azure-SSIS IR à joindre. Ensuite, configurez une connexion [du type réseau virtuel Azure Resource Manager vers Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hébergement du catalogue SSIS dans SQL Database

Si vous hébergez votre catalogue SSIS dans une base de données Azure SQL Database avec des points de terminaison de service de réseau virtuel, assurez-vous de joindre votre Azure-SSIS IR aux mêmes réseau virtuel et sous-réseau.

Si vous hébergez votre catalogue SSIS dans SQL Managed Instance avec un point de terminaison privé, veillez à associer votre Azure-SSIS IR au même réseau virtuel mais dans un sous-réseau différent de celui de l’instance managée. Si vous associez votre runtime d’intégration Azure-SSIS IR à un réseau virtuel différent de celui de SQL Managed Instance, nous recommandons le peering de réseau virtuel (qui est limité à la même région) ou une connexion de réseau virtuel à réseau virtuel. Pour plus d’informations, consultez [Connecter votre application à Azure SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Accès aux services Azure

Si vos packages SSIS accèdent à des ressources Azure qui prennent en charge des [points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) et que vous souhaitez sécuriser l’accès à ces ressources dans Azure-SSIS IR, vous pouvez joindre votre Azure-SSIS IR à un sous-réseau de réseau virtuel configuré pour des points de terminaison de service de réseau virtuel, puis ajouter une règle de réseau virtuel aux ressources Azure appropriées pour autoriser l’accès à partir du même sous-réseau.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Accès à des sources de données protégées par une règle de pare-feu IP

Si vos packages SSIS accèdent à des magasins de données/ressources qui n’autorisent que certaines adresses IP publiques statiques et que vous souhaitez sécuriser l’accès à ces ressources à partir du runtime d’intégration Azure-SSIS IR, vous pouvez associer des [adresses IP publiques](../virtual-network/ip-services/virtual-network-public-ip-address.md) à Azure-SSIS IR tout en le joignant à un réseau virtuel, puis ajouter une règle de pare-feu IP aux ressources appropriées pour autoriser l’accès à partir de ces adresses IP. Il existe deux autres façons d’effectuer cette opération : 

- Lorsque vous créez un runtime d’intégration Azure-SSIS, vous pouvez apporter vos propres IP publiques et les spécifier par le biais de l’[interface utilisateur Azure Data Factory](join-azure-ssis-integration-runtime-virtual-network-ui.md) ou du [Kit de développement logiciel (SDK) Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md). Seule la connectivité Internet sortante d’Azure-SSIS IR utilise vos adresses IP publiques fournies. Les autres appareils du sous-réseau ne les utilisent pas.
- Vous pouvez également configurer une [NAT de réseau virtuel](../virtual-network/nat-gateway/nat-overview.md) pour le sous-réseau qu’Azure-SSIS IR va rejoindre et toutes les connexions sortantes de ce sous-réseau vont utiliser vos adresses IP publiques spécifiées.

Dans tous les cas, le réseau virtuel peut être déployé uniquement par le biais du modèle de déploiement Azure Resource Manager.

## <a name="next-steps"></a>Étapes suivantes

- [Détails de la configuration de réseau virtuel pour un runtime d’intégration Azure-SSIS](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel avec l’interface utilisateur d’Azure Data Factory studio](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel à l’aide d’Azure PowerShell](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Pour plus d’informations sur le runtime d’intégration Azure-SSIS IR, voir les articles suivants : 
- [Runtime d’intégration Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations conceptuelles générales sur les runtimes d’intégration, notamment sur Azure-SSIS IR. 
- [Tutoriel : Déployer des packages SSIS vers Azure](./tutorial-deploy-ssis-packages-azure.md). Ce didacticiel fournit des instructions pas à pas pour créer votre Azure-SSIS IR. Il utilise Azure SQL Database pour héberger le catalogue SSIS. 
- [Créez un runtime d’intégration Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le didacticiel. Il fournit des instructions sur l’utilisation d’Azure SQL Database avec des points de terminaison de service de réseau virtuel ou SQL Managed Instance dans un réseau virtuel pour héberger le catalogue SSIS. Il explique comment joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment obtenir des informations sur votre runtime d’intégration Azure-SSIS IR. Il fournit des descriptions sur l’état des informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out du runtime d’intégration Azure-SSIS en lui ajoutant des nœuds.