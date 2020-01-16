---
title: Créer une instance Load Balancer avec un frontend interzone redondant - portail Azure
titleSuffix: Azure Load Balancer
description: Découvrez comment créer un équilibreur de charge standard public avec un front-end d’adresse IP publique redondant interzone en utilisant le portail Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: bd85e062b2b45abc32269b94ce678ebf63fb3fc1
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894451"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Créer un équilibreur de charge standard avec un front-end redondant interzone en utilisant le portail Azure

Cet article décrit les étapes de création d’une instance publique de [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) avec un frontend interzone redondant utilisant une adresse IP publique standard. Par défaut, une adresse IP de serveur frontal unique dans un équilibreur de charge standard est redondante interzone.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
>  La prise en charge des zones de disponibilité est assurée pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Créer un équilibreur de charge redondant dans une zone

1. Dans un navigateur, accédez au portail Azure [https://portal.azure.com](https://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibrage de charge**.
3. Dans la page **Créer un équilibreur de charge**, sous **Nom**, tapez **myLoadBalancer**.
4. Sous **Type**, sélectionnez **Public**.
5. Sous Référence SKU, sélectionnez **Standard**.
6. Cliquez sur **Adresse IP publique**, puis sur **Créer**, puis, dans **Créer une adresse IP publique**, sous Nom, tapez **myPublicIPStandard**.
    >[!NOTE] 
    > L’adresse IP publique créée à cette étape a la référence SKU Standard. Par défaut, elle est redondante interzone. 
8. Sous **Emplacement**, sélectionnez **USA Est 2**, puis cliquez sur **OK**. L’équilibreur de charge commence ensuite le déploiement, qui peut prendre plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Load Balancer Standard et les zones de disponibilité](load-balancer-standard-availability-zones.md).



