---
title: 'Didacticiel : Configurer RingCentral pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et retirer automatiquement des comptes utilisateur sur RingCentral.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8aa6a8bfb690b00ee21980f4aa64d950abec2f3c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152258"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Didacticiel : Configurer RingCentral pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans RingCentral et Azure Active Directory (Azure AD) afin de configurer Azure AD pour le provisionnement et le déprovisionnement automatiques d’utilisateurs et/ou de groupes sur RingCentral.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire RingCentral](https://www.ringcentral.com/office/plansandpricing.html)
* Un compte d’utilisateur dans RingCentral avec des autorisations d’administration.

## <a name="assigning-users-to-ringcentral"></a>Attribution d’utilisateurs à RingCentral

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à RingCentral. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à votre application RingCentral en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ringcentral"></a>Conseils importants pour l’attribution d’utilisateurs à RingCentral

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à RingCentral afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à RingCentral, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="set-up-ringcentral-for-provisioning"></a>Configurer RingCentral pour le provisionnement

1. Connectez-vous à la [console d’administration RingCentral](https://login.ringcentral.com/sw.html). Accédez à **Outils > Intégration d’annuaire**.

    ![Console d’administration RingCentral](media/ringcentral-provisioning-tutorial/admin.png)

2.  Choisissez **SCIM** sous **Sélectionner le fournisseur d’annuaire**. (À l’avenir, il y aura une option appelée Azure Active Directory). Cliquez sur **Activer le service SCIM**.

    ![RingCentral - Ajouter SCIM](media/ringcentral-provisioning-tutorial/scim.png)

3.  Contactez l’équipe de support RingCentral à l’adresse matthew.hunt@ringcentral.com pour obtenir un **Jeton d’authentification SCIM**. Vous devez entrer cette valeur dans le champ Jeton secret de l’onglet Provisionnement de votre application RingCentral sur le portail Azure.

> [!NOTE]
> Pour attribuer des licences aux utilisateurs, reportez-vous au lien vidéo [ici](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="add-ringcentral-from-the-gallery"></a>Ajouter RingCentral à partir de la galerie

Avant de configurer RingCentral pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter RingCentral à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter RingCentral à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **RingCentral**, sélectionnez **RingCentral** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![RingCentral dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ringcentral"></a>Configuration du provisionnement automatique d’utilisateurs sur RingCentral 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans RingCentral en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour RingCentral, en suivant les instructions fournies dans le [tutoriel sur l’authentification unique pour RingCentral](https://docs.microsoft.comazure/active-directory/saas-apps/ringcentral-tutorial). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

> [!NOTE]
> Pour en savoir plus sur le point de terminaison SCIM de RingCentral, reportez-vous aux [informations de référence sur l’API RingCentral](https://developers.ringcentral.com/api-reference).

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour RingCentral dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **RingCentral**.

    ![Lien RingCentral dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, saisissez `https://platform.ringcentral.com/scim/v2` dans **URL du locataire**. Saisissez la valeur de **Jeton d’authentification SCIM** récupérée précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à RingCentral. Si la connexion échoue, vérifiez que votre compte RingCentral dispose des autorisations d’administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur RingCentral**.

    ![Mappages d’utilisateurs RingCentral](media/ringcentral-provisioning-tutorial/usermappings.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et RingCentral. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans RingCentral pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur RingCentral](media/ringcentral-provisioning-tutorial/userattributes.png)

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Afin d’activer le service de provisionnement Azure AD pour RingCentral, modifiez le paramètre **État de provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner sur RingCentral en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur RingCentral.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

