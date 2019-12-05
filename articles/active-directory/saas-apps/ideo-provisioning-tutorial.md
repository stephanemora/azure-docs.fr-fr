---
title: 'Didacticiel : Configurer IDEO pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour attribuer et désattribuer automatiquement des comptes d’utilisateur dans IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: d6bc3170162710e86359b374d1ef707bba0ce268
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152218"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Didacticiel : Configurer IDEO pour l’attribution automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans IDEO et Azure Active Directory (Azure AD) afin de configurer Azure AD pour attribuer et désattribuer automatiquement des utilisateurs et/ou des groupes dans IDEO.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire IDEO](https://www.shape.space/product/pricing)
* Un compte d’utilisateur sur IDEO | Shape avec des autorisations d’administrateur

## <a name="assign-users-to-ideo"></a>Affecter des utilisateurs à IDEO

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’attribution automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à IDEO. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs et/ou groupes à IDEO en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Conseils importants pour l’affectation d’utilisateurs à IDEO

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à IDEO afin de tester la configuration de l’attribution automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous affectez un utilisateur à IDEO, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="set-up-ideo-for-provisioning"></a>Configurer IDEO pour l’attribution

Avant de configurer IDEO pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez récupérer certaines informations d’attribution dans IDEO.

1. Pour obtenir un **jeton secret**, contactez l’équipe de support IDEO à l’adresse productsupport@ideo.com. Vous devrez entrer cette valeur dans le champ **Jeton secret** sous l’onglet Approvisionnement de votre application IDEO dans le portail Azure. 

## <a name="add-ideo-from-the-gallery"></a>Ajouter IDEO à partir de la galerie

Pour configurer IDEO pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez ajouter IDEO à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **IDEO**, puis sélectionnez **IDEO** dans le volet de résultats. 

    ![IDEO dans la liste des résultats](common/search-new-app.png)

5. Sélectionnez le bouton **S’inscrire à IDEO** qui vous redirige vers la page de connexion d’IDEO. 

    ![IDEO - Ajouter OIDC](media/ideo-provisioning-tutorial/signup.png)

6. Comme IDEO est une application OpenIDConnect, choisissez de vous connecter à IDEO avec votre compte professionnel Microsoft.

    ![IDEO - Connexion OIDC](media/ideo-provisioning-tutorial/login.png)

7. Une fois l’authentification réussie, acceptez l’invite de consentement pour la page de consentement. L’application est alors automatiquement ajoutée à votre locataire et vous êtes redirigé vers votre compte IDEO.

    ![IDEO - Consentement OIDC](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Configurer l’attribution automatique d’utilisateurs sur IDEO 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans IDEO en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour IDEO dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **IDEO**.

    ![Lien IDEO dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://profile.ideo.com/api/scim/v2` dans **URL de locataire**. Entrez la valeur que vous avez récupérée auprès de l’équipe de support IDEO dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à IDEO. Si la connexion échoue, vérifiez que votre compte IDEO dispose des autorisations d’administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec IDEO**.

    ![Mappages d’utilisateurs IDEO](media/ideo-provisioning-tutorial/usermappings.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et IDEO. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans IDEO pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur IDEO](media/ideo-provisioning-tutorial/userattributes.png)

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service de provisionnement Azure AD pour IDEO, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](media/ideo-provisioning-tutorial/groupmappings.png)

12. Définissez les utilisateurs et/ou les groupes que vous souhaitez attribuer à IDEO en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](media/ideo-provisioning-tutorial/groupattributes.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur IDEO.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)


