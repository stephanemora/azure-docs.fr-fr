---
title: Configurer les paramètres de démarrage automatique pour une machine virtuelle dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment configurer les paramètres de démarrage automatique pour les machines virtuelles dans un laboratoire. Ce paramètre permet aux machines virtuelles du laboratoire de démarrer automatiquement selon une planification.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807711"
---
# <a name="auto-startup-lab-virtual-machines"></a>Démarrer automatiquement des machines virtuelles de laboratoire  
Azure DevTest Labs vous permet de configurer les machines virtuelles de votre laboratoire pour qu’elles soient automatiquement démarrées et arrêtées en fonction d’une planification. Pour plus d’informations sur la configuration des paramètres d’arrêt automatique, consultez [Gérer toutes les stratégies d’arrêt automatique d’un laboratoire dans Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

Contrairement à l’arrêt automatique, où toutes les machines virtuelles sont incluses quand la stratégie est activée, la stratégie de démarrage automatique exige qu’un utilisateur de laboratoire sélectionne explicitement une machine virtuelle et s’abonne à cette planification. Ainsi, vous risquez difficilement de vous retrouver dans le cas où des machines virtuelles sont démarrées automatiquement par inadvertance et entraînent des dépenses imprévues.

Cet article explique comment configurer la stratégie de démarrage automatique pour un laboratoire.

## <a name="configure-autostart-settings-for-a-lab"></a>Configurer les paramètres de démarrage automatique pour un laboratoire 
1. Accédez à la page d’accueil de votre fabrique de données. 
2. Sélectionnez **Configuration et stratégies** dans le menu de gauche. 

    ![Menu Configuration et stratégies](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Dans la page **Configuration et stratégies**, effectuez les étapes suivantes :
    
    1. Sélectionnez **Activé** pour **Autoriser la planification du démarrage automatique des machines virtuelles** afin d’activer la fonctionnalité de démarrage automatique pour ce laboratoire. 
    2. Sélectionnez une heure de début (par exemple 8:00:00 AM) dans le champ **Début de la planification**. 
    3. Sélectionnez un **fuseau horaire** à utiliser. 
    4. Sélectionnez les **jours de la semaine** lors desquels les machines virtuelles doivent être démarrées automatiquement. 
    5. Ensuite, sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres. 

        ![Paramètres de démarrage automatique](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Cette stratégie n’applique pas automatiquement le démarrage automatique aux machines virtuelles du laboratoire. Pour **abonner** des machines virtuelles spécifiques, accédez à la page de la machine virtuelle et activez le **démarrage automatique** pour cette machine virtuelle.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Activer le démarrage automatique pour une machine virtuelle du laboratoire
La procédure suivante indique comment abonner une machine virtuelle à la stratégie de démarrage automatique du laboratoire. 

1. Dans la page d’accueil de votre laboratoire, sélectionnez la **machine virtuelle** dans la liste **Mes machines virtuelles**. 

    ![Menu Configuration et stratégies](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Dans la page **Machine virtuelle**, sélectionnez **Démarrage automatique** dans le menu de gauche ou dans la liste **Planifications**. 

    ![Sélectionnez le menu Démarrage automatique](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Dans la page **Démarrage automatique**, sélectionnez **Activé** pour l’option **Autoriser la planification de cette machine virtuelle pour le démarrage automatique**.

    ![Activer le démarrage automatique pour la machine virtuelle](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Ensuite, sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer le paramètre. 


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration de la stratégie d’arrêt automatique pour un laboratoire, consultez [Gérer toutes les stratégies d’arrêt automatique d’un laboratoire dans Azure DevTest Labs](devtest-lab-auto-shutdown.md).
