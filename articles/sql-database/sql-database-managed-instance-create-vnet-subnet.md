---
title: Azure SQL Database Managed Instance - Créer un réseau virtuel | Microsoft Docs
description: Cette rubrique explique comment créer un réseau virtuel (VNet) dans lequel vous pouvez déployer une instance Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: ebdfc80d3802ad8eb7da6fb7f152efdaee8d777d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345076"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurer un réseau virtuel pour Azure SQL Database Managed Instance

Cette rubrique explique comment créer un réseau virtuel et un sous-réseau valides dans lesquels vous pouvez déployer des instances Azure SQL Database Managed Instance.

Azure SQL Database Managed Instance doit être déployé au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Ce déploiement permet les scénarios suivants : 
- Sécurisation d’une adresse IP privée
- Connexion directe à une instance managée à partir d’un réseau local 
- Connexion de Managed Instance à un serveur lié ou un autre magasin de données local 
- Connexion de Managed Instance à des ressources Azure  

  > [!Note]
  > Vous devez [déterminer la taille du sous-réseau pour Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md) avant de déployer la première instance, car le sous-réseau ne peut pas être redimensionné une fois que vous y avez placé les ressources.
  > Si vous envisagez d’utiliser un réseau virtuel existant, vous devez modifier sa configuration pour prendre en compte Managed Instance. Pour plus d’informations, consultez [Modifier un réseau virtuel existant pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md). 

## <a name="create-a-new-virtual-network"></a>Créer un réseau virtuel

Pour créer et configurer le réseau virtuel, le plus simple consiste à utiliser le modèle de déploiement Azure Resource Manager.

1. Connectez-vous au portail Azure.

2. Utilisez le bouton **Déployer dans Azure** pour déployer un réseau virtuel dans le cloud Azure :

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  Ce bouton ouvre un formulaire que vous pouvez utiliser pour configurer l’environnement réseau où vous pouvez déployer Managed Instance.

  > [!Note]
  > Ce modèle Azure Resource Manager déploie un réseau virtuel avec deux sous-réseaux. Un sous-réseau appelé **ManagedInstances** est réservé pour les Managed Instances et a une table d’itinéraires préconfigurée, tandis que le second sous-réseau appelé **Default** est utilisé pour les autres ressources qui doivent accéder à Managed Instance (par exemple, les machines virtuelles Azure). Vous pouvez supprimer le sous-réseau **Default** si vous n’en avez pas besoin.

3. Configurez l’environnement réseau. Dans le formulaire suivant, vous pouvez configurer les paramètres de votre environnement réseau :

![Configurer le réseau Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Vous pouvez modifier les noms du réseau virtuel et des sous-réseaux et ajustez les plages d’adresses IP associées à vos ressources réseau. Lorsque vous appuyez sur le bouton « Acheter », ce formulaire crée et configure votre environnement. Si vous n’avez pas besoin de deux sous-réseaux, vous pouvez supprimer le sous-réseau par défaut. 

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Qu’est-ce qu’une instance managée ?](sql-database-managed-instance.md)
- Découvrez l’[architecture de connectivité dans Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Apprenez à [modifier un réseau virtuel existant pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Pour un tutoriel qui montre comment créer un réseau virtuel, créer une option Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Création d’une option Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
