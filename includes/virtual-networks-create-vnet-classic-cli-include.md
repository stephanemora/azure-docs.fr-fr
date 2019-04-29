---
title: Fichier Include
description: Fichier Include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cd686e1bf62bbd7f37f61ced767e92918edf919c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596984"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Création d'un réseau virtuel classique à l'aide de l'interface de ligne de commande Azure
Vous pouvez utiliser l'interface de ligne de commande Azure pour gérer vos ressources Azure à partir de l'invite de commande sur n'importe quel ordinateur exécutant Windows, Linux ou OSX.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../articles/cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Pour créer un réseau virtuel et un sous-réseau, exécutez la commande **azure network vnet create** :
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Sortie attendue :
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Nom du réseau virtuel à créer. Pour le scénario, *TestVNet*
   * **-e (ou --address-space)**. Espace d'adressage du réseau virtuel. Pour le scénario, *192.168.0.0*
   * **-i (ou -cidr)**. Masque de réseau au format CIDR. Pour le scénario, *16*.
   * **-n (ou --subnet-name**). Nom du premier sous-réseau. Pour le scénario, *FrontEnd*.
   * **-p (ou --subnet-start-ip)**. Adresse IP de début pour le sous-réseau, ou espace d'adressage du sous-réseau. Pour le scénario, *192.168.1.0*.
   * **-r (ou --subnet-cidr)**. Masque de réseau au format CIDR pour le sous-réseau. Pour le scénario, *24*.
   * **-l (ou --location)**. Région Azure où le réseau virtuel est créé. Pour notre scénario, *USA Centre*.
3. Pour créer un sous-réseau, exécutez la commande **azure network vnet subnet create** :
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Voici la sortie attendue de la commande précédente :
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (ou --vnet-name**. Nom du réseau virtuel où sera créé le sous-réseau. Pour le scénario, *TestVNet*.
   * **-n (ou --name)**. Nom du nouveau sous-réseau. Pour le scénario, *BackEnd*.
   * **-a (ou --address-prefix)**. Bloc CIDR de sous-réseau. Pour le scénario, *192.168.2.0/24*.
4. Pour afficher les propriétés du nouveau réseau virtuel, exécutez la commande **azure network vnet show** :
   
            azure network vnet show
   
    Voici la sortie attendue de la commande précédente :
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

