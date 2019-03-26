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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3ec3abffc7962051f4cfc02d5369581ca193d70e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775575"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Didacticiel : Accéder à un laboratoire de salle de classe dans Azure Lab Services
Dans ce didacticiel, vous vous connectez en tant qu’étudiant à une machine virtuelle dans un laboratoire de classe. 

Dans ce didacticiel, vous effectuez les actions suivantes :

> [!div class="checklist"]
> * Utiliser le lien d’inscription 
> * Connectez-vous à la machine virtuelle.

## <a name="use-the-registration-link"></a>Utiliser le lien d’inscription

1. Accédez à **l’URL d’inscription** que vous avez reçue du professeur/formateur. Vous n’avez pas besoin d’utiliser l’URL d’inscription après avoir effectué l’enregistrement. À la place, utilisez l’URL : [https://labs.azure.com](https://labs.azure.com). 
1. Connectez-vous au service à l’aide de votre compte scolaire pour terminer l’inscription. 
2. Une fois l’inscription terminée, confirmez que vous visualisez bien les machines virtuelles du laboratoire auquel vous avez accès. 
3. Attendez que la machine virtuelle soit prête, puis **démarrez-la**. Ce processus prend un certain temps.  

    ![Démarrer la machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

1. Sélectionnez **Se connecter** sur la vignette de la machine virtuelle du laboratoire auquel vous souhaitez accéder. 

    ![Se connecter à une machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Effectuez l’une des étapes suivantes : 
    1. Pour les machines virtuelles **Windows**, enregistrez le fichier **RDP** sur le disque dur. Ouvrez le fichier RDP pour vous connecter à la machine virtuelle. Utilisez le **nom d’utilisateur** et le **mot de passe** obtenus auprès de votre formateur/professeur pour vous connecter à la machine. 
    3. Pour les machines virtuelles **Linux**, copiez et enregistrez la chaîne de connexion SSH dans la boîte de dialogue **Se connecter à votre machine virtuelle**. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez accédé à un laboratoire de classe à l’aide du lien d’inscription obtenu auprès de votre formateur/professeur.

En tant que propriétaire de laboratoire, vous souhaitez voir qui y est inscrit et suivre l’utilisation des machines virtuelles. Passez au tutoriel suivant pour savoir comment effectuer le suivi de l’utilisation du laboratoire :

> [!div class="nextstepaction"]
> [Suivre l’utilisation d’un laboratoire](tutorial-track-usage.md) 