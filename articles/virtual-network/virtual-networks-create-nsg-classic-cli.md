---
title: Créer un groupe de sécurité réseau (classique) à l’aide d’Azure CLI 1.0 | Microsoft Docs
description: Découvrez comment créer et déployer un groupe de sécurité réseau (classique) à l’aide d’Azure CLI 1.0.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5468801e56849498d712f51e71cfb31bf068398a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Créer un groupe de sécurité réseau (classique) à l’aide d’Azure CLI 1.0
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique. Vous pouvez également [créer un groupe de sécurité réseau dans le modèle de déploiement Resource Manager](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes d’interface de ligne de commande Azure suivants supposent qu’un environnement simple a déjà été créé conformément au scénario. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Création d’un réseau virtuel](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Créer un Groupe de sécurité réseau (NSG) pour le sous-réseau frontal

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md).
2. Basculer vers le mode classique :

    ```azurecli
    azure config mode asm
    ```   

3. Créer un NSG :
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Créer une règle de sécurité qui autorise l'accès au port 3389 (RDP) à partir d'Internet :
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Créer une règle qui autorise l'accès au port 80 (HTTP) à partir d'Internet :
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Associer le Groupe de sécurité réseau au sous-réseau frontal :
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Créer le Groupe de sécurité réseau pour le sous-réseau principal

1. Créez le groupe de sécurité réseau :
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Créer une règle qui autorise l'accès au port 1433 (SQL) à partir du sous-réseau frontal :
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Créer une règle qui refuse l'accès à Internet :
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Associer le Groupe de sécurité réseau au sous-réseau principal :
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```