---
title: Comprendre les adresses IP partagées dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment Azure DevTest Labs utilise les adresses IP partagées afin de limiter les adresses IP publiques requises pour accéder aux machines virtuelles de votre laboratoire.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e7080901118dde33ed07c8a80f254b9b0d2e221c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622999"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Comprendre les adresses IP partagées dans Azure DevTest Labs

Azure DevTest Labs permet aux machines virtuelles de laboratoire de partager la même adresse IP publique afin de limiter le nombre d’adresses IP publiques requises pour accéder aux machines virtuelles de votre laboratoire individuel.  Cet article décrit le fonctionnement des adresses IP partagées et les options de configuration qui leur sont associées.

## <a name="shared-ip-setting"></a>Paramètre d’adresse IP partagée

Lorsque vous créez un laboratoire, il se trouve dans un sous-réseau de réseau virtuel.  Par défaut, ce sous-réseau est créé avec le paramètre **Enable shared public IP** (Activer l’adresse IP publique partagée) défini sur *Oui*.  Cette configuration crée une adresse IP publique pour le sous-réseau dans son ensemble.  Pour plus d’informations sur la configuration des réseaux virtuels et des sous-réseaux, consultez [Configurer un réseau virtuel dans Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nouveau sous-réseau de laboratoire](media/devtest-lab-shared-ip/lab-subnet.png)

Pour les laboratoires existants, vous pouvez activer cette option en sélectionnant **Configuration et stratégies > réseaux virtuels**. Puis, sélectionnez un réseau virtuel dans la liste et choisissez **ACTIVER L’ADRESSE IP PUBLIQUE PARTAGÉE** pour un sous-réseau sélectionné. Vous pouvez également désactiver cette option dans un laboratoire si vous ne souhaitez pas partager une adresse IP publique entre les machines virtuelles de laboratoire.

Les machines virtuelles créées dans ce laboratoire utilisent par défaut une adresse IP partagée.  Lorsque vous créez la machine virtuelle, ce paramètre peut être observé dans le panneau **Paramètres avancés** sous **Configuration de l’adresse IP**.

![Nouvelle machine virtuelle](media/devtest-lab-shared-ip/new-vm.png)

- **Partagé :** Toutes les machines virtuelles créées en tant que **partagé** sont placés dans un groupe de ressources (RG). Une seule adresse IP est affectée à ce GR et toutes les machines virtuelles de ce GR utilisent cette adresse IP.
- **Public :** Chaque machine virtuelle que vous créez possède sa propre adresse IP et est créé dans son propre groupe de ressources.
- **Privé :** Chaque machine virtuelle que vous créez utilise une adresse IP privée. Vous ne pourrez pas vous connecter directement à cette machine virtuelle depuis Internet avec le bureau à distance.

Chaque fois qu’une machine virtuelle avec adresse IP partagée activée est ajoutée au sous-réseau, DevTest Labs ajoute automatiquement la machine virtuelle à un équilibreur de charge et attribue un numéro de port TCP sur l’adresse IP publique, transfert vers le port RDP sur la machine virtuelle.  

## <a name="using-the-shared-ip"></a>Utilisation de l’adresse IP partagée

- **Utilisateurs Linux :** SSH à la machine virtuelle à l’aide de l’adresse IP ou le nom de domaine complet, suivis du signe deux-points, suivi par le port. Par exemple, dans l’image ci-dessous, l’adresse RDP pour se connecter à la machine virtuelle est `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Exemple de machine virtuelle](media/devtest-lab-shared-ip/vm-info.png)

- **Utilisateurs Windows :** Sélectionnez le **Connect** bouton sur le portail Azure pour télécharger un fichier RDP préconfiguré et accéder à la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

* [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configuration d’un réseau virtuel dans Azure DevTest Labs](devtest-lab-configure-vnet.md)





