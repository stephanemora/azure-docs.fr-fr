---
title: Guide pratique pour exécuter une révision d’accès dans Privileged Identity Management pour les ressources Azure | Microsoft Docs
description: Ce document explique comment effectuer une révision et y accéder dans PIM pour les ressources Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Privileged Identity Management - Rôle de ressource - Exécuter une révision d’accès
Privileged Identity Management pour les ressources Azure simplifie le mode de gestion par les entreprises de l’accès privilégié aux ressources dans Azure. 

Si vous êtes affecté à un rôle d’administrateur, l’administrateur de rôle privilégié de votre organisation peut vous demander de confirmer régulièrement que vous avez toujours besoin de ce rôle pour votre travail. Vous pouvez recevoir un e-mail contenant un lien ou accéder directement au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans cet article pour effectuer un auto-examen de vos rôles attribués.

Si vous êtes un administrateur de rôle privilégié intéressé par les révisions d’accès, consultez [Comment démarrer une révision de sécurité](pim-resource-roles-start-access-review.md)pour plus de détails.

## <a name="add-the-privileged-identity-management-application"></a>Ajout de l’application Privileged Identity Management
Vous pouvez utiliser l'application Azure AD Privileged Identity Management (PIM) dans le [portail Azure](https://portal.azure.com/) pour effectuer votre révision.  Si vous n'avez pas l'application Azure AD Privileged Identity Management sur votre portail, procédez comme suit pour commencer.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure, puis choisissez le répertoire que vous allez utiliser.
3. Sélectionnez **Tous les services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

## <a name="approve-or-deny-access"></a>Approuver ou refuser l'accès
Lorsque vous acceptez ou refusez l’accès, vous indiquez simplement au réviseur si vous utilisez toujours ce rôle ou non. Choisissez **Approuver** si vous souhaitez conserver le rôle, ou **Refuser** si vous n’avez plus besoin de l’accès. Votre état ne change pas tout de suite car le réviseur doit d’abord appliquer les résultats.
Procédez comme suit pour rechercher et terminer la révision de l’accès :
1. Accédez à l’application Azure AD Privileged Identity Management.
2. Sélectionnez le panneau Revoir l’accès.

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Sélectionnez la révision que vous aimeriez effectuer. 
4. Choisissez **Approuver** ou **Refuser**. Vous devrez peut-être motiver votre choix dans la zone de texte **Indiquez une raison** .

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
