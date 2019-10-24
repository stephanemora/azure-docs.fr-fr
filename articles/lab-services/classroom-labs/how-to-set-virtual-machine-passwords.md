---
title: Définir des mots de passe pour des machines virtuelles dans Azure Lab Services | Microsoft Docs
description: Découvrez comment définir et réinitialiser des mots de passe pour des machines virtuelles (VM) dans les labos de salle de classe de Microsoft Azure Lab Services.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 40cdd0adf7bf100e1dbca64dbba68db3bc59a4fe
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331423"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configurer et gérer un pool de machines virtuelles 
Cet article explique comment effectuer les tâches suivantes :

- Augmenter le nombre de machines virtuelles dans le laboratoire
- Démarrer toutes les machines virtuelles ou les machines virtuelles sélectionnées 
- Réinitialiser les machines virtuelles

## <a name="update-the-lab-capacity"></a>Mettre à jour la capacité du labo
Pour augmenter ou diminuer la capacité du labo (nombre de machines virtuelles dans un laboratoire), procédez comme suit :

1. Dans la page **Pool de machines virtuelles**, sélectionnez **Capacité du labo : &lt;nombre&gt; machines**.
2. Entrez le nouveau **nombre de machines virtuelles** que vous souhaitez dans le laboratoire. Ce nombre doit être supérieur ou égal au nombre d’utilisateurs inscrits dans le labo. 
3. Sélectionnez ensuite **Enregistrer**. 

    ![Bouton Démarrer tout](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Si vous avez augmenté la capacité, vous pouvez voir la ou les machines virtuelles en cours de création. 

    ![Machine virtuelle en cours de création](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Start VMs

### <a name="start-ot-stop-all-vms"></a>Démarrer ou arrêter toutes les machines virtuelles
1. Basculez vers la page **Pool de machines virtuelles**. 
2. Sélectionnez **Démarrer tout** dans la barre d’outils. 

    ![Bouton Démarrer tout](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Une fois que toutes les machines virtuelles ont démarré, vous pouvez toutes les arrêter en sélectionnant le bouton **Arrêter tout** dans la barre d’outils. 

    ![Bouton Arrêter tout](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Démarrer les machines virtuelles sélectionnées
Il existe deux façons de démarrer des machines virtuelles sélectionnées (une ou plusieurs). La première consiste à sélectionner la ou les machines virtuelles dans la liste, puis à sélectionner **Démarrer** dans la barre d’outils. La seconde consiste à sélectionner la ou les machines virtuelles dans la liste, à sélectionner la liste déroulante dans la colonne **État** de l’une des lignes, puis à sélectionner **Démarrer**. 

![Démarrer les machines virtuelles sélectionnées](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

De même, vous pouvez arrêter une ou plusieurs machines virtuelles à l’aide de la liste déroulante dans la colonne **État** ou **Arrêter** dans la barre d’outils. 

## <a name="reset-vms"></a>Réinitialiser les machines virtuelles
Pour réinitialiser une ou plusieurs machines virtuelles, sélectionnez-les dans la liste, puis sélectionnez **Réinitialiser** dans la barre d’outils. 

![Réinitialiser les machines virtuelles sélectionnées](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Dans la boîte de dialogue **Réinitialiser la ou les machines virtuelles**, sélectionnez **Réinitialiser**. 

![Boîte de dialogue Réinitialiser la machine virtuelle](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Définir un mot de passe pour des machines virtuelles
Un propriétaire de labo (enseignant) peut définir/réinitialiser le mot de passe pour des machines virtuelles au moment de la création du labo (avec l’Assistant de création de labo) ou après l’avoir créé, sur la page **Modèle**. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Définir le mot de passe au moment de la création du labo
Un propriétaire de labo (enseignant) peut définir un mot de passe pour des machines virtuelles dans le labo, sur la page **Informations d’identification de machine virtuelle** de l’Assistant de création de labo.

![Fenêtre Nouveau labo](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

En activant ou désactivant l’option **Use same password for all virtual machines** (Utiliser le même mot de passe pour toutes les machines virtuelles) figurant sur cette page, un professeur peut choisir d’utiliser le même mot de passe pour toutes les machines virtuelles du labo, ou autoriser les étudiants à définir leur propre mot de passe pour l’accès aux machines virtuelles. Par défaut, ce paramètre est activé pour toutes les images de systèmes d’exploitation Windows et Linux, à l’exception d’Ubuntu. Lorsque ce paramètre est désactivé, les étudiants sont invités à définir un mot de passe lorsqu’ils tentent de se connecter à la machine virtuelle pour la première fois. 

### <a name="reset-password-later"></a>Réinitialiser le mot de passe plus tard

1. Dans la page **Modèle** du laboratoire, sélectionnez **Réinitialiser le mot de passe** dans la barre d’outils. 

    ![Menu Réinitialiser le mot de passe figurant sur la page d’accueil](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Dans la boîte de dialogue **Réinitialiser le mot de passe**, entrez un mot de passe et sélectionnez **Réinitialiser le mot de passe**.
    
    ![Boîte de dialogue Set Password (Définir un mot de passe)](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les autres options d’utilisation que vous pouvez configurer pour les étudiants (en tant que propriétaire du labo), consultez l’article suivant : [Configure student usage](how-to-configure-student-usage.md) (Configurer l’utilisation pour les étudiants).

Pour apprendre à réinitialiser les mots de passe de leurs machines virtuelles, les étudiants peuvent consulter [Définir ou réinitialiser un mot de passe d'accès à des machines virtuelles dans un labo de salle de classe (étudiants)](how-to-set-virtual-machine-passwords-student.md).