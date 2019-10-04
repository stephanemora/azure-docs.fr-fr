---
title: 'Didacticiel : Configurer Wrike pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner des comptes utilisateur ou annuler l’approvisionnement de ceux-ci de façon automatique sur Wrike.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: f94c12ed355b25e6a10c8d71e176d9bf3bf78758
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847001"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Didacticiel : Configurer Wrike pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Wrike et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et l’annulation d’approvisionnement automatiques d’utilisateurs et/ou de groupes sur Wrike.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [un locataire Wrike](https://www.wrike.com/price/) ;
* Un compte d’utilisateur dans Wrike avec des autorisations d’administrateur.

## <a name="assign-users-to-wrike"></a>Attribuer des utilisateurs à Wrike
Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Wrike. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à Wrike en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Conseils importants pour l’attribution d’utilisateurs à Wrike

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Wrike afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à Wrike, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue Attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="set-up-wrike-for-provisioning"></a>Configurer Wrike pour l’approvisionnement

Avant de configurer Wrike pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM sur Wrike.

1. Connectez-vous à votre [Console d’administration Wrike](https://www.Wrike.com/login/). Accédez à votre ID de locataire. Sélectionnez **Applications et intégrations**.

    ![Console d’administration Wrike](media/Wrike-provisioning-tutorial/admin.png)

2.  Accédez à **Azure AD**. Sélectionnez-le.

    ![Console d’administration Wrike](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Sélectionnez le SCIM. Copier l’**URL de base**.

    ![Console d’administration Wrike](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Cliquez sur **API** > Sélectionnez **SCIM Azure**.

    ![Ajouter SCIM Wrike](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Une fenêtre contextuelle s’ouvre. Entrez le mot de passe que vous avez créé lors de la création du compte 

    ![Création de jeton Wrike](media/Wrike-provisioning-tutorial/password.png)

6.  Copiez le **jeton secret** et collez-le dans Azure AD. Cliquez sur **Enregistrer** pour achever la configuration de l’approvisionnement sur Wrike.

    ![Création de jeton Wrike](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Ajouter Wrike à partir de la galerie

Avant de configurer Wrike pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Wrike à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Wrike à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Wrike**, sélectionnez **Wrike** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Wrike dans la liste des résultats](common/search-new-app.png)


## <a name="configuring-automatic-user-provisioning-to-wrike"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Wrike 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Wrike en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Wrike en suivant les instructions fournies dans le [didacticiel sur l’authentification unique pour Wrike](wrike-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Wrike dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Wrike**.

    ![Lien Wrike dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section Informations d’identification de l’administrateur, entrez respectivement les valeurs de l’**URL de base** et du **Jeton d’accès permanent** récupérées précédemment dans les champs **URL de locataire** et **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Wrike. Si la connexion échoue, vérifiez que votre compte Wrike dispose des autorisations d’administrateur, puis réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Wrike**.

    ![Mappages d’utilisateurs Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Wrike. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Wrike pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pour activer le service d’approvisionnement Azure AD pour Wrike, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

13. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur Wrike en choisissant les valeurs souhaitées sous **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

14. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Pour plus d’informations sur la durée d’approvisionnement des utilisateurs et/ou des groupes, voir [Combien de temps faut-il pour approvisionner des utilisateurs ?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Vous pouvez utiliser la section **État actuel** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Wrike. Pour plus d’informations, consultez [Vérifier l’état de l’approvisionnement d’utilisateurs](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Pour la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
