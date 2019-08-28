---
title: Accéder à un laboratoire de classe dans Azure Lab Services | Microsoft Docs
description: Dans ce didacticiel, vous accédez aux machines virtuelles dans un laboratoire de classe qui est configuré par un professeur.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 769cb2c6a3ae0e5b94ffd65425634ac9dd0221d0
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640081"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Didacticiel : Accéder à un laboratoire de salle de classe dans Azure Lab Services
Dans ce didacticiel, vous vous connectez en tant qu’étudiant à une machine virtuelle dans un laboratoire de classe. 

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Vous inscrire au labo
> * Démarrer la machine virtuelle
> * Connexion à la machine virtuelle

## <a name="register-to-the-lab"></a>Vous inscrire au labo

1. Accédez à **l’URL d’inscription** que vous avez reçue du professeur/formateur. Vous n’avez pas besoin d’utiliser l’URL d’inscription après avoir effectué l’enregistrement. À la place, utilisez l’URL : [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 n’est pas encore pris en charge. 
1. Connectez-vous au service à l’aide de votre compte scolaire pour terminer l’inscription. 
2. Une fois l’inscription terminée, confirmez que vous visualisez bien les machines virtuelles du laboratoire auquel vous avez accès. 
3. Attendez que la machine virtuelle soit prête. Vous pouvez voir les champs suivants sur la vignette de la machine virtuelle :
    1. En haut de la vignette, vous voyez le **nom du labo**.
    1. À droite, vous voyez l’icône représentant le **système d’exploitation (SE)** de la machine virtuelle. Dans cet exemple, il s’agit du système d’exploitation Windows. 
    1. La barre de progression sur la vignette indique le nombre d’heures d’utilisation par rapport aux [heures de quota](how-to-configure-student-usage.md#set-quotas-for-users) qui vous sont attribuées. Il s’agit du temps qui vous est alloué en plus du temps planifié pour le labo. 
    1. Les icônes/boutons situés en bas de la vignette vous permettent de démarrer/d’arrêter la machine virtuelle et de vous y connecter. 
    1. À droite des boutons, vous voyez l’état de la machine virtuelle. Vérifiez que l’état de la machine virtuelle est **Arrêtée**. 

        ![Machine virtuelle à l’état Arrêtée](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Démarrer la machine virtuelle
1. **Démarrez** la machine virtuelle en cliquant sur le premier bouton comme illustré dans l’image suivante. Ce processus prend un certain temps.  

    ![Démarrer la machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Vérifiez que l’état de la machine virtuelle est **En cours d’exécution**. 

    ![Machine virtuelle à l’état En cours d’exécution](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Notez que l’icône du premier bouton a changé et qu’elle représente maintenant une action d’**arrêt**. Vous pouvez cliquer sur ce bouton pour arrêter la machine virtuelle. 

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

1. Cliquez sur le deuxième bouton comme illustré dans l’image suivante pour vous **connecter** à la machine virtuelle du labo. 

    ![Se connecter à une machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Effectuez l’une des étapes suivantes : 
    1. Pour les machines virtuelles **Windows**, enregistrez le fichier **RDP** sur le disque dur. Ouvrez le fichier RDP pour vous connecter à la machine virtuelle. Utilisez le **nom d’utilisateur** et le **mot de passe** obtenus auprès de votre formateur/professeur pour vous connecter à la machine. 
    3. Pour les machines virtuelles **Linux**, vous pouvez utiliser **SSH** ou **RDP** (s’il est activé) pour vous y connecter. Pour plus d’informations, consultez [Activer la connexion Bureau à distance pour les machines Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez accédé à un laboratoire de classe à l’aide du lien d’inscription obtenu auprès de votre formateur/professeur.

En tant que propriétaire de laboratoire, vous souhaitez voir qui y est inscrit et suivre l’utilisation des machines virtuelles. Passez au tutoriel suivant pour savoir comment effectuer le suivi de l’utilisation du laboratoire :

> [!div class="nextstepaction"]
> [Suivre l’utilisation d’un laboratoire](tutorial-track-usage.md) 
