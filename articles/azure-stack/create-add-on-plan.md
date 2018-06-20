---
title: Dans cet article, vous allez apprendre à vous mettre à jour les offres et les plans Azure Stack | Microsoft Docs
description: Cet article décrit comment afficher et modifier les offres et les plans Azure Stack existants.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236950"
---
# <a name="azure-stack-add-on-plans"></a>Plans de module complémentaire de Azure Stack
En tant qu’opérateur Azure Stack, vous créez des plans qui contiennent les services souhaités et les quotas applicables auxquels vos utilisateurs peuvent s’abonner. Ces [*plans de base*](azure-stack-create-plan.md) contiennent les principaux services offerts à vos utilisateurs et vous ne pouvez avoir qu’un plan de base par offre. Si vous devez modifier votre offre, vous pouvez utiliser des *plans d’extension* qui vous permettent de modifier le plan pour étendre les quotas d’ordinateurs, de stockage ou réseau proposés initialement avec le plan de base. 

Même s’il peut s’avérer optimal de tout regrouper dans un seul plan dans certains cas, il est judicieux d’avoir un plan de base et d’offrir des services complémentaires avec des plans d’extension. Par exemple, vous pouvez choisir d’offrir des services IaaS dans le cadre d’un plan de base, avec tous les services PaaS traités comme des plans d’extension. Vous pouvez également vous servir des plans pour contrôler la consommation des ressources dans votre environnement Azure Stack. Par exemple, si vous souhaitez que vos utilisateurs fassent attention à leur utilisation des ressources, vous pourriez proposer un plan de base relativement petit (selon les services requis) où dès lors que les utilisateurs atteignent le seuil défini, ils sont avertis qu’ils ont déjà consommé les ressources allouées de leur plan attribué. À partir de là, les utilisateurs peuvent sélectionner un plan d’extension disponible pour obtenir des ressources supplémentaires. 

> [!NOTE]
> Lorsqu’un utilisateur ajoute un plan d’extension à son abonnement existant à une offre, les ressources supplémentaires peuvent prendre une heure à s’afficher. 

## <a name="create-an-add-on-plan"></a>Créer un plan d’extension
Les plans d’extension sont créés en modifiant une offre existante :

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur de cloud.
2. Suivez les étapes décrites pour [créer un nouveau plan de base](azure-stack-create-plan.md) afin de créer un plan offrant des services qui n’étaient pas proposés auparavant. Dans cet exemple, les services Key Vault (Microsoft.KeyVault) seront inclus dans le nouveau plan.
3. Dans le portail de l’administrateur, cliquez sur **Offres** , puis sélectionnez l’offre à mettre à jour avec un plan d’extension.

   ![](media/create-add-on-plan/1.PNG)

4.  Faites défiler les propriétés de l’offre jusqu’en bas et sélectionnez **Plans d’extension**. Cliquez sur **Add**.
   
    ![](media/create-add-on-plan/2.PNG)

5. Sélectionnez le plan à ajouter. Dans cet exemple, le plan se nomme **Key vault plan**. Cliquez ensuite sur **Sélectionner** pour ajouter le plan à l’offre. Vous devriez recevoir une notification vous indiquant que le plan a été ajouté à l’offre.
   
    ![](media/create-add-on-plan/3.PNG)

6. Passez en revue la liste des plans d’extension inclus avec l’offre pour vérifier que le nouveau plan y figure.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Étapes suivantes
[Créer une offre](azure-stack-create-offer.md)