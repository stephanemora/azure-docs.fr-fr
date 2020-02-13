---
title: 'Tutoriel : Configurer 15Five pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateurs sur 15Five.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059224"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Tutoriel : Configurer 15Five pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans 15Five et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur 15Five.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire 15Five](https://www.15five.com/pricing/).
* Un compte d’utilisateur dans 15Five avec des autorisations d’administration.

## <a name="assigning-users-to-15five"></a>Affectation d’utilisateurs à 15Five

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à 15Five. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à 15Five en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Conseils importants pour l’affectation d’utilisateurs à 15Five

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à 15Five afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à 15Five, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-15five-for-provisioning"></a>Configurez 15Five pour l’approvisionnement

Avant de configurer 15Five pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM sur 15Five.

1. Connectez-vous à votre [Console d’administration 15Five](https://my.15five.com/). Accédez à **Fonctionnalités > Intégrations**.

    ![Console d’administration 15Five](media/15five-provisioning-tutorial/integration.png)

2.  Cliquez sur **SCIM 2.0**.

    ![Console d’administration 15Five](media/15five-provisioning-tutorial/image00.png)

3.  Accédez à **Intégration SCIM > Générer un jeton OAuth**.

    ![Ajouter SCIM 15Five](media/15five-provisioning-tutorial/image02.png)

4.  Copiez les valeurs de l’**URL de base SCIM 2.0** et du **jeton d’accès**. Cette valeur doit être entrée dans les champs **URL de locataire** et **Jeton secret** dans l’onglet Approvisionnement de votre application 15Five dans le Portail Azure.
    
    ![Ajouter SCIM 15Five](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Ajouter 15Five à partir de la galerie

Avant de configurer 15Five pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter 15Five à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter 15Five à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **15Five**, sélectionnez **15Five** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![15Five dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur 15Five 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans 15Five en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour 15Five en suivant les instructions fournies dans le [didacticiel sur l’authentification unique pour 15Five](15five-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour 15Five dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **15Five**.

    ![Lien 15Five dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5.  Sous la section Informations d’identification de l’administrateur, entrez les valeurs d’**URL de base SCIM 2.0 et de Jeton du porteur** récupérées précédemment dans **URL de locataire** et **Jeton secret**, respectivement. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à 15Five. Si la connexion échoue, vérifiez que votre compte 15Five dispose des autorisations d’administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to 15Five (Synchroniser les utilisateurs Azure Active Directory avec 15Five)** .

    ![Mappages d’utilisateurs 15Five](media/15five-provisioning-tutorial/usermapping.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et 15Five. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans 15Five pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur 15Five](media/15five-provisioning-tutorial/userattribute.png)

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec 15Five**.

    ![Mappages de groupes 15Five](media/15five-provisioning-tutorial/groupmapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et 15Five. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans 15Five dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs du groupe 15Five](media/15five-provisioning-tutorial/groupattribute.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’approvisionnement Azure AD pour 15Five, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur 15Five en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

    Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur 15Five.

    Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitations du connecteur

* 15FiveBitaBIZ ne prend pas en charge les suppressions matérielles actuellement.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md).
