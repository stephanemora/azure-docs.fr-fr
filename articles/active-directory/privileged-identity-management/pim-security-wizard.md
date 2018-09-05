---
title: Assistant Sécurité dans PIM - Azure | Microsoft Docs
description: Décrit l’Assistant Sécurité qui s’affiche quand vous utilisez Azure AD Privileged Identity Management (PIM) pour la première fois.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/27/2017
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 178a4c5e978075f2a59b22a1cccf462138527964
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189077"
---
# <a name="security-wizard-in-pim"></a>Assistant Sécurité dans PIM
Si vous êtes la première personne à exécuter Azure Privileged Identity Management (PIM) pour votre organisation, un Assistant s’affiche. Celui-ci vous aide à comprendre les risques liés à la sécurité des identités privilégiées et comment utiliser PIM pour les limiter. Vous n’avez pas besoin d’apporter des modifications aux affectations de rôle existantes dans l’Assistant. Vous pouvez le faire ultérieurement si vous préférez.

## <a name="what-to-expect"></a>À quoi s’attendre
Avant le démarrage de votre organisation à l’aide de PIM, toutes les attributions de rôles sont permanentes : les utilisateurs ont toujours ces rôles, même s’ils n’ont pas actuellement besoin de leurs privilèges.  La première étape de l’Assistant vous montre une liste des rôles à privilèges élevés et le nombre d’utilisateurs ayant actuellement ces rôles. Vous pouvez explorer en détail un rôle particulier pour en savoir plus sur les utilisateurs si certains vous sont inconnus.

La deuxième étape de l’Assistant vous donne la possibilité de modifier les attributions du rôle d’administrateur.  

> [!WARNING]
> Il est important de disposer d’au moins un administrateur général et plusieurs administrateurs de rôle privilégié avec un compte professionnel ou scolaire (et non un compte Microsoft). S’il n’existe qu’un seul administrateur de rôle privilégié, l’organisation ne pourra pas utiliser PIM si ce compte est supprimé.
> En outre, conservez des affectations de rôle permanentes si un utilisateur possède un compte Microsoft (un compte pour se connecter aux services Microsoft tels que Skype et Outlook.com). Si vous envisagez d’exiger une authentification multifacteur (MFA) pour l’activation de ce rôle, cet utilisateur est exclu de ce rôle.
> 
> 

Une fois que vous avez apporté les modifications, l’Assistant ne s’affiche plus. La prochaine fois que vous ou un autre administrateur de rôle privilégié utiliserez PIM, vous verrez le tableau de bord de PIM.  

* Si vous souhaitez ajouter des utilisateurs dans des rôles ou en supprimer, ou encore modifier des affectations de l’état permanent à l’état éligible, consultez la rubrique [Comment ajouter ou supprimer un rôle d’utilisateur](pim-how-to-add-role-to-user.md)pour en savoir plus.
* Si vous souhaitez autoriser plus d’utilisateurs à gérer PIM, consultez la rubrique expliquant [comment affecter un accès à PIM](pim-how-to-give-access-to-pim.md)pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

- [Commencer à utiliser PIM](pim-getting-started.md)
- [Attribuer des rôles d’annuaire Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Accorder l’accès à d’autres administrateurs pour la gestion de PIM](pim-how-to-give-access-to-pim.md)
