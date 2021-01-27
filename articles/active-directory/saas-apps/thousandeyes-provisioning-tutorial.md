---
title: 'Tutoriel : Approvisionnement d’utilisateurs pour ThousandEyes – Azure AD'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur sur ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735087"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutoriel : Configurer ThousandEyes pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans ThousandEyes et Azure AD pour approvisionner et retirer automatiquement des comptes d’utilisateur entre Azure AD et ThousandEyes. 

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un locataire Azure Active Directory
* Un locataire ThousandEyes pour lequel au moins le [plan standard](https://www.thousandeyes.com/pricing) est activé 
* Un compte d’utilisateur dans ThousandEyes doté d’autorisations d’administrateur 

> [!NOTE]
> L’intégration de l’approvisionnement d’Azure AD repose sur l’[API ThousandEyes SCIM](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), mise à la disposition des équipes de ThousandEyes qui relèvent au moins du plan standard.

## <a name="assigning-users-to-thousandeyes"></a>Affectation d’utilisateurs à ThousandEyes

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application ThousandEyes. Dès que vous avez fait votre choix, vous pouvez assigner ces utilisateurs à votre application ThousandEyes en suivant les instructions disponibles à la page suivante :

[Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Conseils importants concernant l’affectation d’utilisateurs à ThousandEyes

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à ThousandEyes afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous affectez un utilisateur à ThousandEyes, vous devez sélectionner le rôle **utilisateur** ou un autre rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement et ces utilisateurs sont ignorés.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configuration de l'approvisionnement d’utilisateurs pour ThousandEyes 

Cette section va vous guider afin de connecter votre instance Azure AD à l’API d’approvisionnement de comptes d’utilisateur de ThousandEyes, puis configurer le service d’approvisionnement pour créer, mettre à jour et désactiver des comptes d’utilisateur affectés dans ThousandEyes, en fonction des affectations d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour ThousandEyes, grâce aux instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurer l’approvisionnement automatique de comptes d’utilisateur pour ThousandEyes dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Si vous avez déjà configuré ThousandEyes pour l’authentification unique, recherchez votre instance de ThousandEyes à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **ThousandEyes** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez ThousandEyes, puis ajoutez-le à votre liste d’applications.

    ![Lien ThousandEyes dans la liste des applications](common/all-applications.png)
    
3. Sélectionnez votre instance de ThousandEyes, puis sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

![Capture d’écran montrant l’onglet Approvisionnement pour ThousandEyes avec Automatique sélectionné pour le mode d’approvisionnement.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **jeton du porteur OAuth** généré par votre compte ThousandEyes (vous pouvez le trouver ou le générer sous la section **Profil** de votre compte ThousandEyes).

    ![Capture d’écran montrant où trouver le lien des paramètres de compte pour le groupe de comptes actuel.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application ThousandEyes. Si la connexion échoue, vérifiez que votre compte ThousandEyes dispose des autorisations d’administrateur et réessayez l’étape 5.

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ThousandEyes**.

10. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers ThousandEyes dans la section **Mappages d’attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Parsable pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](../app-provisioning/customize-application-attributes.md), vérifiez que l’API Parsable prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

     |Attribut|Type|Pris en charge pour le filtrage|
     |---|---|---|
     |externalId|String|&check;|
     |userName|String|&check;|
     |active|Boolean|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |name.formatted|String|


11. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pour activer le service de provisionnement Azure AD pour ThousandEyes, accédez à la section **Paramètres** et définissez l’**État de l’approvisionnement** sur **Activé**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

13. Définissez les utilisateurs et/ou les groupes que vous voulez provisionner dans ThousandEyes en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

14. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. 

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)