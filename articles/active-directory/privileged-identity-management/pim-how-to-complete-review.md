---
title: Effectuer une révision d’accès pour les rôles d’Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Découvrez comment effectuer une révision d’accès pour les rôles d’Azure AD dans Azure AD Privileged Identity Management (PIM) et afficher les résultats
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed53c8bb3cb735822fc40aab86ca40bef7b9607a
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619180"
---
# <a name="complete-an-access-review-for-azure-ad-roles-in-pim"></a>Effectuer une révision d’accès pour les rôles d’Azure AD dans PIM
Les administrateurs de rôle privilégié peuvent examiner l’accès privilégié dès le [démarrage d’une révision d’accès](pim-how-to-start-security-review.md). Azure Active Directory (Azure AD) Privileged Identity Management (PIM) envoie automatiquement un e-mail demandant aux utilisateurs de revoir leur accès. Si un utilisateur n’a pas reçu d’e-mail, vous pouvez lui envoyer les instructions contenues dans [Guide pratique pour effectuer une révision d’accès](pim-how-to-perform-security-review.md).

À la fin de la période de révision d’accès, ou dès que tous les utilisateurs ont terminé leur auto-examen, suivez les étapes décrites dans cet article pour gérer la révision et afficher les résultats.

## <a name="manage-access-reviews"></a>Gérer les révisions d’accès
1. Accédez à la [portail Azure](https://portal.azure.com/) et sélectionnez l’application **Azure AD Privileged Identity Management** sur votre tableau de bord.
2. Sélectionnez la section **Révisions de l’accès** du tableau de bord.
3. Sélectionnez la révision d’accès que vous souhaitez gérer.

Le panneau de détails de la révision d’accès comporte plusieurs options pour la gestion de cette révision.

![Boutons de révision d’accès PIM - capture d’écran](./media/pim-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>Rappeler
Si une vérification de l’accès est configurée de sorte que les utilisateurs doivent eux-mêmes la réviser, le bouton **Rappeler** envoie une notification. 

### <a name="stop"></a>Arrêter
Toutes les révisions d’accès ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour les terminer plus tôt. Si les utilisateurs n’ont pas été révisés à cette date, ils ne pourront plus le faire une fois la révision arrêtée. Vous ne pouvez pas redémarrer une révision arrêtée.

### <a name="apply"></a>Appliquer
Lorsqu’une vérification de l’accès est terminé, soit parce qu’elle a atteint la date de fin ou après un arrêt manuel, le bouton **Appliquer** implémente le résultat de la révision. Si l’accès d’un utilisateur a été refusé lors de la révision, cette étape supprimera l’affectation de son rôle.  

### <a name="export"></a>Exportation
Si vous souhaitez appliquer manuellement les résultats de la révision d’accès, vous pouvez exporter la révision. Le bouton **Exporter** fait démarrer le téléchargement d'un fichier CSV. Vous pouvez gérer les résultats dans Excel ou d'autres programmes qui s'ouvrent les fichiers CSV.

### <a name="delete"></a>Supprimer
Si vous n'êtes plus intéressé par la révision, supprimez-la. Le bouton **Supprimer** permet de supprimer la révision de l'application PIM.

> [!IMPORTANT]
> La suppression ayant lieu sans affichage préalable d’un avertissement, assurez-vous que vous voulez supprimer cette révision. 

## <a name="next-steps"></a>Étapes suivantes

- [Démarrer une révision d’accès pour les rôles Azure AD dans PIM](pim-how-to-start-security-review.md)
- [Effectuer une révision d’accès de mes rôles Azure AD dans PIM](pim-how-to-perform-security-review.md)
