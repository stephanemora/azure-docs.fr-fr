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
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: dadc90e6a39b9e9689bab0249e6496fdea6f6205
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450213"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Didacticiel : Accéder à un laboratoire de classe dans Azure Lab Services
Dans ce didacticiel, vous vous connectez en tant qu’étudiant à une machine virtuelle dans un laboratoire de classe. 

Dans ce didacticiel, vous effectuez les actions suivantes :

> [!div class="checklist"]
> * Utiliser le lien d’inscription 
> * Connectez-vous à la machine virtuelle.

## <a name="use-the-registration-link"></a>Utiliser le lien d’inscription

1. Accédez à **l’URL d’inscription** que vous avez reçue du professeur/formateur. 
2. Connectez-vous au service à l’aide de votre compte scolaire pour terminer l’inscription. 
3. Une fois l’inscription terminée, confirmez que vous visualisez bien les machines virtuelles du laboratoire auquel vous avez accès. 
2. Attendez que la machine virtuelle soit prête, puis **démarrez-la**.

    ![Démarrer la machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

1. Sélectionnez **Se connecter** sur la vignette qui représente la machine virtuelle du laboratoire auquel vous souhaitez accéder. 

    ![Se connecter à une machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Enregistrez le fichier RDP sur le disque dur et ouvrez-le. 
3. Utilisez le **nom d’utilisateur** et le **mot de passe** obtenus auprès de votre formateur/profession pour vous connecter à la machine. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez accédé à un laboratoire de classe à l’aide du lien d’inscription obtenu auprès de votre formateur/professeur.

En tant que propriétaire d’un laboratoire, vous souhaitez afficher qui y est inscrit et suivre l’utilisation des machines virtuelles. Passez au didacticiel suivant pour savoir comment :

> [!div class="nextstepaction"]
> [Suivre l’utilisation d’un laboratoire](tutorial-track-usage.md) 