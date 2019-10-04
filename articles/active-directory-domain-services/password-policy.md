---
title: Créer et utiliser des stratégies de mot de passe dans Azure AD Domain Services | Microsoft Docs
description: Découvrez comment et pourquoi utiliser des stratégies de mot de passe affinées pour sécuriser et contrôler les mots de passe de comptes dans un domaine managé Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617210"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Stratégies de mot de passe et de verrouillage de compte sur les domaines managés

Pour gérer la sécurité des comptes dans Azure AD DS (Azure Active Directory Domain Services), vous pouvez définir des stratégies de mot de passe affinées qui contrôlent des paramètres comme la longueur minimale des mots de passe, leur délai d’expiration ou leur complexité. Une stratégie de mot de passe par défaut est appliquée à tous les utilisateurs d’un domaine managé Azure AD DS. Pour assurer un contrôle précis et répondre à des besoins métier ou de conformité spécifiques, il est possible de créer et d’appliquer des stratégies supplémentaires à des groupes d’utilisateurs spécifiques.

Cet article montre comment créer et configurer une stratégie de mot de passe affinée à partir du Centre d’administration Active Directory.

## <a name="before-you-begin"></a>Avant de commencer

Pour faire ce qui est décrit dans cet article, vous avez besoin des ressources et des privilèges suivants :

* Un abonnement Azure actif.
  * Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un locataire Azure Active Directory associé à votre abonnement, synchronisé avec un annuaire local ou un annuaire cloud uniquement.
  * Si nécessaire, [créez un locataire Azure Active Directory][create-azure-ad-tenant] ou [associez un abonnement Azure à votre compte][associate-azure-ad-tenant].
