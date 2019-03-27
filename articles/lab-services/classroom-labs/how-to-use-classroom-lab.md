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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 7c4a73c86245067a07dc253041c43ae6c17f7172
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096977"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Accéder à un laboratoire de classe dans Azure Lab Services
Cet article décrit comment accéder à un laboratoire de classe, se connecter à la machine virtuelle du laboratoire et arrêter la machine virtuelle. 

## <a name="register-to-a-lab"></a>S’inscrire à un labo
1. Accédez à **l’URL d’inscription** que vous avez reçue du professeur/formateur. 
2. Connectez-vous au service à l’aide de votre compte scolaire pour terminer l’inscription. 
3. Une fois l’inscription terminée, confirmez que vous visualisez bien les machines virtuelles du laboratoire auquel vous avez accès. 
2. Attendez que la machine virtuelle soit prête, puis **démarrez-la**. Ce processus prend un certain temps.  

    ![Démarrer la machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Afficher tous les laboratoires de classe
Une fois que vous êtes inscrit, vous pouvez voir tous les labos de classe en effectuant les étapes suivantes : 

1. Accédez à [https://labs.azure.com](https://labs.azure.com). 
2. Connectez-vous au service à l’aide du compte d’utilisateur que vous avez utilisé pour vous inscrire au labo. 
3. Confirmez que vous voyez tous les labos auxquels vous avez accès. 

    ![Afficher tous les laboratoires](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Se connecter à la machine virtuelle du laboratoire de salle de classe

1. Démarrez la machine virtuelle si elle ne l’est pas déjà, en sélectionnant **Démarrer** dans la vignette. 
2. Sélectionnez **Se connecter** sur la vignette qui représente la machine virtuelle du laboratoire auquel vous souhaitez accéder. 
3. Effectuez l’une des étapes suivantes : 
   1. Pour les machines virtuelles **Windows**, enregistrez le fichier **RDP** sur le disque dur. Ouvrez le fichier RDP pour vous connecter à la machine virtuelle. Utilisez le **nom d’utilisateur** et le **mot de passe** obtenus auprès de votre formateur/profession pour vous connecter à la machine. 
   3. Pour les machines virtuelles **Linux**, copiez et enregistrez la chaîne de connexion SSH dans la boîte de dialogue **Se connecter à votre machine virtuelle**. Utilisez cette chaîne de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle.
      ## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Arrêter la machine virtuelle d’un laboratoire de classe

Pour arrêter votre machine virtuelle, sélectionnez **Arrêter** sur la vignette. Lorsque la machine virtuelle est arrêtée, le bouton **Démarrer** sur la vignette est activé. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
 