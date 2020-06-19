---
title: Suivre l’utilisation d’un laboratoire dans Azure Lab Services | Microsoft Docs
description: Dans ce didacticiel, vous (en tant que créateur et propriétaire d’un laboratoire) allez suivre l’utilisation de votre laboratoire.
services: lab-services
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: e7f260904db3c8aaaa83973b6c8b565134c02616
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84889431"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Didacticiel : suivre l’utilisation d’un labo dans Azure Lab Service
Ce didacticiel vous montre comment un créateur ou propriétaire de laboratoire peut suivre l’utilisation d’un laboratoire.

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Afficher les utilisateurs inscrits à votre laboratoire
> * Afficher l’utilisation des machines virtuelles dans le laboratoire
> * Gérer les machines virtuelles des étudiants 


## <a name="view-registered-users"></a>Afficher les utilisateurs inscrits

1. Accédez au [site web Azure Lab Services](https://labs.azure.com). 
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. Azure Lab Services prend en charge les comptes professionnels et les comptes Microsoft.
3. Sur la page **Mes laboratoires**, sélectionnez le laboratoire pour lequel vous souhaitez suivre l’utilisation. 
4. Sélectionnez **Utilisateurs** dans le menu de gauche ou la vignette **Utilisateurs**. Vous visualisez les étudiants inscrits à votre laboratoire.  

    ![Utilisateurs inscrits](./media/tutorial-track-usage/registered-users.png)

    Pour plus d’informations sur l’ajout et la gestion d’utilisateurs du labo, consultez [Ajouter et gérer des utilisateurs du labo](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Afficher l’utilisation des machines virtuelles

1. Dans le menu de gauche, sélectionnez **Machines virtuelles**. 
2. Confirmez que vous visualisez bien l’état des machines virtuelles ainsi que le nombre d’heures d’exécution de celles-ci. Le temps qu’un propriétaire de laboratoire passe sur la machine virtuelle d’un étudiant n’est pas comptabilisé dans le temps d’utilisation indiqué dans la dernière colonne. 

    ![Utilisation de machines virtuelles](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gérer les machines virtuelles des étudiants 
Dans cette page, vous pouvez démarrer, arrêter ou réinitialiser les machines virtuelles des étudiants en utilisant les contrôles de la colonne **État** ou de la barre d’outils.

![Actions de machine virtuelle](./media/tutorial-track-usage/vm-controls.png)

Pour plus d’informations sur la gestion des pools de machines virtuelles du labo, consultez [Configurer et gérer un pool de machines virtuelles](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Lorsqu’un formateur active une machine virtuelle d’un étudiant, le quota de l’étudiant n’est pas affecté. Le quota d’un utilisateur spécifie le nombre d’heures de labo disponibles pour l’utilisateur en dehors des heures de classe planifiées. Pour plus d’informations sur les quotas, consultez [Définir des quotas pour les utilisateurs](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les laboratoires de classe, consultez les articles de la section [Guides pratiques](how-to-manage-lab-accounts.md).