* Un domaine managé Azure Active Directory Domain Services activé et configuré dans votre locataire Azure AD.
  * Si nécessaire, suivez le tutoriel [Créer et configurer une instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Une machine virtuelle de gestion Windows Server jointe au domaine managé Azure AD DS.
  * Si nécessaire, suivez le tutoriel [Créer une machine virtuelle de gestion][tutorial-create-management-vm].
* Un compte d’utilisateur membre du groupe *Administrateurs Azure AD DC* dans votre locataire Azure AD.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Vue d’ensemble des stratégies de mot de passe affinées (SMPA)

Les stratégies de mot de passe affinées (SMPA) vous permettent d’appliquer des restrictions spécifiques pour les stratégies de verrouillage de compte et de mot de passe à différents utilisateurs d’un domaine. Par exemple, pour sécuriser les comptes privilégiés, vous pouvez appliquer des paramètres de mot de passe plus stricts que les comptes standard sans privilèges. Vous pouvez créer plusieurs SMPA pour spécifier des stratégies de mot de passe dans un domaine managé Azure AD DS.

Les paramètres de mot de passe suivants peuvent être configurés à l’aide de SMPA :

* Longueur minimale du mot de passe
* Historique du mot de passe
* Les mots de passe doivent répondre aux exigences de complexité :
* Âge minimum du mot de passe
* Âge maximum du mot de passe
* Stratégie de verrouillage de compte
  * Durée de verrouillage de compte
  * Nombre d’échecs d’ouverture de session autorisés
  * Réinitialisation le nombre d’échecs d’ouverture de session après

Les stratégies SMPA affectent uniquement les utilisateurs créés dans Azure AD DS. Les utilisateurs du cloud et les utilisateurs de domaine synchronisés dans le domaine managé Azure AD DS à partir d’Azure AD ne sont pas affectés par les stratégies de mot de passe.

Les stratégies sont distribuées par le biais de l’association de groupes dans le domaine managé Azure AD DS, et les modifications que vous apportez sont appliquées à la connexion utilisateur suivante. La modification de la stratégie ne déverrouille pas un compte d’utilisateur qui est déjà verrouillé.

## <a name="default-fine-grained-password-policy-settings"></a>Paramètres par défaut des stratégies de mot de passe affinées

Dans un domaine managé Azure AD DS, les stratégies de mot de passe suivantes sont configurées par défaut et appliquées à tous les utilisateurs :

* **Longueur minimale du mot de passe (caractères) :** 7
* **Âge maximal du mot de passe (durée de vie) :** 90 jours
* **Les mots de passe doivent respecter les conventions de complexité**

Les stratégies de verrouillage de compte suivantes sont alors configurées par défaut :

* **Durée de verrouillage de compte :** 30
* **Nombre d’échecs d’ouverture de session autorisés :** 5.
* **Réinitialisation du nombre d’échecs d’ouverture de session après :** 30 minutes

Avec ces paramètres par défaut, les comptes d’utilisateurs sont verrouillés pendant 30 minutes si cinq mots de passe non valides sont utilisés en l’espace de 2 minutes. Les comptes sont déverrouillés automatiquement après 30 minutes.

Vous ne pouvez ni modifier ni supprimer la stratégie de mot de passe affinée prédéfinie par défaut. En revanche, les membres du groupe *Administrateurs AAD DC* peuvent créer une stratégie SMPA personnalisée et la configurer pour qu’elle remplace (prioritairement) la stratégie SMPA prédéfinie par défaut, comme indiqué dans la section suivante.

## <a name="create-a-custom-fine-grained-password-policy"></a>Créer une stratégie de mot de passe affinée personnalisée

À mesure que vous créerez des applications dans Azure, vous souhaiterez peut-être configurer une stratégie SMPA personnalisée. Cette volonté peut être motivée notamment par la nécessité de créer une stratégie de verrouillage de compte différente ou de configurer un paramètre de durée de vie de mot de passe par défaut pour le domaine managé.

Vous pouvez créer une SMPA personnalisée et l’appliquer à des groupes spécifiques de votre domaine managé Azure AD DS. Cette configuration remplace de fait la SMPA par défaut. Vous pouvez aussi créer des stratégies de mot de passe affinées personnalisées et les appliquer aux unités d’organisation personnalisées que vous créez dans le domaine managé Azure AD DS.

Pour créer une stratégie de mot de passe affinée, utilisez les outils d’administration Active Directory à partir d’une machine virtuelle jointe à un domaine. Le Centre d’administration Active Directory vous permet d’afficher, de modifier et de créer des ressources dans un domaine managé Azure AD DS, notamment des unités d’organisation.

> [!NOTE]
> Pour créer une stratégie de mot de passe affinée dans un domaine managé Azure AD DS, vous devez être connecté à un compte d’utilisateur membre du groupe *Administrateurs AAD DC*.

1. Dans l’écran d’accueil, sélectionnez **Outils d’administration**. La liste des outils de gestion disponibles qui ont été installés dans le tutoriel [Créer une machine virtuelle de gestion][tutorial-create-management-vm] s’affiche à l’écran.
1. Pour créer et gérer des unités d’organisation, sélectionnez **Centre d’administration Active Directory** dans la liste des outils d’administration.
1. Dans le volet gauche, choisissez votre domaine managé Azure AD DS, comme *contoso.com*.
1. Dans le volet **Tâches** à droite, sélectionnez **Nouveau > Paramètres de mot de passe**.
1. Dans la boîte de dialogue **Créer des paramètres de mot de passe**, entrez un nom pour la stratégie, par exemple *MyCustomFGPP*. Définissez la priorité de manière appropriée pour remplacer la SMPA par défaut (en l’occurrence, *200*), par exemple *1*.

    Modifiez les autres paramètres de stratégie de mot de passe à votre guise, par exemple, **Appliquer l’historique des mots de passe** pour obliger l’utilisateur à créer un mot de passe différent des *24* précédents.

    ![Créer une stratégie de mot de passe affinée personnalisée](./media/how-to/custom-fgpp.png)

1. Décochez **Protéger contre la suppression accidentelle**. Si cette option est sélectionnée, vous ne pouvez pas enregistrer la SMPA.
1. Dans la section **S’applique directement à**, cliquez sur le bouton **Ajouter**. Dans la boîte de dialogue **Select Users or Groups (Sélectionner des utilisateurs ou des groupes)** , cliquez sur le bouton **Emplacements**.

    ![Sélectionner les utilisateurs et les groupes auxquels appliquer la stratégie de mot de passe](./media/how-to/fgpp-applies-to.png)

1. Les stratégies de mot de passe affinées peuvent uniquement être appliquées à des groupes. Dans la boîte de dialogue **Emplacements**, développez le nom de domaine, comme *contoso.com*, puis sélectionnez une unité d’organisation, comme **Utilisateurs AADDC**. Si vous avez à disposition une unité d’organisation personnalisée qui contient un groupe d’utilisateurs auquel vous souhaitez appliquer une stratégie, sélectionnez cette unité d’organisation.

    ![Sélectionner l’unité d’organisation à laquelle le groupe appartient](./media/how-to/fgpp-container.png)

1. Tapez le nom du groupe auquel vous souhaitez appliquer la stratégie, puis sélectionnez **Vérifier les noms** pour vérifier que le groupe existe.

    ![Rechercher et sélectionner le groupe auquel appliquer la SMPA](./media/how-to/fgpp-apply-group.png)

1. Comme le nom du groupe que vous avez sélectionné figure dans la section **S’applique directement à**, sélectionnez **OK** pour enregistrer votre stratégie de mot de passe personnalisée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les stratégies de mot de passe affinées et l’utilisation du Centre d’administration Active Directory, consultez les articles suivants :

* [En savoir plus sur les stratégies de mot de passe affinées](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurer des stratégies de mot de passe affinées à partir du Centre d’administration Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
