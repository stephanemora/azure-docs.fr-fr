---
title: Activer le Bureau à distance pour Linux dans Azure Lab Services | Microsoft Docs
description: Découvrez comment activer le Bureau à distance pour les machines virtuelles Linux dans un lab dans Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: dfd16d0977504e598492225238d29c83fcb7a028
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598060"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Activer le Bureau à distance pour les machines virtuelles Linux dans un labo Azure Lab Services
Cet article explique comment effectuer les tâches suivantes :

- Activer Bureau à distance pour une machine virtuelle Linux
- Comment les enseignants peuvent se connecter au modèle de machine virtuelle via une connexion Bureau à distance (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Activer Bureau à distance pour une machine virtuelle Linux
Lors de la création du lab, les enseignants peuvent activer une **connexion Bureau à distance** pour les images **Linux**. L'option **Activer la connexion Bureau à distance** s'affiche lorsqu'une image Linux est sélectionnée pour le modèle. Lorsque cette option est activée, les enseignants peuvent se connecter au modèle de machine virtuelle et aux machines virtuelles des étudiants via RDP (Bureau à distance). 

![Activer la connexion Bureau à distance pour une image Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Dans la zone de message **Activation de la connexion Bureau à distance**, sélectionnez **Continuer avec le Bureau à distance**. 

![Activer la connexion Bureau à distance pour une image Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> L’activation de la **connexion Bureau à distance** ouvre uniquement le port **RDP** sur les machines Linux. Si RDP est déjà installé et configuré sur l’image de machine virtuelle, vous ou les étudiants pouvez vous connecter aux machines virtuelles via RDP sans suivre d’étapes supplémentaires.
> 
> Si le protocole RDP n’est pas installé et configuré sur l’image de machine virtuelle, vous devez vous connecter à la machine Linux à l’aide de SSH la première fois et installer les packages RDP et GUI afin que vous ou les étudiants puissiez vous connecter à la machine Linux à l’aide de RDP ensuite. Pour plus d’informations, voir [Installer et configurer le Bureau à distance pour effectuer une connexion à une machine virtuelle Linux dans Azure](../../virtual-machines/linux/use-remote-desktop.md). Publiez ensuite l’image pour permettre aux étudiants de se connecter à l’aide de RDP aux machines virtuelles Linux d’étudiant. 

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Actuellement, la connexion Bureau à distance est prise en charge pour les systèmes d’exploitation suivants :

- openSUSE Leap 42.3
- CentOS-based 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Se connecter au modèle de machine virtuelle 
Les enseignants doivent se connecter au modèle de machine virtuelle à l’aide de SSH la première fois, puis installer les packages RDP et GUI. Ensuite, les enseignants peuvent utiliser le protocole RDP pour se connecter au modèle de machine virtuelle : 

1. Si vous voyez l’option **Personnaliser le modèle** dans la barre d’outils, sélectionnez-la. Ensuite, sélectionnez **Continuer** dans la boîte de dialogue **Personnaliser le modèle**. Cette action démarre le modèle de machine virtuelle.  

    ![Personnaliser le modèle](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Une fois le modèle de machine virtuelle démarré, vous pouvez sélectionner **Se connecter au modèle** puis **Se connecter via le protocole SSH** dans la barre d’outils. 

    ![Se connecter au modèle via RDP une fois le lab créé](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. La boîte de dialogue **Se connecter à votre machine virtuelle** suivante s’affiche. Sélectionnez le bouton **Copier** en regard de la zone de texte pour la copier dans le Presse-papiers. Enregistrez la chaîne de connexion SSH. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle.
 
    ![Chaîne de connexion SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installez les packages RDP et GUI pour que vous ou les étudiants puissiez vous connecter ultérieurement à la machine Linux avec RDP. Pour plus d’informations, voir [Installer et configurer le Bureau à distance pour effectuer une connexion à une machine virtuelle Linux dans Azure](../../virtual-machines/linux/use-remote-desktop.md). Publiez ensuite l’image pour permettre aux étudiants de se connecter à l’aide de RDP aux machines virtuelles Linux d’étudiant.
5. Une fois ces packages installés, vous pouvez utiliser l’option **Se connecter au modèle** dans la barre d’outils, puis sélectionner **Se connecter via RDP** pour vous connecter au modèle de machine virtuelle via RDP. Enregistrez le fichier RDP et utilisez-le pour vous connecter au modèle de machine virtuelle via RDP. 

## <a name="next-steps"></a>Étapes suivantes
Une fois qu’un formateur a activé la fonctionnalité de connexion du Bureau à distance, les étudiants peuvent se connecter à leurs machines virtuelles via le protocole RDP/SSH. Pour plus d’informations, voir [Utiliser le Bureau à distance pour des machines virtuelles Linux dans un labo de classe](how-to-use-remote-desktop-linux-student.md). 