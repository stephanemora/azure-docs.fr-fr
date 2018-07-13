---
title: Exécution d’une révision d’accès | Microsoft Docs
description: Découvrez comment effectuer une révision avec l'application Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b70018b85147d11d8da0201ef82aedb1c752cf5c
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949915"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Comment effectuer une révision de l’accès dans Azure AD Privileged Identity Management ?
Le service Azure Active Directory (AD) Privileged Identity Management simplifie la gestion par les entreprises de l’accès privilégié aux ressources dans Azure AD et d’autres services en ligne Microsoft tels qu’Office 365 ou que Microsoft Intune.  

Si vous êtes affecté à un rôle d’administrateur, l’administrateur de rôle privilégié de votre organisation peut vous demander de confirmer régulièrement que vous avez toujours besoin de ce rôle pour votre travail. Vous pouvez recevoir un e-mail contenant un lien ou accéder directement au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans cet article pour effectuer un auto-examen de vos rôles attribués.

Si vous êtes un administrateur de rôle privilégié ou administrateur global intéressé par les révisions d’accès, consultez [Comment démarrer une révision d’accès](active-directory-privileged-identity-management-how-to-start-security-review.md)pour plus de détails.

## <a name="add-the-privileged-identity-management-application"></a>Ajout de l’application Privileged Identity Management
Vous pouvez utiliser l'application Azure AD Privileged Identity Management (PIM) dans le [portail Azure](https://portal.azure.com/) pour effectuer votre révision.  Si vous n'avez pas l'application Azure AD Privileged Identity Management sur votre portail, procédez comme suit pour commencer.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure, puis choisissez le répertoire que vous allez utiliser.
3. Sélectionnez **Tous les services** et utilisez la zone de texte Filtre pour rechercher **Azure AD Privileged Identity Management**.
4. Cochez **Épingler au tableau de bord**, puis cliquez sur **Créer**. L’application Privileged Identity Management s’ouvre.

## <a name="approve-or-deny-access"></a>Approuver ou refuser l'accès
Lorsque vous acceptez ou refusez l’accès, vous indiquez simplement au réviseur si vous utilisez toujours ce rôle ou non. Choisissez **Approuver** si vous souhaitez conserver le rôle, ou **Refuser** si vous n’avez plus besoin de l’accès. Votre état ne change pas tout de suite car le réviseur doit d’abord appliquer les résultats.
Procédez comme suit pour rechercher et terminer la révision de l’accès :

1. Dans l’application PIM, sélectionnez **Réviser un accès privilégié**. Si vous avez des révisions d’accès en attente, ces révisions apparaissent dans le panneau des révisions d’accès Azure AD.
2. Sélectionnez la révision à terminer.
3. Sauf si vous avez créé la révision, vous serez l’unique utilisateur de cette révision. Cochez la case en regard de votre nom.
4. Choisissez **Approuver** ou **Refuser**. Vous devrez peut-être motiver votre choix dans la zone de texte **Indiquez une raison** .  
5. Fermez le panneau **Réviser les rôles Azure AD** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
