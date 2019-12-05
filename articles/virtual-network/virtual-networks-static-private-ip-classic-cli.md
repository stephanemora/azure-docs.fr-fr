---
title: Configurer des adresses IP privées pour des machines virtuelles (Classic) - CLI
titlesuffix: Azure Virtual Network
description: Découvrez comment configurer des adresses IP privées pour les machines virtuelles (Classic) à l’aide de l’interface de ligne de commande (CLI) Azure Classic.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 9deaf4b1d80ad4e55e7c971998e8b1f5ea562257
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196577"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Configurer des adresses IP privées pour une machine virtuelle (Classic) à l’aide d’Azure Classic CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement de Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Les exemples de commandes Azure Classic CLI suivants attendent un environnement simple déjà créé. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée statique lors de la création d’une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans un nouveau service cloud nommé *TestService* conformément au scénario ci-dessus, procédez comme suit :

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](/cli/azure/install-cli-version-1.0) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **azure service create** pour créer le service cloud.
   
        azure service create TestService --location uscentral
   
    Sortie attendue :
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Exécutez la commande **azure create vm** commande pour créer la machine virtuelle. Notez la valeur d’une adresse IP privée statique. La liste affichée après le résultat présente les différents paramètres utilisés.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Sortie attendue :
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (ou --location)** . Région Azure où la machine virtuelle sera créée. Pour notre scénario, *centralus*.
   * **-n (ou--vm-name)** . Nom de la machine virtuelle à créer.
   * **-w (ou --virtual-network-name)** . Nom du réseau virtuel où la machine virtuelle sera créée. 
   * **-S (ou --static-ip)** . Adresse IP privée statique de la machine virtuelle.
   * **TestService**. Nom du service cloud dans lequel la machine virtuelle sera créée.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Image utilisée pour créer la machine virtuelle.
   * **adminuser**. Administrateur local pour la machine virtuelle Windows.
   * <strong>AdminP@ssw0rd</strong>. Mot de passe administrateur local pour la machine virtuelle Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Récupération d’informations d’adresse IP privée statique pour une machine virtuelle
Pour visualiser les informations d’adresse IP privée statique concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande d’interface de ligne de commande Azure ci-après, et examinez la valeur *Network StaticIP*:

    azure vm static-ip show DNS01

Sortie attendue :

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Suppression d’une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique ajoutée à la machine virtuelle dans le script ci-dessus, exécutez la commande d’interface de ligne de commande Azure suivante :

    azure vm static-ip remove DNS01

Sortie attendue :

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Ajout d’une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

    azure vm static-ip set DNS01 192.168.1.101

Sortie attendue :

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Définir des adresses IP au sein du système d’exploitation

Il est recommandé de ne pas statiquement assigner l’IP privée assignée à la machine virtuelle Azure au sein du système d’exploitation d’une machine virtuelle, sauf si nécessaire. Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’il s’agit de la même adresse que l’adresse IP privée assignée à la machine virtuelle Azure ou vous pouvez perdre la connectivité à la machine virtuelle. Vous ne devez jamais assigner manuellement l’adresse IP publique assignée à une machine virtuelle Azure dans le système d’exploitation de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [adresses IP publiques réservées](virtual-networks-reserved-public-ip.md) .
* En savoir plus sur les [adresses IP publiques de niveau d’instance](virtual-networks-instance-level-public-ip.md) .
* Consulter les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).