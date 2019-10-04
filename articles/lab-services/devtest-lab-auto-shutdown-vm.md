---
title: Configurer les paramètres d’arrêt automatique pour une machine virtuelle dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment configurer les paramètres d’arrêt automatique pour une machine virtuelle afin que la machine virtuelle soit arrêtée automatiquement lorsqu’elle n’est pas utilisée.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361273"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Configurer les paramètres d’arrêt automatique pour une machine virtuelle dans Azure DevTest Labs
Azure DevTest Labs vous permet de contrôler les coûts et de réduire le gaspillage dans vos laboratoires en gérant les stratégies (paramètres) de chacun d’entre eux. Cet article vous montre comment configurer la stratégie d’arrêt automatique pour un compte de laboratoire et configurer les paramètres d’arrêt automatique pour un laboratoire dans le compte de laboratoire. Pour savoir comment définir chaque stratégie de laboratoire, consultez [Définir des stratégies de laboratoire dans Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Stratégie d’arrêt automatique pour un labo
En tant que propriétaire d’un laboratoire, vous pouvez configurer une planification d’arrêt pour toutes les machines virtuelles de votre laboratoire. Ceci vous permet de réaliser des économies en stoppant l’exécution de machines inutilisées (inactives). Vous pouvez appliquer une stratégie d’arrêt centralisée à toutes les machines virtuelles de votre laboratoire et, également, épargner aux utilisateurs de votre laboratoire l’établissement d’une planification pour leurs machines individuelles. Cette fonctionnalité vous permet de définir la stratégie de planification de votre laboratoire, du contrôle inexistant au contrôle total, pour les utilisateurs de votre laboratoire. En tant que propriétaire de laboratoire, vous pouvez configurer cette stratégie en procédant comme suit :

1. Sur la page d’accueil de votre laboratoire, sélectionnez **Configuration et stratégies**.
2. Sélectionnez **Stratégie d'arrêt automatique** dans la section **Planifications** du menu de gauche.
3. Sélectionnez l'une des options. Les sections suivantes vous donnent plus de détails sur ces options : La stratégie définie s’applique seulement aux nouvelles machines virtuelles créées dans le labo et pas aux machines virtuelles déjà existantes. 

    ![Options de stratégie d’arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Configurer les paramètres d’arrêt automatique pour un laboratoire
La stratégie d’arrêt automatique vous permet d’indiquer l’heure à laquelle les machines virtuelles du laboratoire doivent s’arrêter et contribue ainsi à réduire les pertes de laboratoire.

Pour afficher (et modifier) les stratégies d’un laboratoire, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Tous les services**, puis **DevTest Labs** dans la liste.
3. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.   
4. Sélectionnez **Configuration et stratégies**.

    ![Volet Paramètres de stratégie](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Dans le volet **Configuration et stratégies** du labo, sélectionnez **Arrêt automatique** sous **Planifications**.
   
    ![Arrêt automatique](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Sélectionnez **Activer** ou **Désactiver** pour activer ou désactiver cette stratégie.
7. Si vous activez cette stratégie, spécifiez l’heure (et le fuseau horaire) de l’arrêt pour toutes les machines virtuelles du laboratoire actif.
8. Spécifiez **Oui** ou **Non** pour l’option d’envoi de notification 15 minutes avant l’heure d’arrêt automatique spécifiée. Si vous choisissez **Oui**, saisissez un point de terminaison de l’URL de Webhook ou une adresse e-mail spécifiant où vous désirez publier ou envoyer la notification. L’utilisateur reçoit une notification et peut retarder l’arrêt. Pour en savoir plus, consultez la section [Notifications](#notifications). 
9. Sélectionnez **Enregistrer**.

    Par défaut, une fois activée, cette stratégie s’applique à toutes les machines virtuelles dans le laboratoire en cours. Pour supprimer ce paramètre sur une machine virtuelle spécifique, ouvrez le volet de gestion de la machine virtuelle et modifiez son paramètre **Arrêt automatique**.

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Configurer les paramètres d’arrêt automatique pour une machine virtuelle

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utilisateur définit une planification qu'il peut refuser
Si cette option de stratégie est définie pour le laboratoire, les utilisateurs peuvent remplacer ou refuser la planification du laboratoire. Cette option accorde aux utilisateurs du laboratoire un contrôle total sur la planification d’arrêt automatique de leurs machines virtuelles. Les utilisateurs du laboratoire ne voient aucune modification de leur page de planification d’arrêt automatique des machine virtuelles.

![Option de stratégie d’arrêt automatique – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utilisateur définit une planification qu'il ne peut pas refuser
Si cette option de stratégie est définie pour le laboratoire, les utilisateurs peuvent remplacer la planification du laboratoire. Ils ne peuvent cependant pas renoncer à la stratégie d’arrêt automatique. Cette option permet de s’assurer que chaque machine de votre laboratoire fait l’objet d’une planification d’arrêt automatique. Les utilisateurs du laboratoire peuvent mettre à jour la planification d’arrêt automatique de leurs machines virtuelles et définir des notifications d’arrêt.

![Option de stratégie d’arrêt automatique – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utilisateur n'a aucun contrôle sur la planification définie par l'administrateur du laboratoire
Si cette option de stratégie est définie pour le laboratoire, les utilisateurs ne peuvent pas remplacer ou refuser la planification du laboratoire. Cette option offre à l’administrateur du laboratoire le contrôle total sur la planification de chaque machine du laboratoire. Les utilisateurs du laboratoire ne peuvent configurer de notifications d’arrêt automatique que pour leurs machines virtuelles.

![Option de stratégie d’arrêt automatique – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notifications
Une fois l’arrêt automatique configuré par le propriétaire du laboratoire, des notifications seront envoyées aux utilisateurs du laboratoire 15 minutes avant le déclenchement de l’arrêt automatique si l’une de leurs machines virtuelles est affectée. Cette option donne aux utilisateurs du laboratoire la possibilité d’enregistrer leur travail avant l’arrêt. La notification fournit également des liens de chaque machine virtuelle pour les actions suivantes :

- Pour l’instant, ignorez l’arrêt automatique
- Répétez l’arrêt automatique pendant une heure ou deux heures afin qu’ils puissent continuer à travailler sur la machine virtuelle.

La notification est envoyée par l’intermédiaire du point de terminaison du webhook configuré ou d’une adresse électronique spécifiée par les propriétaires du laboratoire dans les paramètres d’arrêt automatique. Les webhooks vous permettent de créer ou de configurer des intégrations qui s’abonnent à certains événements. Lorsque l’un de ces événements se déclenche, DevTest Labs envoie une charge utile POST HTTP à l’URL configurée du webhook. Pour plus d’informations sur les webhooks, consultez [Créer une fonction Azure d’API ou de webhook](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Nous vous recommandons d’utiliser des webhooks car ils sont largement pris en charge par différentes applications (par exemple, Slack, Azure Logic Apps, etc.) et vous permettent d’implémenter votre propre méthode pour envoyer des notifications. Pour obtenir un exemple de réception d’une notification d’arrêt automatique à partir d’e-mails à l’aide d’Azure Logic Apps, consultez [Créer une application logique qui reçoit des notifications par e-mail](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Étapes suivantes
Consultez [Gérer toutes les stratégies d’arrêt automatique d’un laboratoire dans Azure DevTest Labs](devtest-lab-auto-shutdown.md)
