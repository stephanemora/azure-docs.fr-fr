---
title: 'Tutoriel : Configurer Templafy OpenID Connect pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateur dans Templafy OpenID Connect.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: thwimmer
ms.openlocfilehash: 56046874155cd54a380d31607331d76f9982c167
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759105"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Tutoriel : Configurer Templafy OpenID Connect pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Templafy OpenID Connect et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et/ou des groupes dans Templafy OpenID Connect.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire Templafy](https://www.templafy.com/pricing/).
* Un compte d’utilisateur dans Templafy avec des autorisations d’administrateur

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](../app-provisioning/user-provisioning.md).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Déterminez les données à [mapper entre Azure AD et Templafy OpenID Connect](../app-provisioning/customize-application-attributes.md). 

## <a name="assigning-users-to-templafy-openid-connect"></a>Affectation d’utilisateurs à Templafy OpenID Connect

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez déterminer quels sont les utilisateurs et/ou groupes dans Azure AD qui ont besoin d’accéder à Templafy OpenID Connect. Une fois votre choix effectué, vous pouvez affecter ces utilisateurs et/ou groupes à Templafy OpenID Connect en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>Conseils importants pour l’affectation d’utilisateurs à Templafy OpenID Connect

* Il est recommandé d’affecter un seul utilisateur Azure AD à Templafy OpenID Connect pour tester la configuration du provisionnement automatique d’utilisateurs. Vous pouvez affecter plus tard d’autres utilisateurs et/ou groupes.

* Quand vous affectez un utilisateur à Templafy OpenID Connect, vous devez sélectionner un rôle valide spécifique à l’application (le cas échéant) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Templafy OpenID Connect pour prendre en charge le provisionnement avec Azure AD

Avant de configurer Templafy OpenID Connect pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez activer le provisionnement SCIM sur Templafy OpenID Connect.

1. Connectez-vous à la Console d’administration Templafy. Cliquez sur **Administration**.

    ![Console d’administration Templafy](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. Cliquez sur **Méthode d'authentification**.

    ![Capture d’écran de la section d’administration Templafy avec l’option Méthode d’authentification en évidence.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. Copiez la valeur de la **clé API SCIM**. Vous devez entrer cette valeur dans le champ **Jeton secret** sous l’onglet Provisionnement de votre application Templafy OpenID Connect dans le portail Azure.

    ![Capture d’écran de la clé API SCIM.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>Étape 3. Ajouter Templafy OpenID Connect à partir de la galerie

Si vous souhaitez configurer Templafy OpenID Connect pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Templafy OpenID Connect à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Templafy OpenID Connect à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Templafy OpenID Connect**, sélectionnez **Templafy OpenID Connect** dans le panneau Résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Templafy OpenID Connect dans la liste des résultats](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>Étape 4. Configurer le provisionnement automatique d’utilisateurs sur Templafy OpenID Connect 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Templafy OpenID Connect en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur OpenID Connect pour Templafy en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Templafy](templafy-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Templafy OpenID Connect dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Templafy OpenID Connect**.

    ![Lien Templafy OpenID Connect dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://scim.templafy.com/scim` dans **URL de locataire**. Entrez la valeur de la **clé API SCIM** récupérée dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Templafy. Si la connexion échoue, vérifiez que votre compte Templafy dispose des autorisations d’administrateur, puis réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Templafy OpenID Connect**.

    ![Mappages d’utilisateurs Templafy OpenID Connect](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. Dans la section **Mappage d’attributs**, passez en revue les attributs utilisateur synchronisés entre Azure AD et Templafy OpenID Connect. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans Templafy OpenID Connect pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|Pris en charge pour le filtrage|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |displayName|String|
   |title|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Templafy**.

    ![Mappages de groupes Templafy OpenID Connect](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. Dans la section **Mappage d’attributs**, passez en revue les attributs de groupe synchronisés entre Azure AD et Templafy OpenID Connect. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les groupes dans Templafy OpenID Connect pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |membres|Informations de référence|
      |externalId|String|      

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Si vous souhaitez activer le service de provisionnement Azure AD pour Templafy OpenID Connect, changez l’option **État de provisionnement** en lui affectant la valeur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes à provisionner dans Templafy OpenID Connect en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

    Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour superviser la progression et suivre les liens vers le rapport d’activité de provisionnement, qui décrit toutes les actions effectuées par le service de provisionnement Azure AD sur Templafy OpenID Connect.

## <a name="step-5-monitor-your-deployment"></a>Étape 5. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](../reports-monitoring/concept-provisioning-logs.md) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)