---
title: Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant interzone à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant interzone à l’aide du portail Azure.
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
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 9a51638ea6d85178e6631ac278c116e4c7e05d61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200256"
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant interzone à l’aide du portail Azure

Cet article décrit les étapes de création d’une instance publique de [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) avec un frontend redondant interzone qui utilise une adresse IP publique Standard. Par défaut, une adresse IP de serveur frontal unique dans un équilibreur de charge standard est redondante interzone.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
 La prise en charge des zones de disponibilité est assurée pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Créer un équilibreur de charge redondant dans une zone

1. Dans un navigateur, accédez au portail Azure [http://portal.azure.com](http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibrage de charge**.
3. Dans la page **Créer un équilibreur de charge**, sous **Nom**, tapez **myLoadBalancer**.
4. Sous **Type**, sélectionnez **Public**.
5. Sous Référence SKU, sélectionnez **Standard**.
6. Cliquez sur **Adresse IP publique**, puis sur **Créer**, puis, dans **Créer une adresse IP publique**, sous Nom, tapez **myPublicIPStandard**.
    >[!NOTE] 
    > L’adresse IP publique créée à cette étape a la référence SKU Standard. Par défaut, elle est redondante interzone. 
8. Sous **Emplacement**, sélectionnez **Est des États-Unis 2**, puis cliquez sur **OK**. L’équilibreur de charge commence ensuite le déploiement, qui peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Load Balancer Standard et les zones de disponibilité](load-balancer-standard-availability-zones.md).



