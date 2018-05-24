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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 362967ca9ed9e246f39a6f8ceae4783d1e2cbf13
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361163"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Accéder à un laboratoire de classe dans Azure Lab Services
Cet article décrit comment accéder à un laboratoire de classe, se connecter à la machine virtuelle du laboratoire et arrêter la machine virtuelle. 

## <a name="view-all-the-classroom-labs"></a>Afficher tous les laboratoires de classe

1. Accédez à **l’URL d’inscription** que vous avez reçue du professeur/formateur. 
2. Connectez-vous au service à l’aide de votre compte scolaire pour terminer l’inscription. 
3. Une fois l’inscription terminée, confirmez que vous voyez les machines virtuelles des laboratoires auxquels vous avez accès. 

    ![Afficher tous les laboratoires](./media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Se connecter à la machine virtuelle du laboratoire de salle de classe

1. Sélectionnez **Se connecter** sur la vignette qui représente la machine virtuelle du laboratoire auquel vous souhaitez accéder.

    ![Afficher tous les laboratoires](./media/how-to-use-classroom-lab/connect-button.png)
2. Quelques minutes sont nécessaire pour que la machine virtuelle soit prête.

    ![Préparation de la machine virtuelle](./media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Enregistrez le fichier RDP sur le disque dur et ouvrez-le. 
    
    ![Enregistrer le fichier RDP](./media/how-to-use-classroom-lab/save-rdp-file.png)
4. Utilisez le **nom d’utilisateur** et le **mot de passe** obtenus auprès de votre formateur/profession pour vous connecter à la machine. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Arrêter la machine virtuelle d’un laboratoire de classe

Sélectionnez **Arrêter** sur la vignette qui représente le laboratoire de classe. Lorsque la machine virtuelle est arrêtée, le bouton **Démarrer** sur la vignette est activé. 

## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire à l’aide d’Azure Lab Services :

- [Configurer un laboratoire de classe](how-to-manage-classroom-labs.md)
- [Configurer un laboratoire personnalisé](tutorial-create-custom-lab.md)

 