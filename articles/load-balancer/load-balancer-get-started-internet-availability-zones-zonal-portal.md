---
title: Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique zonal à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique zonal à l’aide du portail Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 533c48b3a004f85dfbd2970d73dcf7de21811dca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-public-ip-address-frontend-using-azure-portal"></a>Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique zonal à l’aide du portail Azure

Cet article décrit les étapes de création d’une instance publique de [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) avec un frontend zonal. Pour comprendre le fonctionnement des zones de disponibilité avec Standard Load Balancer, consultez [Standard Load Balancer et zones de disponibilité](load-balancer-standard-availability-zones.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> La prise en charge des zones de disponibilité est assurée pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Créez un équilibreur de charge avec une adresse IP de frontend zonal

1. Dans un navigateur, accédez au portail Azure [http://portal.azure.com](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibrage de charge**.
3. Dans la page **Créer un équilibreur de charge**, sous **Nom**, tapez **myLoadBalancer**.
4. Sous **Type**, sélectionnez **Public**.
5. Sous Référence SKU, sélectionnez **Standard**.
6. Cliquez sur **Choisir une adresse IP publique**, sur **Créer nouveau** et dans la page **Créer une adresse IP publique**, sous le nom, tapez **myPublicIPZonal**, pour la référence (SKU), sélectionnez **Standard**, pour la zone de la disponibilité, sélectionnez **1**.
    
>[!NOTE] 
> L’adresse IP publique créée à cette étape a la référence (SKU) Standard par défaut.

7. Pour **Groupe de ressources**, cliquez sur **Créer nouveau**, puis tapez **myResourceGroupZLB** comme nom du groupe de ressources.
8. Pour **Emplacement**, sélectionnez **Europe de l’Ouest**, puis cliquez sur **OK**. L’équilibreur de charge commence ensuite le déploiement, qui peut prendre plusieurs minutes.

    ![Créer une instance de Load Balancer Standard redondante dans une zone à l’aide du portail Azure](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Load Balancer Standard et les zones de disponibilité](load-balancer-standard-availability-zones.md).



