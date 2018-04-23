---
title: Privileged Identity Management pour les ressources Azure - Effectuer une révision d’accès pour les ressources Azure | Microsoft Docs
description: Décrit comment effectuer une révision d’accès pour les ressources Azure.
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
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - Rôle de ressource - Effectuer une révision d’accès
Les administrateurs de rôle privilégié peuvent examiner un accès privilégié une fois qu’une [révision de la sécurité a été démarrée](pim-resource-roles-start-access-review.md). Azure AD Privileged Identity Management (PIM) pour les ressources Azure envoie automatiquement un e-mail invitant les utilisateurs à vérifier leur accès. Si un utilisateur n'a pas reçu d’e-mail, vous pouvez lui envoyer les instructions dans [comment effectuer une révision de sécurité](pim-resource-roles-perform-access-review.md).

Après la période de révision de sécurité, ou après que tous les utilisateurs ont terminé leur auto-examen, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## <a name="manage-security-reviews"></a>Gérer les révisions de sécurité
1. Accédez au [portail Azure](https://portal.azure.com/) et sélectionnez l’application **Ressources Azure** dans le tableau de bord.
2. Sélectionnez votre ressource.
3. Sélectionnez la section **Révisions de l’accès** du tableau de bord.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Sélectionnez la révision d’accès que vous souhaitez gérer.

Le panneau de détails de la révision d’accès comporte plusieurs options de gestion de cette révision.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Arrêter
Toutes les révisions d’accès ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour les terminer plus tôt. Si les utilisateurs n’ont pas été révisés à cette date, ils ne pourront plus le faire une fois la révision arrêtée. Vous ne pouvez pas redémarrer une révision arrêtée.

### <a name="reset"></a>Réinitialiser
Vous pouvez réinitialiser une révision d’accès afin de supprimer toutes les décisions qui lui sont associées. Une fois la réinitialisation de la révision d’accès effectuée, tous les utilisateurs sont à nouveau marqués comme non vérifiés. 

### <a name="apply"></a>Appliquer
Lorsqu’une vérification de l’accès est terminé, soit parce qu’elle a atteint la date de fin ou après un arrêt manuel, le bouton **Appliquer** implémente le résultat de la révision. Si l’accès d’un utilisateur a été refusé lors de la révision, cette étape supprimera l’affectation de son rôle.  

### <a name="delete"></a>Supprimer
Si vous n'êtes plus intéressé par la révision, supprimez-la. Le bouton **Supprimer** permet de supprimer la révision de l'application PIM.

## <a name="results"></a>Résultats
Consultez et téléchargez la liste des résultats de votre révision sous l’onglet Résultats. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Réviseurs
Consultez et ajoutez des réviseurs à votre révision d’accès existant. Rappelez aux réviseurs d’effectuer leur révision.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



