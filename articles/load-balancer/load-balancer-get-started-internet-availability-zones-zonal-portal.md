---
title: Créer un équilibreur de charge avec front-end zonal - Portail Azure
titleSuffix: Azure Load Balancer
description: Créer une instance de Standard Load Balancer avec front-end zonal à l'aide du portail Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 7395da5bc1bf3829cf652374a9a0bb60b4621042
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894520"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Créer une instance Standard Load Balancer avec un front-end interzone à l’aide du portail Azure

Cet article décrit les étapes de création d'une instance publique de [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) avec configuration IP de front-end zonal. Pour comprendre le fonctionnement des zones de disponibilité avec Standard Load Balancer, consultez [Standard Load Balancer et zones de disponibilité](load-balancer-standard-availability-zones.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> La prise en charge des zones de disponibilité est assurée pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Créez un équilibreur de charge avec une adresse IP de frontend zonal

1. Dans un navigateur, accédez au portail Azure [https://portal.azure.com](https://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibrage de charge**.
3. Dans la page **Créer un équilibreur de charge**, sous **Nom**, tapez **myLoadBalancer**.
4. Sous **Type**, sélectionnez **Public**.
5. Sous Référence SKU, sélectionnez **Standard**.
6. Cliquez sur **Choisir une adresse IP publique**, sur **Créer nouveau** et dans la page **Créer une adresse IP publique**, sous le nom, tapez **myPublicIPZonal**, pour la référence (SKU), sélectionnez **Standard**, pour la zone de la disponibilité, sélectionnez **1**.
    
>[!NOTE] 
> L’adresse IP publique créée à cette étape a la référence (SKU) Standard par défaut.

1. Pour **Groupe de ressources**, cliquez sur **Créer nouveau**, puis tapez **myResourceGroupZLB** comme nom du groupe de ressources.
1. Pour **Emplacement**, sélectionnez **Europe Ouest**, puis cliquez sur **OK**. L’équilibreur de charge commence ensuite le déploiement, qui peut prendre plusieurs minutes.

    ![Créer une instance de Standard Load Balancer redondante interzone à l’aide du portail Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Load Balancer Standard et les zones de disponibilité](load-balancer-standard-availability-zones.md).



