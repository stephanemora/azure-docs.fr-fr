---
title: 'Tutoriel : Configurer Rollbar pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et dé-approvisionner automatiquement des comptes d’utilisateur dans Rollbar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: d9720ca769eab8cf0e4ee763c720f6ba12ebb1d9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063286"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Tutoriel : Configurer Rollbar pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est d’illustrer les étapes à effectuer dans Rollbar et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et retirer automatiquement des utilisateurs et/ou des groupes à Rollbar.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire Rollbar](https://rollbar.com/pricing/) qui a un plan d’entreprise.
* Un compte d’utilisateur dans Rollbar avec des autorisations d’administrateur.

## <a name="assigning-users-to-rollbar"></a>Affectation d’utilisateurs à Rollbar

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Rollbar. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Rollbar en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rollbar"></a>Conseils importants pour l’attribution d’utilisateurs à Rollbar

* Il est recommandé qu’un seul utilisateur Azure AD soit affecté à Rollbar pour tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Lorsque vous affectez un utilisateur à Rollbar, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-rollbar-for-provisioning"></a>Configurer Rollbar pour l’approvisionnement

Avant de configurer Rollbar pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM sur Rollbar.

1. Connectez-vous à votre [console d’administration Rollbar](https://rollbar.com/login/). Cliquez sur **Paramètres du compte**.

    ![Console d’administration Rollbar](media/rollbar-provisioning-tutorial/image00.png)

2. Accédez à votre **nom de locataire Rollbar > Jetons d’accès du compte**.

    ![Console d’administration Rollbar](media/rollbar-provisioning-tutorial/account.png)

3. Copiez la valeur de **SCIM**. Cette valeur sera entrée dans le champ jeton secret de l’onglet approvisionnement de votre application Rollbar dans la Portail Azure.

    ![Console d’administration Rollbar](media/rollbar-provisioning-tutorial/scim.png)

## <a name="add-rollbar-from-the-gallery"></a>Ajout de Rollbar depuis la galerie

Pour configurer Rollbar pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Rollbar à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Rollbar à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Rollbar**, sélectionnez **Rollbar** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Rollbar dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rollbar"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Rollbar 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Rollbar en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Rollbar en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Rollbar](rollbar-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Rollbar dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Rollbar**.

    ![Lien Rollbar dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **informations d’identification de l’administrateur**, entrez la valeur du **jeton d' accès au compte** extraite précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Rollbar. Si la connexion échoue, vérifiez que votre compte Rollbar dispose des autorisations d’administrateur et réessayez.

    ![Console d’administration Rollbar](media/rollbar-provisioning-tutorial/admin.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Rollbar**.

    ![Mappages d’utilisateurs Rollbar](media/rollbar-provisioning-tutorial/usermapping.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Rollbar. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Rollbar pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur Rollbar](media/rollbar-provisioning-tutorial/userattribute.png)

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Rollbar**.

    ![Mappages de groupes Rollbar](media/rollbar-provisioning-tutorial/groupmapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs groupe qui sont synchronisés entre Azure AD et Rollbar. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Rollbar dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs du groupe Rollbar](media/rollbar-provisioning-tutorial/groupattribute.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour Rollbar, affectez la valeur **Activé** au paramètre **Statut d’approvisionnement** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner sur Rollbar en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

    Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Rollbar.

    Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
