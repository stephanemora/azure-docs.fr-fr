---
title: Créer un plan dans Azure Stack | Microsoft Docs
description: En tant qu’administrateur cloud, créez un plan permettant aux abonnés d’approvisionner des machines virtuelles.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Créer un plan dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les [plans](azure-stack-key-features.md) regroupent un ou plusieurs services. En tant que fournisseur, vous pouvez créer des plans à proposer à vos utilisateurs. En retour, ceux-ci s’abonnent à vos offres pour utiliser les plans et les services qu’elles comprennent. Cet exemple montre comment créer un plan comprenant les fournisseurs de ressources de stockage, réseau et de calcul. Ce plan donne aux abonnés la possibilité d’approvisionner des machines virtuelles.

1. Connectez-vous au portail d’administration d’Azure Stack (https://adminportal.local.azurestack.external)).

2. Pour créer un plan et une offre auxquels les utilisateurs peuvent s’abonner, sélectionnez **Nouveau** > **Offres + Plans** > **Plan**.  
   ![Sélectionner un plan](media/azure-stack-create-plan/select-plan.png)

3. Dans le volet **Nouveau plan**, renseignez le **Nom d’affichage** et le **Nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan, que les utilisateurs voient. Seul l’administrateur peut voir le nom de la ressource, qui est le nom utilisé par les administrateurs pour utiliser le plan comme une ressource Azure Resource Manager.  
   ![Spécifier les détails](media/azure-stack-create-plan/plan-name.png)

4. Créez un **Groupe de ressources** ou sélectionnez-en un qui servira de conteneur pour le plan.  
   ![Spécifier le groupe de ressources](media/azure-stack-create-plan/resource-group.png)

5. Sélectionnez **Services**, puis cochez la case pour **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**. Choisissez ensuite **Sélectionner** pour enregistrer la configuration. Les cases à cocher s’affichent lorsque la souris pointe sur chaque option.  
   ![Sélectionner des services](media/azure-stack-create-plan/services.png)

6. Sélectionnez **Quotas**, **Microsoft.Storage (local)**, puis choisissez le quota par défaut ou sélectionnez **Créer un quota** pour personnaliser le quota.  
   ![Quotas](media/azure-stack-create-plan/quotas.png)

7. Si vous créez un quota, entrez un **Nom** pour le quota > spécifiez ses valeurs > sélectionnez **OK**. Le volet **Créer un quota** se ferme.
   ![Nouveau quota](media/azure-stack-create-plan/new-quota.png)

   Vous sélectionnez ensuite le nouveau quota que vous avez créé. La sélection du quota permet de l’assigner et ferme le volet de sélection.  
   ![Assigner le quota](media/azure-stack-create-plan/assign-quota.png)

8. Répétez les étapes 6 et 7 pour créer et assigner des quotas pour **Microsoft.Network (local)** et **Microsoft.Compute (local)**.  Lorsque des quotas ont été assignés aux trois services, ils apparaissent comme dans l’image suivante.  
   ![Affectations de quota terminées](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Dans le volet **Quotas**, choisissez **OK** puis, dans le volet **Nouveau plan**, choisissez **Créer** pour créer le plan.  
    ![Créer le plan](media/azure-stack-create-plan/create.png)
10. Pour voir votre nouveau plan, sélectionnez **Toutes les ressources**, puis recherchez le plan et sélectionnez son nom. Si votre liste de ressources est longue, utilisez **Rechercher** pour retrouver votre plan par nom.  
   ![Vérifier le plan](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>Étapes suivantes
[Créer une offre](azure-stack-create-offer.md)
