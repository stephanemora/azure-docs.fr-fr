---
title: Effectuer une révision d’accès pour les ressources Azure à l’aide de Privileged Identity Management | Microsoft Docs
description: Cet article explique comment effectuer une révision d’accès pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: ae64d9ebbca80f6c21b8c7f352022a0878518e65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149336"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Effectuer une révision d’accès pour les ressources Azure à l’aide de Privileged Identity Management
Les administrateurs de rôle privilégié peuvent examiner l’accès privilégié après le [démarrage d’une révision de sécurité](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) pour les ressources Azure envoie automatiquement un e-mail invitant les utilisateurs à vérifier leur accès. Si un utilisateur ne reçoit pas d’e-mail, vous pouvez lui envoyer les instructions relatives à l’[exécution d’une révision de sécurité](pim-resource-roles-perform-access-review.md).

Après la période de révision de sécurité, ou après que tous les utilisateurs ont terminé leur auto-examen, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## <a name="manage-security-reviews"></a>Gérer les révisions de sécurité
1. Accédez au [portail Azure](https://portal.azure.com/). Ensuite, dans le tableau de bord, sélectionnez l’application **Ressources Azure**.

2. Sélectionnez votre ressource.

3. Sélectionnez la section **Révisions de l’accès** du tableau de bord.
![Révisions d’accès](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Sélectionnez la révision d’accès que vous souhaitez gérer.

Le panneau de détails de la révision d’accès comporte plusieurs options pour la gestion de cette révision. Les options disponibles sont les suivantes :

![Options pour la gestion d’une révision](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Arrêter
Toutes les révisions d’accès ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour les terminer plus tôt. Tous les utilisateurs qui n’ont pas terminé leur révision à ce stade ne pourront pas la terminer si vous l’arrêtez. Vous ne pouvez pas redémarrer une révision une fois qu’elle a été arrêtée.

### <a name="reset"></a>Réinitialiser
Vous pouvez réinitialiser une révision d’accès afin de supprimer toutes les décisions qui lui sont associées. Une fois la révision d’accès réinitialisée, tous les utilisateurs sont à nouveau marqués comme non révisés. 

### <a name="apply"></a>Appliquer
Une fois une révision d’accès terminée, utilisez le bouton **Appliquer** pour implémenter le résultat de la révision. Si l’accès d’un utilisateur a été refusé lors de la révision, cette étape entraîne la suppression de son attribution de rôle.  

### <a name="delete"></a>Supprimer
Si la révision ne vous intéresse plus, supprimez-la. Le bouton **Supprimer** permet de supprimer la révision de l'application PIM.

## <a name="results"></a>Résultats
Dans l’onglet **Résultats**, affichez et téléchargez la liste des résultats de votre révision. 
![Onglet Résultats](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Réviseurs
Consultez et ajoutez des réviseurs à votre révision d’accès existant. Rappelez aux réviseurs d’effectuer leur révision.
![Ajouter des réviseurs](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



