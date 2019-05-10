---
title: Activer le Bureau à distance pour Linux dans Azure Lab Services | Microsoft Docs
description: Découvrez comment activer le Bureau à distance pour les machines virtuelles Linux dans un laboratoire dans Azure Lab Services.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 9ad6f82d7b9bd7c4957df1dd37d0f2ddf7462e9e
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410947"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Activer et utiliser le Bureau à distance pour les machines virtuelles Linux dans un laboratoire dans Azure Lab Services
Cet article vous montre comment effectuer les tâches suivantes :

- Activer le Bureau à distance pour Linux VM
- Comment les enseignants peuvent se connecter au modèle de machine virtuelle par le biais de connexions RDP (Remote Desktop).
- Comment les étudiants se connectent à la machine virtuelle via RDP étudiant

## <a name="enable-remote-desktop-for-linux-vm"></a>Activer le Bureau à distance pour Linux VM
Lors de la création de laboratoire, les enseignants peuvent activer **connexion Bureau à distance** pour **Linux** images. Le **activer une connexion Bureau à distance** option est affichée lorsqu’une image Linux est sélectionnée pour le modèle. Lorsque cette option est activée, les enseignants peuvent se connecter au modèle de machine virtuelle et les machines virtuelles d’étudiant via RDP (bureau à distance). 

![Activer une connexion Bureau à distance pour une image Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

> [!IMPORTANT] 
> L’activation de **connexion Bureau à distance** ouvre uniquement le **RDP** port sur les machines Linux. Vous, en tant qu’enseignant, connectez à la machine Linux à l’aide de SSH pour la première fois et que vous installez les packages RDP et l’interface utilisateur graphique afin que vous pouvez vous connecter à la machine Linux à l’aide de RDP ultérieurement. Ensuite, vous **publier** l’image afin que les étudiants peuvent RDP dans pour les étudiants, les machines virtuelles Linux. 

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Actuellement, la connexion Bureau à distance est prise en charge pour les systèmes d’exploitation suivants :

- openSUSE Leap 42.3
- CentOS-based 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Les enseignants de la connexion au modèle de machine virtuelle à l’aide de RDP
Les enseignants doivent se connecter au modèle de machine virtuelle à l’aide de SSH tout d’abord et installer des packages RDP et l’interface graphique utilisateur dessus. Ensuite, les enseignants peuvent procédez comme suit pour vous connecter à des machines virtuelles Linux à l’aide de RDP : 

Vous voyez la **Bureau à distance** option pour vous connecter au modèle de machine virtuelle au moment de la création du laboratoire. 

![Se connecter au modèle via RDP au moment de la création](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Vous voyez la **Bureau à distance** option sur la page d’accueil du laboratoire après avoir créé le laboratoire et le modèle de machine virtuelle est démarrée. Démarrer le modèle de machine virtuelle si elle n’est pas déjà démarré. 

![Se connecter au modèle par le biais de RDP après avoir créé le laboratoire](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Lorsque vous sélectionnez le **RDP** option, il télécharge un fichier RDP. Vous ouvrez pour vous connecter à la machine Linux. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Les enseignants de la connexion à un machine virtuelle de l’étudiant à l’aide de RDP
Un propriétaire de laboratoire (enseignant) peut se connecter à un machine virtuelle d’étudiant en basculant vers le **Machines virtuelles** afficher, puis en sélectionnant le **connecter** icône. Avant cela, les enseignants doivent **publier** l’image de modèle avec les packages RDP et l’interface graphique utilisateur installé dessus. 

![Enseignants de la connexion à la machine virtuelle de l’étudiant](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>Étudiants de la connexion à la machine virtuelle de l’étudiant
Étudiant peut RDP dans pour leurs machines virtuelles Linux après le propriétaire de laboratoire (enseignant) **publie** le modèle de machine virtuelle avec RDP et l’interface graphique utilisateur des packages installés sur l’ordinateur. Voici la procédure à suivre : 

1. Lorsqu’un étudiant se connecte au portail Labs directement (`https://labs.azure.com`) ou à l’aide d’un lien d’inscription (`https://labs.azure.com/register/<registrationCode>`), une vignette pour chaque laboratoire l’étudiant a accès à s’affiche. 
2. Sur la vignette, sélectionnez **Démarrer** si la machine virtuelle est arrêtée. 
3. Sélectionnez **Connecter**. Cette action télécharge le fichier RDP à votre machine. Enregistrer et l’ouvrir pour vous connecter à l’ordinateur Linux via RDP. 

    ![Étudiant machine virtuelle - RDP télécharger](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    Vous pouvez toujours vous connecter à la VM Linux à l’aide de SSH. Sélectionnez **... (points de suspension)**  pour voir l’option de SSH. 
    
    ![Étudiant machine virtuelle - SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    Copiez et enregistrez la chaîne de connexion SSH sur le **se connecter à votre machine virtuelle** boîte de dialogue. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)

