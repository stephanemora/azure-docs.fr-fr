---
title: Assistant de sécurité des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Décrit l’Assistant de sécurité que vous pouvez utiliser pour définir des attributions de rôles Azure AD privilégiés permanents comme éligibles à l’aide d’Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231805"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Assistant de sécurité des rôles Azure AD dans Privileged Identity Management

Si vous êtes la première personne à utiliser Privileged Identity Management (PIM) dans votre organisation Azure Active Directory (Azure AD), un Assistant vous est proposé pour la prise en main. Celui-ci vous aide à comprendre les risques liés à la sécurité des identités privilégiées et la façon d’utiliser Privileged Identity Management pour les limiter. Vous n’avez pas besoin d’apporter des modifications aux affectations de rôle existantes dans l’Assistant. Vous pouvez le faire ultérieurement si vous préférez.

## <a name="wizard-overview"></a>Présentation de l’Assistant

Avant que votre organisation ne commence à utiliser Privileged Identity Management, toutes les attributions de rôles sont permanentes : les utilisateurs ont toujours ces rôles, même s’ils n’ont pas actuellement besoin de leurs privilèges. La première étape de l’Assistant vous montre une liste des rôles à privilèges élevés et le nombre d’utilisateurs ayant actuellement ces rôles. Vous pouvez explorer en détail un rôle particulier pour en savoir plus sur les utilisateurs si certains vous sont inconnus.

La deuxième étape de l’Assistant vous donne la possibilité de modifier les attributions du rôle d’administrateur.  

> [!WARNING]
> Il est important de disposer d’au moins un Administrateur général et de plusieurs Administrateurs de rôle privilégié avec un compte d’organisation (et non un compte Microsoft). S’il n’existe qu’un seul Administrateur de rôle privilégié, l’organisation ne peut pas gérer Privileged Identity Management si ce compte est supprimé.
> En outre, conservez les attributions de rôle permanentes si un utilisateur possède un compte Microsoft (en d’autres termes, un compte pour se connecter aux services Microsoft tels que Skype et Outlook.com). Si vous envisagez de demander une authentification multifacteur pour l’activation de ce rôle, cet utilisateur sera verrouillé.

## <a name="run-the-wizard"></a>Exécuter l'assistant

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Sélectionnez **Rôles Azure AD**, puis **Assistant**.

    ![Rôles Azure AD - Page de l’Assistant montrant les 3 étapes pour exécuter l’Assistant](./media/pim-security-wizard/wizard-start.png)

1. Sélectionnez **1 Découvrir les rôles privilégiés**.

1. Passez en revue la liste des rôles privilégiés pour voir quels utilisateurs sont permanents ou éligibles.

    ![Découvrir les rôles privilégiés : volet des rôles affichant les membres éligibles et permanents](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Sélectionnez **Suivant** pour sélectionner les utilisateurs ou groupes que vous voulez rendre aptes.

    ![Convertir les membres dans la page éligible avec les options permettant de sélectionner les membres que vous souhaitez rendre éligibles pour les rôles](./media/pim-security-wizard/convert-members-eligible.png)

1. Une fois que vous avez sélectionné les utilisateurs ou les groupes, sélectionnez **Suivant**.

    ![Passez en revue la page de modifications montrant les membres avec des affectations de rôle permanentes qui seront converties](./media/pim-security-wizard/review-changes.png)

1. Sélectionnez **OK** pour définir les attributions permanentes comme aptes.

    Une fois la conversion terminée, vous verrez une notification.

    ![Notification indiquant l’état d’une conversion](./media/pim-security-wizard/notification-completion.png)

Si vous avez besoin de définir d’autres attributions de rôles privilégiés comme éligibles, vous pouvez réexécuter l’Assistant. Si vous souhaitez utiliser l’interface Privileged Identity Management au lieu de l’Assistant, consultez [Attribuer des rôles Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles dans Azure AD dans Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Accorder l’accès à d’autres administrateurs pour la gestion de Privileged Identity Management](pim-how-to-give-access-to-pim.md)
