---
title: Commencer à utiliser PIM - Azure | Microsoft Docs
description: Découvrez comment activer et bien démarrer avec Azure AD Privileged Identity Management (PIM) dans le portail Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 95582fb62f19dcc34aa03a6550f4dbc46257cf7f
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494355"
---
# <a name="start-using-pim"></a>Commencer à utiliser PIM

Avec Azure Active Directory (Azure AD) Privileged Identity Management (PIM), vous pouvez gérer, contrôler et superviser les accès au sein de votre organisation. Cette étendue inclut l’accès aux ressources Azure, à Azure AD et à d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

Cet article décrit comment activer et bien démarrer avec PIM.

## <a name="prerequisites"></a>Prérequis

Pour utiliser PIM, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Pour plus d’informations, consultez [Exigences relatives aux abonnements pour l’utilisation de PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Première personne à utiliser PIM

Si vous êtes la première personne à utiliser PIM dans votre annuaire, les rôles [Administrateur de sécurité](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) et [Administrateur de rôle privilégié](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) vous sont automatiquement attribués pour cet annuaire. Seuls les administrateurs de rôle privilégié peuvent gérer les attributions de rôles d’annuaire Azure AD d’utilisateurs. En outre, vous pouvez choisir d’exécuter l’[Assistant Sécurité](pim-security-wizard.md) qui vous guide dans l’expérience de découverte et d’attribution initiale.

## <a name="enable-pim"></a>Activer PIM

Pour commencer à utiliser PIM dans votre annuaire, vous devez d’abord activer PIM.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre annuaire.

    Vous devez être un administrateur général avec un compte d’organisation (par exemple @yourdomain.com) et non pas un compte Microsoft (par exemple @outlook.com), pour activer PIM sur un annuaire.

1. Cliquez sur **Tous les services**, puis recherchez le service **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management dans Tous les services](./media/pim-getting-started/pim-all-services-find.png)

1. Cliquez pour ouvrir le guide de démarrage rapide de PIM.

1. Dans la liste, cliquez sur **Consentir à PIM**.

    ![Consentir à PIM](./media/pim-getting-started/consent-pim.png)

1. Cliquez sur **Vérifier mon identité** pour vérifier votre identité avec Azure MFA. Vous devez choisir un compte.

    ![Choisir un compte](./media/pim-getting-started/pick-account.png)

1. Si plus d’informations sont nécessaires pour la vérification, vous êtes guidé dans le processus. Pour plus d’informations, consultez [Obtenir de l’aide pour la vérification en deux étapes](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Plus d’informations sont nécessaires](./media/pim-getting-started/more-information-required.png)

    Par exemple, une vérification par téléphone peut vous être demandée.

    ![Vérification de sécurité supplémentaire](./media/pim-getting-started/additional-security-verification.png)

1. Une fois que vous avez terminé le processus de vérification, cliquez sur le bouton **Consentir**.

1. Dans le message qui s’affiche, cliquez sur **Oui** pour consentir au service PIM.

    ![Message de consentement à PIM](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscrire PIM pour les rôles Azure AD

Une fois que vous avez activé PIM pour votre annuaire, vous devez inscrire PIM pour gérer les rôles Azure AD.

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

    ![Inscrire PIM pour les rôles Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Cliquez sur **S’inscrire**.

1. Dans le message qui s’affiche, cliquez sur **Oui** afin d’inscrire PIM pour gérer les rôles Azure AD.

    ![Message Inscrire PIM pour les rôles Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Une fois l’inscription effectuée, les options d’Azure AD sont activées. Il peut être nécessaire d’actualiser le portail.

    Pour plus d’informations sur la façon de découvrir et de sélectionner les ressources Azure à protéger avec PIM, consultez [Découvrir les ressources Azure à gérer dans PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Accédez à vos tâches

Une fois PIM configuré, vous pouvez effectuer vos tâches de gestion des identités.

![Tâches de niveau supérieur pour PIM - capture d’écran](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tâche + Gérer | Description |
| --- | --- |
| **Mes rôles**  | Affiche la liste des rôles éligibles et actifs qui vous sont attribués. C’est là où vous pouvez activer tout rôle éligible attribué. |
| **Mes demandes** | Affiche toutes vos requêtes en attente pour activer les attributions de rôles éligibles. |
| **Accès aux applications** | Permet de réduire les délais potentiels et d’utiliser un rôle immédiatement après son activation. |
| **Approuver des requêtes** | Affiche la liste des demandes d’activation des rôles éligibles par utilisateur dans votre annuaire, dont l’approbation vous revient. |
| **Révision d’accès** | Répertorie les révisions d’accès que vous devez terminer, que vous les effectuiez pour vous-même ou pour quelqu’un d’autre. |
| **Rôles Azure AD** | Affiche un tableau de bord et des paramètres pour les administrateurs de rôle privilégié qui doivent gérer les attributions de rôles d’annuaire Azure AD. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire). |
| **Ressources Azure** | Affiche un tableau de bord et des paramètres pour les administrateurs de rôle privilégié qui doivent gérer les attributions de rôles de ressources Azure. Ce tableau de bord est désactivée pour toute personnes n’étant pas un administrateur de rôle privilégié. Ces utilisateurs ont accès à un tableau de bord spécial intitulé Mon affichage. Le tableau de bord Mon affichage présente uniquement des informations sur l’utilisateur qui accède au tableau de bord (et non sur l’ensemble du locataire). |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Ajouter une vignette PIM au tableau de bord

Pour faciliter l’ouverture de PIM, vous pouvez ajouter une vignette PIM à votre tableau de bord, dans le portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Cliquez sur **Tous les services**, puis recherchez le service **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management dans Tous les services](./media/pim-getting-started/pim-all-services-find.png)

1. Cliquez pour ouvrir le guide de démarrage rapide de PIM.

1. Cochez la case **Épingler le panneau au tableau de bord** pour épingler le panneau du démarrage rapide de PIM au tableau de bord.

    ![Épingler le panneau au tableau de bord](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Dans le tableau de bord Azure, vous verrez une vignette comme celle-ci :

    ![Vignette du démarrage rapide de PIM](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles d’annuaire Azure AD dans PIM](pim-how-to-add-role-to-user.md)
- [Découvrir les ressources Azure de gestion dans PIM](pim-resource-roles-discover-resources.md)
