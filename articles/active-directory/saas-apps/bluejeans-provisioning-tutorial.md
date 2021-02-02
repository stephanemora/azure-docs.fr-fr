---
title: 'Tutoriel : Configurer BlueJeans pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour attribuer et retirer automatiquement des utilisateurs dans BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8e155a253910cc5ee3f4fc71cf9ea66ced5cb46f
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742112"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Configurer BlueJeans pour l’attribution automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à suivre dans BlueJeans et Azure Active Directory (Azure AD) permettant de configurer Azure AD pour l’attribution et le retrait automatiques d’utilisateurs et/ou de groupes sur BlueJeans.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* un locataire Azure AD ;
* Un locataire BlueJeans pour lequel est activé le plan [My Company](https://www.BlueJeans.com/pricing) (Ma société), ou niveau de plan plus élevé
* Un compte d’utilisateur dans BlueJeans avec des autorisations d’administration

> [!NOTE]
> L’intégration de l’attribution d’utilisateurs Azure AD repose sur [l’API BlueJeans](https://BlueJeans.github.io/developer), qui est mise à la disposition des équipes BlueJeans qui relèvent au moins du plan standard.

## <a name="adding-bluejeans-from-the-gallery"></a>Ajout de BlueJeans à partir de la galerie

Avant de configurer BlueJeans pour l’attribution automatique des utilisateurs avec Azure AD, vous devez ajouter BlueJeans à partir de la galerie d’applications Azure AD, à votre liste d’applications SaaS managées.

**Pour ajouter BlueJeans à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **BlueJeans**, sélectionnez **BlueJeans** dans le volet de résultats, puis sélectionnez le bouton **Ajouter** pour ajouter l’application.

    ![BlueJeans dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Attribution d’utilisateurs dans BlueJeans

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’attribution automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à BlueJeans. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à votre application BlueJeans en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Conseils importants pour l’attribution d’utilisateurs à BlueJeans

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à BlueJeans pour tester la configuration de l’attribution automatique des utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à BlueJeans, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configuration de l’attribution automatique d’utilisateurs dans BlueJeans

Cette section vous guide tout au long des étapes de configuration du service d’attribution d’utilisateurs d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans BlueJeans en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour BlueJeans en suivant les instructions fournies dans le [tutoriel sur l’authentification unique BlueJeans](bluejeans-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour BlueJeans dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **BlueJeans**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **BlueJeans**.

    ![Lien BlueJeans dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification d’administrateur**, entrez vos URL de locataire et Jeton secret BlueJeans. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à BlueJeans. Si la connexion échoue, vérifiez que votre compte BlueJeans dispose des autorisations d’administrateur, puis réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)


6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec BlueJeans**.

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et BlueJeans. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour établir une correspondance avec les comptes d’utilisateur BlueJeans en vue de mises à jour ultérieures. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

|Attribut|Type|Pris en charge pour le filtrage|
|---|---|---|
|userName|String|&check;|
|active|Boolean|
|title|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’attribution d’utilisateurs Azure AD pour BlueJeans, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous aimeriez attribuer dans BlueJeans en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’attribution d’utilisateurs, qui décrivent toutes les actions effectuées par le service d’attribution d’utilisateurs Azure AD dans BlueJeans.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Bluejeans n’autorise pas les noms d’utilisateurs (« userNames ») de plus de 30 caractères.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
