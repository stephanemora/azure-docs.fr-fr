---
title: 'Tutoriel : Approvisionnement d’utilisateurs pour GitHub - Azure AD'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et désapprovisionner automatiquement l’appartenance de l’organisation utilisateur dans le cloud GitHub Enterprise.
services: active-directory
author: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: thwimmer
ms.openlocfilehash: b8de34165c327d335bff399319852ef035a7094e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728227"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutoriel : Configurer GitHub pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de vous montrer les étapes à effectuer dans GitHub et Azure AD pour automatiser l’approvisionnement de l’appartenance de l’organisation cloud GitHub Enterprise.

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur l’[API GitHub SCIM](https://developer.github.com/v3/scim/) disponible pour les clients [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) dans le [plan de facturation GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un locataire Azure Active Directory
* Une organisation GitHub créée dans [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), ce qui nécessite le [plan de facturation GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Un compte d’utilisateur dans GitHub avec des autorisations d’administrateur vers l’organisation
* [SAML configuré pour l’organisation GitHub Enterprise Cloud](./github-tutorial.md)
* Assurez-vous que l’accès OAuth a été fourni pour votre organisation, comme décrit [ici](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)
* Le provisionnement SCIM pour une seule organisation est pris en charge uniquement lorsque l’authentification unique est activée au niveau de l’organisation

> [!NOTE]
> Cette intégration peut également être utilisée à partir de l’environnement cloud US Government Azure AD. Cette application est disponible dans la Galerie d’applications cloud US Government Azure AD et peut être configurée de la même façon que dans le cloud public.

## <a name="assigning-users-to-github"></a>Attribution d’utilisateurs à GitHub

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application GitHub. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs à votre application GitHub en suivant les instructions fournies ici :

Pour plus d’informations, voir [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-github"></a>Conseils importants pour l’attribution d’utilisateurs à GitHub

* Nous vous recommandons d’affecter un seul utilisateur Azure AD à GitHub pour tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à GitHub, vous devez sélectionner le rôle **Utilisateur** ou un autre rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement et ces utilisateurs sont ignorés.

## <a name="configuring-user-provisioning-to-github"></a>Configuration de l'approvisionnement des utilisateurs sur GitHub

Cette section vous guide tout au long de la connexion de votre Azure AD à l’API d’approvisionnement SCIM GitHub pour automatiser la configuration de l’appartenance à l’organisation GitHub. Cette intégration, qui tire parti d’une [application OAuth](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/authorizing-oauth-apps#oauth-apps-and-organizations), ajoute, gère et supprime automatiquement l’accès des membres à une organisation cloud GitHub Enterprise en fonction de l’affectation d’utilisateurs et de groupes dans Azure AD. Lorsque des utilisateurs sont [configurés pour une organisation GitHub via SCIM](https://docs.github.com/en/free-pro-team@latest/rest/reference/scim#provision-and-invite-a-scim-user), une invitation par e-mail est envoyée à l’adresse e-mail de l’utilisateur.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurer l’approvisionnement automatique de comptes utilisateurs vers GitHub dans Azure AD

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré GitHub pour l’authentification unique, recherchez votre instance de GitHub à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **GitHub** dans la galerie d’applications. Sélectionnez GitHub dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de GitHub, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

   ![Approvisionnement GitHub](./media/github-provisioning-tutorial/github1.png)

5. Sous la section **informations d’identification de l’administrateur**, cliquez sur **Autoriser**. Cette opération ouvre une boîte de dialogue d’autorisation GitHub dans une nouvelle fenêtre du navigateur. Notez que vous devez vous assurer d’être approuvé pour autoriser l’accès. Suivez les instructions décrites [ici](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. Dans la nouvelle fenêtre, connectez-vous à GitHub à l’aide de votre compte d’administrateur. Dans la boîte de dialogue d’autorisation qui s’affiche, sélectionnez l’équipe GitHub pour laquelle vous souhaitez activer l’approvisionnement, puis sélectionnez **Autoriser**. Une fois cela terminé, revenez au portail Azure pour terminer la configuration de l’approvisionnement.

   ![Capture d’écran représentant la page de connexion pour GitHub.](./media/github-provisioning-tutorial/github2.png)

7. Dans le portail Azure, entrez **URL cliente** et cliquez sur **Connexion test** pour vous assurer qu’Azure AD peut se connecter à votre application GitHub. En cas d’échec de connexion, vérifiez que votre compte GitHub dispose des autorisations Administrateur et que **l’URL de locataire** est correctement entrée. Ensuite, recommencez l’étape « Autoriser » (vous pouvez constituer **l’URL de locataire** par règle : `https://api.github.com/scim/v2/organizations/<Organization_name>`, et rechercher vos organisations sous votre compte GitHub : **Paramètres** > **Organisations**).

   ![Capture d’écran représentant la page Organisations dans GitHub.](./media/github-provisioning-tutorial/github3.png)

8. Entrez l’adresse de courrier d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case « Envoyer une notification par e-mail en cas de défaillance ».

9. Cliquez sur **Enregistrer**.

10. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec GitHub**.

11. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateur qui sont synchronisés d’Azure AD vers GitHub. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans GitHub pour les opérations de mise à jour. N’activez pas le paramètre **précédence de la correspondance** pour les autres attributs par défaut dans la section **Approvisionnement** avant que les erreurs se produisent. Sélectionnez **Enregistrer** pour valider les modifications.

12. Pour activer le service d’approvisionnement Azure AD pour GitHub, définissez **l’état d’approvisionnement** sur **Activé** dans la section **Paramètres**.

13. Cliquez sur **Enregistrer**.

Cette opération démarre la synchronisation initiale des utilisateurs et/ou des groupes attribués à GitHub dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement. 

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
