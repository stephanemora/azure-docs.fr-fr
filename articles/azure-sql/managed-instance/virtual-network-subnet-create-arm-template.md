---
title: Créez un réseau virtuel
titleSuffix: Azure SQL Managed Instance
description: Cet article décrit comment créer un réseau virtuel configuré pour prendre en charge le déploiement d'une instance Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 923f8b447b1103284b2c999a981826ef19a1c7d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84039260"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Créer un réseau virtuel pour Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article explique comment créer un réseau virtuel et un sous-réseau valides dans lesquels vous pouvez déployer Azure SQL Managed Instance.

Azure SQL Managed Instance doit être déployé au sein d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) Azure. Ce déploiement permet les scénarios suivants :

- Sécurisation d’une adresse IP privée
- Connexion directe à une instance SQL Managed Instance à partir d’un réseau local
- Connexion d'une instance SQL Managed Instance à un serveur lié ou un autre magasin de données local
- Connexion d'une instance SQL Managed Instance à des ressources Azure  

> [!NOTE]
> Vous devez [déterminer la taille du sous-réseau pour SQL Managed Instance](vnet-subnet-determine-size.md) avant de déployer la première instance. Vous ne pouvez pas redimensionner le sous-réseau une fois que celui-ci contient des ressources.
>
> Si vous envisagez d’utiliser un réseau virtuel existant, vous devez modifier sa configuration pour prendre en compte SQL Managed Instance. Pour plus d’informations, consultez [Modifier un réseau virtuel existant pour SQL Managed Instance](vnet-existing-add-subnet.md).
>
> Après la création d’une instance SQL Managed Instance, le déplacement de l’instance SQL Managed Instance ou du réseau virtuel vers un autre groupe de ressources ou abonnement n’est pas pris en charge.  Le déplacement de l’instance SQL Managed Instance vers un autre sous-réseau n’est pas non plus pris en charge.
>

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Pour créer et configurer un réseau virtuel, le plus simple consiste à utiliser un modèle de déploiement Azure Resource Manager.

1. Connectez-vous au portail Azure.

2. Sélectionnez le bouton **Déployer sur Azure** :

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ce bouton ouvre un formulaire que vous pouvez utiliser pour configurer l’environnement réseau dans lequel vous pouvez déployer l'instance SQL Managed Instance.

   > [!Note]
   > Ce modèle Azure Resource Manager déploie un réseau virtuel avec deux sous-réseaux. Un sous-réseau, appelé **ManagedInstances**, est réservé pour SQL Managed Instance et doté d'une table de routage préconfigurée. Le second sous-réseau, appelé **Default**, permet à d’autres ressources d’accéder à SQL Managed Instance (par exemple, Machines virtuelles Azure).

3. Configurez l’environnement réseau. Dans le formulaire suivant, vous pouvez configurer les paramètres de votre environnement réseau :

   ![Modèle Resource Manager pour configurer le réseau Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Vous pouvez changer les noms du réseau virtuel et des sous-réseaux. Vous pouvez également ajuster les plages d’adresses IP associées à vos ressources réseau. Quand vous appuyez sur le bouton **Acheter**, ce formulaire crée et configure votre environnement. Si vous n’avez pas besoin de deux sous-réseaux, vous pouvez supprimer le sous-réseau par défaut.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Découvrez l’[architecture de connectivité dans SQL Managed Instance](connectivity-architecture-overview.md).
- Apprenez à [modifier un réseau virtuel existant pour SQL Managed Instance](vnet-existing-add-subnet.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance SQL Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance Azure SQL Managed Instance](instance-create-quickstart.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](custom-dns-configure.md).
