---
title: Accéder à un labo de classe dans Azure Lab Services | Microsoft Docs
description: Dans ce tutoriel, vous accédez aux machines virtuelles d’un labo de classe mis en place par un professeur.
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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: b5a55a35dc4baacf8248adad3133220214050eeb
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706685"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Accéder à un labo de classe dans Azure Lab Services
Cet article décrit comment accéder à un labo de classe, se connecter à la machine virtuelle du labo et arrêter la machine virtuelle. 

## <a name="register-to-a-lab"></a>S’inscrire à un labo
1. Accédez à **l’URL d’inscription** que vous avez reçue du professeur/formateur. 
2. Connectez-vous au service à l’aide de votre compte scolaire pour procéder à l’inscription. 
3. Une fois l’inscription terminée, confirmez que vous visualisez bien les machines virtuelles du labo auquel vous avez accès. 
2. Attendez que la machine virtuelle soit prête, puis **démarrez-la**. Ce processus prend un certain temps.  

    ![Démarrer la machine virtuelle](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Voir tous les labos de classe
Une fois que vous êtes inscrit, vous pouvez voir tous les labos de classe en effectuant les étapes suivantes : 

1. Accédez à [https://labs.azure.com](https://labs.azure.com). 
2. Connectez-vous au service à l’aide du compte d’utilisateur que vous avez utilisé pour vous inscrire au labo. 
3. Confirmez que vous voyez tous les labos auxquels vous avez accès. 

    ![Voir tous les labos](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Se connecter à la machine virtuelle d’un labo de classe

1. Si la machine virtuelle n’a pas encore été démarrée, faites-le en sélectionnant **Démarrer**.
2. Sélectionnez **Se connecter** sur la vignette qui représente la machine virtuelle du labo auquel vous souhaitez accéder. 

    ![Voir tous les labos](../media/how-to-use-classroom-lab/connect-button.png)
3. Enregistrez le fichier RDP (pour les machines virtuelles Windows) sur le disque dur, puis ouvrez-le. 
4. Utilisez le **nom d’utilisateur** et le **mot de passe** obtenus auprès de votre formateur/profession pour vous connecter à la machine. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Arrêter la machine virtuelle d’un labo de classe

Pour arrêter votre machine virtuelle, sélectionnez **Arrêter** sur la vignette. Lorsque la machine virtuelle est arrêtée, le bouton **Démarrer** sur la vignette est activé. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
 