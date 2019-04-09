---
title: 'Tutoriel : Configurer G Suite pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et annuler automatiquement l’approvisionnement des comptes d’utilisateur d’Azure AD vers G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1f4d4a6b60961515826a1ba7409bf149b318e8
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058473"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutoriel : Configurer G Suite pour l’attribution automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer comment approvisionner et annuler automatiquement l’approvisionnement des comptes d’utilisateur d’Azure Active Directory (Azure AD) vers G Suite.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à G Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement G Suite pour lequel l’authentification unique est activée
- Un abonnement Google Apps ou Google Cloud Platform.

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Affecter des utilisateurs à G Suite

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, seuls les utilisateurs et les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à votre application. Une fois cette décision prise, vous pouvez affecter ces utilisateurs à votre application en suivant les instructions fournies dans [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Nous vous recommandons de n’affecter qu’un seul utilisateur Azure AD à G Suite pour tester la configuration de l’approvisionnement. Vous pouvez affecter des groupes et des utilisateurs supplémentaires ultérieurement.
> 
> Quand vous affectez un utilisateur à G Suite, sélectionnez le rôle **Utilisateur** ou **Groupe** dans la boîte de dialogue d’affectation. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section vous guide tout au long du processus de connexion d’Azure AD à l’API d’approvisionnement de comptes d’utilisateur de G Suite. Elle vous aide également à configurer le service d’approvisionnement pour créer, mettre à jour et désactiver les comptes d’utilisateur affectés dans G Suite en fonction de l’affectation d’utilisateurs et de groupes dans Azure AD.

>[!TIP]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour G Suite, en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning"></a>Configurer l’approvisionnement automatique d’un compte utilisateur

> [!NOTE]
> Une autre option possible pour automatiser l’approvisionnement des utilisateurs dans G Suite consiste à utiliser [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS approvisionne vos identités Active Directory locales dans G Suite. En revanche, la solution proposée dans ce tutoriel approvisionne vos utilisateurs Azure Active Directory (cloud) et vos groupes à extension messagerie dans G Suite. 

1. Connectez-vous à la [Console d’administration de Google Apps](https://admin.google.com/) avec votre compte d’administrateur, puis sélectionnez **Sécurité**. Si le lien ne s’affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l’écran.

    ![Sélectionnez Sécurité.][10]

1. Dans la page **Sécurité**, sélectionnez **Informations de référence sur l’API**.

    ![Sélectionnez Informations de référence sur l’API.][15]

1. Sélectionnez **Activer l'accès à l'API**.

    ![Sélectionnez Informations de référence sur l’API.][16]

   > [!IMPORTANT]
   > Le nom d’utilisateur Azure Active Directory de chaque utilisateur que vous souhaitez approvisionner dans G Suite *doit* être associé à un domaine personnalisé. Par exemple, les noms d’utilisateur qui ressemblent à bob@contoso.onmicrosoft.com ne sont pas acceptés par G Suite. En revanche, bob@contoso.com est accepté. Vous pouvez modifier le domaine d’un utilisateur existant en modifiant ses propriétés dans Azure AD. Les étapes suivantes montrent comment définir un domaine personnalisé pour Azure Active Directory et G Suite.

1. Si vous n’avez pas encore ajouté de nom de domaine personnalisé à Azure Active Directory, effectuez les étapes suivantes :
  
    a. Dans le panneau de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez **Active Directory**. Dans la liste Annuaire, sélectionnez votre annuaire.

    b. Sélectionnez **Nom de domaine** dans le volet de navigation gauche, puis sélectionnez **Ajouter**.

    ![Domaine](./media/google-apps-provisioning-tutorial/domain_1.png)

    ![Ajout de domaine](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Saisissez votre nom de domaine dans le champ **Nom de domaine** . Ce nom de domaine doit être identique à celui que vous souhaitez utiliser pour G Suite. Ensuite, sélectionnez le bouton **Ajouter un domaine**.

    ![Nom de domaine](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Sélectionnez **Suivant** pour accéder à la page de vérification. Pour vérifier que vous être propriétaire de ce domaine, modifiez ses enregistrements DNS en fonction des valeurs fournies dans cette page. Vous pouvez choisir d’effectuer la vérification en utilisant des **enregistrements MX** ou des **enregistrements TXT**, selon l’option de **Type d’enregistrement** sélectionnée.

    Pour obtenir des instructions plus complètes sur la vérification des noms de domaine avec Azure AD, consultez [Ajouter votre propre domaine à Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).

    ![Domaine](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Répétez la procédure précédente pour tous les domaines que vous souhaitez ajouter à votre répertoire.

    > [!NOTE]
    > Pour l’attribution d’utilisateurs, le domaine personnalisé doit correspondre au nom de domaine de l’annuaire Azure AD source. S’ils ne correspondent pas, vous pouvez peut-être résoudre le problème en mettant en œuvre une personnalisation du mappage des attributs.

1. Maintenant que vous avez vérifié tous vos domaines avec Azure AD, vous devez les revérifier avec Google Apps. Pour chaque domaine qui n’est pas encore inscrit auprès de Google, effectuez les étapes suivantes :

    a. Dans la [Console d’administration de Google Apps](https://admin.google.com/), sélectionnez **Domaines**.

    ![Sélectionner les domaines][20]

    b. Sélectionnez **Ajouter un domaine ou un alias de domaine**.

    ![Ajouter un nouveau domaine][21]

    c. Sélectionnez **Ajouter un autre domaine**, puis tapez le nom du domaine que vous souhaitez ajouter.

    ![Entrer votre nom de domaine][22]

    d. Sélectionnez **Continuer et vérifier la propriété du domaine**. Puis suivez les étapes pour vérifier que vous possédez le nom de domaine. Pour obtenir des instructions complètes sur la vérification de votre domaine avec Google, consultez [Vérification de votre site avec Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Répétez la procédure précédente pour tous les domaines supplémentaires que vous souhaitez ajouter à Google Apps.

    > [!WARNING]
    > Si vous changez le domaine principal pour votre locataire G Suite tout en ayant déjà configuré l’authentification unique avec Azure AD, vous devez répéter l’étape 3 sous Étape 2 : Activer l’authentification unique.

1. Dans la [Console d’administration de Google Apps](https://admin.google.com/), sélectionnez **Rôles d’administrateur**.

    ![Sélectionner Google Apps][26]

1. Déterminez le compte d’administrateur à utiliser pour gérer l’approvisionnement des utilisateurs. Pour le **Rôle d’administrateur** de ce compte, modifiez les **Privilèges** pour ce rôle. Veillez à activer tous les **Privilèges d’administrateur d’API** pour que ce compte puisse être utilisé pour l’approvisionnement.

    ![Sélectionner Google Apps][27]

    > [!NOTE]
    > Si vous configurez un environnement de production, la meilleure pratique est de créer un compte d’administrateur dans G Suite spécialement pour cette étape. Ce compte doit avoir un rôle d’administrateur associé qui dispose des privilèges d’API nécessaires.

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.

1. Si vous avez déjà configuré G Suite pour l’authentification unique, recherchez votre instance de G Suite à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **G Suite** ou **Google Apps** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez votre application, puis ajoutez-la à votre liste d’applications.

1. Sélectionnez votre instance G Suite, puis sélectionnez l’onglet **Attribution**.

1. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![Approvisionnement](./media/google-apps-provisioning-tutorial/provisioning.png)

1. Sous la section **Informations d’identification de l’administrateur**, sélectionnez **Autoriser**. Une boîte de dialogue d’autorisation Google s’ouvre dans une nouvelle fenêtre de navigateur.

1. Confirmez que vous souhaitez autoriser Azure Active Directory à apporter des modifications à votre locataire G Suite. Sélectionnez **Accepter**.

    ![Confirmez les autorisations.][28]

1. Sur le Portail Azure, sélectionnez **Tester la connexion** pour vérifier qu’Azure AD parvient à se connecter à votre application. Si la connexion échoue, vérifiez que votre compte G Suite dispose des autorisations Administrateur d’équipe. Ensuite, réessayez d’effectuer l’étape **Autoriser**.

1. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**. Ensuite, cochez la case.

1. Sélectionnez **Enregistrer**.

1. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Google Apps**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateur qui sont synchronisés d’Azure AD vers G Suite. Les attributs qui sont des propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans G Suite pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

1. Pour activer le service d’approvisionnement Azure AD pour G Suite, affectez la valeur **Activé** au paramètre **État de l’approvisionnement** dans les **Paramètres**.

1. Sélectionnez **Enregistrer**.

Ce processus démarre la synchronisation initiale des utilisateurs ou des groupes affectés à G Suite dans la section Utilisateurs et groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ pendant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement. Ces journaux décrivent toutes les actions effectuées par le service de provisionnement dans votre application.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’authentification unique](google-apps-tutorial.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
