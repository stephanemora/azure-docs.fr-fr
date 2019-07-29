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
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 389d467bd9672743d4a086e8a1c505fb0366dba7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237110"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Activer et utiliser le Bureau à distance pour les machines virtuelles Linux dans un lab dans Azure Lab Services
Cet article explique comment effectuer les tâches suivantes :

- Activer Bureau à distance pour une machine virtuelle Linux
- Comment les enseignants peuvent se connecter au modèle de machine virtuelle via une connexion Bureau à distance (RDP).
- Comment les étudiants se connectent à la machine virtuelle d'étudiant via RDP

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

## <a name="students-connecting-to-the-student-vm"></a>Étudiants se connectant à la machine virtuelle d’étudiant
Les étudiants peuvent se connecter via RDP à leurs machines virtuelles Linux après que le propriétaire du lab (enseignant) a **publié** le modèle de machine virtuelle avec les packages RDP et GUI installés. Voici la procédure à suivre : 

1. Lorsqu’un étudiant se connecte directement au portail Labs (`https://labs.azure.com`) ou à l'aide d'un lien d’inscription (`https://labs.azure.com/register/<registrationCode>`), une vignette correspondant à chaque lab auquel l'étudiant a accès s'affiche. 
2. Sur la vignette, sélectionnez **Démarrer** si la machine virtuelle est arrêtée. 
3. Sélectionnez **Connecter**. Deux options de connexion à la machine virtuelle vous sont proposées : **SSH** et **Bureau à distance**.

    ![Machine virtuelle d'étudiant - options de connexion](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Se connecter à l’aide de SSH ou RDP
Si vous sélectionnez l'option **SSH**, la boîte de dialogue **Se connecter à votre machine virtuelle** s'affiche :  

![Chaîne de connexion SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Sélectionnez le bouton **Copier** en regard de la zone de texte pour la copier dans le Presse-papiers. Enregistrez la chaîne de connexion SSH. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle.

Si vous sélectionnez l'option **RDP**, un fichier RDP est téléchargé sur votre ordinateur. Enregistrez-le et ouvrez-le pour vous connecter à la machine. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)

