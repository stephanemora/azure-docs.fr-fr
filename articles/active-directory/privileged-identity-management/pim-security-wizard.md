---
title: Assistant de sécurité des rôles Azure AD dans PIM - Azure Active Directory | Microsoft Docs
description: Décrit l’Assistant de sécurité que vous pouvez utiliser pour définir des attributions de rôles Azure AD privilégiés permanents comme éligibles à l’aide d’Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6f978612cbbf0c326c3e66f25a0fbf4b749cc73
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60286934"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Assistant de sécurité des rôles Azure AD dans PIM

Si vous êtes la première personne à exécuter Azure AD Privileged Identity Management (PIM) pour votre organisation, un Assistant s’affiche. Celui-ci vous aide à comprendre les risques liés à la sécurité des identités privilégiées et comment utiliser PIM pour les limiter. Vous n’avez pas besoin d’apporter des modifications aux affectations de rôle existantes dans l’Assistant. Vous pouvez le faire ultérieurement si vous préférez.

## <a name="wizard-overview"></a>Présentation de l’Assistant

Avant le démarrage de votre organisation à l’aide de PIM, toutes les attributions de rôles sont permanentes : les utilisateurs ont toujours ces rôles, même s’ils n’ont pas actuellement besoin de leurs privilèges. La première étape de l’Assistant vous montre une liste des rôles à privilèges élevés et le nombre d’utilisateurs ayant actuellement ces rôles. Vous pouvez explorer en détail un rôle particulier pour en savoir plus sur les utilisateurs si certains vous sont inconnus.

La deuxième étape de l’Assistant vous donne la possibilité de modifier les attributions du rôle d’administrateur.  

> [!WARNING]
> Il est important de disposer d’au moins un Administrateur général et plusieurs Administrateurs de rôle privilégié avec un compte professionnel ou scolaire (et non un compte Microsoft). S’il n’existe qu’un seul Administrateur de rôle privilégié, l’organisation ne pourra pas utiliser PIM si ce compte est supprimé.
> En outre, conservez des affectations de rôle permanentes si un utilisateur possède un compte Microsoft (un compte pour se connecter aux services Microsoft tels que Skype et Outlook.com). Si vous envisagez d’exiger une authentification multifacteur (MFA) pour l’activation de ce rôle, cet utilisateur est exclu de ce rôle.

## <a name="run-the-wizard"></a>Exécuter l’Assistant

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD** puis cliquez sur **Assistant**.

    ![Rôles Azure AD - Assistant](./media/pim-security-wizard/wizard-start.png)

1. Cliquez sur **1 Découvrir les rôles privilégiés**.

1. Passez en revue la liste des rôles privilégiés pour voir quels utilisateurs sont permanents ou éligibles.

    ![Découvrir les utilisateurs avec des rôles privilégiés](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Cliquez sur **Suivant** pour sélectionner les membres que vous voulez rendre éligibles.

    ![Définir des membres comme éligibles](./media/pim-security-wizard/convert-members-eligible.png)

1. Une fois que vous avez sélectionné les membres, cliquez sur **Suivant**.

    ![Examiner les modifications](./media/pim-security-wizard/review-changes.png)

1. Cliquez sur **OK** pour définir les attributions permanentes comme éligibles.

    Une fois la conversion terminée, vous verrez une notification.

    ![Notifications](./media/pim-security-wizard/notification-completion.png)

Si vous avez besoin de définir d’autres attributions de rôles privilégiés comme éligibles, vous pouvez réexécuter l’Assistant. Si vous souhaitez utiliser l’interface PIM au lieu de l’Assistant, consultez [Attribuer des rôles Azure AD dans PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Accorder l’accès à d’autres administrateurs pour la gestion de PIM](pim-how-to-give-access-to-pim.md)
