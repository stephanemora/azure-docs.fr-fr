---
title: Dans cet article, vous allez apprendre à vous mettre à jour les offres et les plans Azure Stack | Microsoft Docs
description: Cet article décrit comment afficher et modifier les offres et les plans Azure Stack existants.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 07/30/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: f6b85d2197a4db9cdfc526e5857e4b6ce707beb2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076788"
---
# <a name="azure-stack-add-on-plans"></a>Plans de module complémentaire de Azure Stack

En tant qu’opérateur Azure Stack, vous créez des plans d’extension pour modifier un [*plan de base*](azure-stack-create-plan.md) lorsque vous souhaitez proposer des services supplémentaires ou étendre vos quotas en matière d’*ordinateur*, de *stockage* , ou de *réseau* au-delà de l’offre initiale des plans de base. Les plans additionnels modifient un plan de base et constituent des extensions facultatives auxquelles les utilisateurs peuvent s’abonner. 

La meilleure solution est parfois de tout regrouper dans un seul plan. Mais vous pouvez aussi parfois préférer disposer d’un plan de base, puis proposer des services supplémentaires à l’aide de plans d’extension. Par exemple, vous pouvez choisir d’offrir des services IaaS dans le cadre d’un plan de base, avec tous les services PaaS traités comme des plans d’extension.

Le fait que les plans d’extension contribuent à rendre les utilisateurs conscients de leur utilisation des ressources constitue une autre raison de les utiliser. Pour ce faire, vous pouvez commencer avec un plan de base qui inclut des quotas relativement petits (selon les services requis). Ensuite, alors les utilisateurs atteignent une pleine capacité, ils sont avertis de leur consommation des ressources allouées en fonction de leur plan attribué. Dès lors, les utilisateurs peuvent sélectionner un plan d’extension qui fournit les ressources supplémentaires.

> [!NOTE]
> Lorsque vous ne souhaitez pas utiliser un plan d’extension pour étendre un quota, vous pouvez également choisir de [modifier la configuration d’origine du quota](azure-stack-quota-types.md#to-edit-a-quota). 

Lorsqu’un utilisateur ajoute un plan d’extension à son abonnement existant à une offre, les ressources supplémentaires peuvent prendre une heure à s’afficher. 

## <a name="create-an-add-on-plan"></a>Créer un plan d’extension
Les plans d’extension sont créés en modifiant une offre existante :

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de cloud.
2. Suivez les étapes décrites pour [créer un nouveau plan de base](azure-stack-create-plan.md) afin de créer un plan offrant des services qui n’étaient pas proposés auparavant. Dans cet exemple, les services Key Vault (Microsoft.KeyVault) seront inclus dans le nouveau plan.
3. Dans le portail de l’administrateur, cliquez sur **Offres** , puis sélectionnez l’offre à mettre à jour avec un plan d’extension.

   ![](media/create-add-on-plan/1.PNG)

4.  Faites défiler les propriétés de l’offre jusqu’en bas et sélectionnez **Plans d’extension**. Cliquez sur **Add**.
   
    ![](media/create-add-on-plan/2.PNG)

5. Sélectionnez le plan à ajouter. Dans cet exemple, le plan s’intitule **Key vault plan**. Après avoir sélectionné le plan, cliquez sur **Sélectionnez** pour ajouter le plan à l’offre. Vous devriez recevoir une notification vous indiquant que le plan a été ajouté à l’offre.
   
    ![](media/create-add-on-plan/3.PNG)

6. Passez en revue la liste des plans d’extension inclus avec l’offre pour vérifier que le nouveau plan y figure.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Étapes suivantes
[Créer une offre](azure-stack-create-offer.md)