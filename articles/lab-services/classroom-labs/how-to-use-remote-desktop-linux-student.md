---
title: Utiliser le Bureau à distance pour Linux dans Azure Lab Services | Microsoft Docs
description: Découvrez comment utiliser le Bureau à distance pour les machines virtuelles Linux dans un labo dans Azure Lab Services.
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
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588103"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Utiliser le Bureau à distance pour les machines virtuelles Linux dans un labo de classe dans Azure Lab Services
Cet article explique comment les étudiants peuvent se connecter à une machine virtuelle Linux d’un labo en utilisant RDP/SSH. 

Un formateur doit activer la fonctionnalité de connexion du Bureau à distance pour que les étudiants puissent se connecter à leurs machines virtuelles de classe. Pour obtenir des instructions sur la façon dont un formateur peut activer la fonctionnalité de connexion du Bureau à distance, consultez [Activer le Bureau à distance pour les machines virtuelles Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> L’activation de la **connexion Bureau à distance** ouvre uniquement le port **RDP** sur les machines Linux. Un formateur peut se connecter à la machine Linux à l’aide de SSH la première fois, et installer les packages RDP et GUI de manière à se connecter à la machine Linux à l'aide de RDP ensuite. 

## <a name="connect-to-the-student-vm"></a>Se connecter-vous à la machine virtuelle de l’étudiant
Les étudiants peuvent se connecter via RDP à leurs machines virtuelles Linux après que le propriétaire du laboratoire (formateur) a **publié** le modèle de machine virtuelle avec les packages RDP et GUI installés. Voici la procédure à suivre : 

1. Lorsqu’un étudiant se connecte directement au portail Labs (`https://labs.azure.com`) ou à l'aide d'un lien d’inscription (`https://labs.azure.com/register/<registrationCode>`), une vignette correspondant à chaque lab auquel l'étudiant a accès s'affiche. 
2. Sur la vignette, basculez le bouton pour démarrer la machine virtuelle si elle est en état arrêté. 
3. Sélectionnez **Connecter**. Deux options de connexion à la machine virtuelle vous sont proposées : **SSH** et **Bureau à distance**.

    ![Machine virtuelle d'étudiant - options de connexion](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Se connecter à l’aide de SSH ou RDP
Si vous sélectionnez l'option **SSH**, la boîte de dialogue **Se connecter à votre machine virtuelle** s'affiche :  

![Chaîne de connexion SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Sélectionnez le bouton **Copier** en regard de la zone de texte pour la copier dans le Presse-papiers. Enregistrez la chaîne de connexion SSH. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle.

Si vous sélectionnez l'option **RDP**, un fichier RDP est téléchargé sur votre ordinateur. Enregistrez-le et ouvrez-le pour vous connecter à la machine. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’activation de la fonctionnalité de connexion du Bureau à distance pour les machines virtuelles Linux d’un labo de classe, consultez [Activer le Bureau à distance pour les machines virtuelles Linux](how-to-enable-remote-desktop-linux.md). 

