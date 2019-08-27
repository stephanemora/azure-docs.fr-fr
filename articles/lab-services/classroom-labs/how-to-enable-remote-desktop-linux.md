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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 3d08105e78274300eb7ee0a8c0ad146a737d0ffa
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644949"
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
> L’activation de la **connexion Bureau à distance** ouvre uniquement le port **RDP** sur les machines Linux. En tant qu’enseignant, connectez-vous à la machine Linux à l’aide de SSH la première fois, et installez les packages RDP et GUI de manière à vous connecter à la machine Linux à l'aide de RDP ensuite. **Publiez** ensuite l'image pour permettre aux étudiants de se connecter à l'aide de RDP aux machines virtuelles Linux d'étudiant. 

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Actuellement, la connexion Bureau à distance est prise en charge pour les systèmes d’exploitation suivants :

- openSUSE Leap 42.3
- CentOS-based 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Enseignants se connectant à un modèle de machine virtuelle à l'aide de RDP
Les enseignants doivent se connecter au modèle de machine virtuelle à l’aide de SSH la première fois, puis installer les packages RDP et GUI. Ensuite, les enseignants peuvent suivre la procédure ci-dessous pour se connecter aux machines virtuelles Linux à l’aide de RDP : 

Utilisez l'option **Bureau à distance** pour vous connecter au modèle de machine virtuelle lors de la création du lab. 

![Se connecter au modèle via RDP lors de la création](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

L'option **Bureau à distance** s'affiche sur la page d’accueil du lab une fois le lab créé et le modèle de machine virtuelle démarré. Si ce n'est pas déjà fait, démarrez le modèle de machine virtuelle. 

![Se connecter au modèle via RDP une fois le lab créé](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Pour plus d’informations sur la connexion à la machine virtuelle à l’aide de SSH ou RDP, consultez [Se connecter à l’aide de SSH ou RDP]((#connect-using-ssh-or-rdp). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Enseignants se connectant à une machine virtuelle d'étudiant à l'aide de RDP
Les enseignants peuvent se connecter à une machine virtuelle d’étudiant en basculant vers la vue **Machines virtuelles**, puis en sélectionnant l'icône **Se connecter**. Mais avant, ils doivent **publier** l’image du modèle avec les packages RDP et GUI installés. 

![Enseignants se connectant à la machine virtuelle d’étudiant](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Pour plus d’informations sur la connexion à la machine virtuelle à l’aide de SSH ou RDP, consultez [Se connecter à l’aide de SSH ou RDP]((#connect-using-ssh-or-rdp). 

## <a name="connect-using-ssh-or-rdp"></a>Se connecter à l’aide de SSH ou RDP
Si vous sélectionnez l'option **SSH**, la boîte de dialogue **Se connecter à votre machine virtuelle** s'affiche :  

![Chaîne de connexion SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Sélectionnez le bouton **Copier** en regard de la zone de texte pour la copier dans le Presse-papiers. Enregistrez la chaîne de connexion SSH. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle.

Si vous sélectionnez l'option **RDP**, un fichier RDP est téléchargé sur votre ordinateur. Enregistrez-le et ouvrez-le pour vous connecter à la machine. 

## <a name="next-steps"></a>Étapes suivantes
Une fois qu’un formateur a activé la fonctionnalité de connexion du Bureau à distance, les étudiants peuvent se connecter à leurs machines virtuelles via le protocole RDP/SSH. Pour plus d’informations, voir [Utiliser le Bureau à distance pour des machines virtuelles Linux dans un labo de classe](how-to-use-remote-desktop-linux-student.md). 