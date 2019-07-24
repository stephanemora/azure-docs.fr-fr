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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144106"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Définir ou réinitialiser un mot de passe d’accès à des machines virtuelles dans un labo de salle de classe
Cet article propose différentes méthodes permettant de définir et de réinitialiser des mots de passe d’accès aux machines virtuelles dans des labos de salle de classe. 

## <a name="lab-owners-teachers"></a>Propriétaires de labos (professeurs)
Un propriétaire de labo (professeur) peut définir/réinitialiser le mot de passe d’accès à des machines virtuelles au moment de la création du labo (avec l’Assistant de création de labo) ou après l’avoir créé (avec le tableau de bord). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Définir le mot de passe au moment de la création du labo
Un propriétaire de labo (professeur) peut définir un mot de passe d’accès à des machines virtuelles via la page **Définition des informations d’identification** de l’Assistant de création de labo.

![Définition des informations d’identification](../media/tutorial-setup-classroom-lab/set-credentials.png)

En activant ou désactivant l’option **Use same password for all virtual machines** (Utiliser le même mot de passe pour toutes les machines virtuelles) figurant sur cette page, un professeur peut choisir d’utiliser le même mot de passe pour toutes les machines virtuelles du labo, ou autoriser les étudiants à définir leur propre mot de passe pour l’accès aux machines virtuelles. Par défaut, ce paramètre est activé pour toutes les images de systèmes d’exploitation Windows et Linux, à l’exception d’Ubuntu. Lorsque ce paramètre est désactivé, les étudiants sont invités à définir un mot de passe lorsqu’ils tentent de se connecter à la machine virtuelle pour la première fois. 

Le propriétaire de labo peut réinitialiser ce mot de passe (si nécessaire) sur la page **Configurer le modèle** de l’Assistant de création de labo. 

![Page Configurer le modèle une fois terminé](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Le propriétaire du labo peut également réinitialiser le mot de passe une fois l’atelier créé, via le tableau de bord. 

### <a name="reset-password-on-the-dashboard"></a>Réinitialiser un mot de passe via le tableau de bord

1. Sélectionnez le menu de dépassement de capacité (représenté par trois points verticaux) sur la mosaïque du labo, puis choisissez **Réinitialiser le mot de passe**. 

    ![Menu Réinitialiser le mot de passe figurant sur la page d’accueil](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Dans la boîte de dialogue **Set password** (Définir un mot de passe), saisissez un mot de passe, puis cliquez sur **Set password**  (Définir un mot de passe).
    
    ![Boîte de dialogue Set Password (Définir un mot de passe)](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Utilisateurs du labo (étudiants)
Au moment de la création du labo, son propriétaire peut activer ou désactiver l’option **Use same password for all virtual machines** (Utiliser le même mot de passe pour toutes les machines virtuelles). Si cette option est activée, les étudiants ne peuvent pas réinitialiser le mot de passe. Toutes les machines virtuelles des labos ont le même mot de passe, défini par le professeur. 

Si cette option est désactivée, les utilisateurs doivent définir un mot de passe lorsqu’ils tentent de se connecter à la machine virtuelle pour la première fois. Lorsque les utilisateurs (étudiants) sélectionnent le bouton **Connexion** sur la mosaïque du labo de la page **Mes machines virtuelles**, l’utilisateur voit apparaître la boîte de dialogue suivante, qui permet de définir le mot de passe d’accès à la machine virtuelle : 

![Réinitialisation du mot de passe par l’étudiant](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

L’étudiant peut également définir le mot de passe en cliquant sur le menu de dépassement de capacité (représenté par **trois points verticaux**) sur la mosaïque du labo, et en sélectionnant **Réinitialiser le mot de passe**. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les autres options d’utilisation que vous pouvez configurer pour les étudiants (en tant que propriétaire du labo), consultez l’article suivant : [Configure student usage](how-to-configure-student-usage.md) (Configurer l’utilisation pour les étudiants).
