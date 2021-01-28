---
title: 'Tutoriel : Configurer Templafy SAML2 pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateur dans Templafy SAML2.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8a966ef5-e364-435b-9e29-3caf27ffb498
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 0e7275ee92431e791fec7bd2c9ec07dd623b0f9e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696006"
---
# <a name="tutorial-configure-templafy-saml2-for-automatic-user-provisioning"></a>Tutoriel : Configurer Templafy SAML2 pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Templafy SAML2 et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et/ou des groupes dans Templafy SAML2.

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
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Templafy SAML2](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="assigning-users-to-templafy-saml2"></a>Affectation d’utilisateurs à Templafy SAML2

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez déterminer quels sont les utilisateurs et/ou groupes dans Azure AD qui ont besoin d’accéder à Templafy SAML2. Une fois que vous avez fait votre choix, vous pouvez affecter ces utilisateurs et/ou groupes à Templafy SAML2 en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-saml2"></a>Conseils importants pour l’affectation d’utilisateurs à Templafy SAML2

* Il est recommandé d’affecter un seul utilisateur Azure AD à Templafy SAML2 pour tester la configuration du provisionnement automatique d’utilisateurs. Vous pouvez affecter plus tard d’autres utilisateurs et/ou groupes.

* Quand vous affectez un utilisateur à Templafy SAML2, vous devez sélectionner un rôle valide spécifique à l’application (le cas échéant) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="step-2-configure-templafy-saml2-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Templafy SAML2 pour prendre en charge le provisionnement avec Azure AD

Avant de configurer Templafy SAML2 pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez activer le provisionnement SCIM sur Templafy SAML2.

1. Connectez-vous à la Console d’administration Templafy. Cliquez sur **Administration**.

    ![Console d’administration Templafy](media/templafy-saml-2-provisioning-tutorial/templafy-admin.png)

2. Cliquez sur **Méthode d'authentification**.

    ![Capture d’écran de la section d’administration Templafy avec l’option Méthode d’authentification en évidence.](media/templafy-saml-2-provisioning-tutorial/templafy-auth.png)

3. Copiez la valeur de la **clé API SCIM**. Vous devez entrer cette valeur dans le champ **Jeton secret** sous l’onglet Provisionnement de votre application Templafy SAML2 dans le portail Azure.

    ![Capture d’écran de la clé API SCIM.](media/templafy-saml-2-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-saml2-from-the-gallery"></a>Étape 3. Ajouter Templafy SAML2 à partir de la galerie

Si vous souhaitez configurer Templafy SAML2 pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Templafy SAML2 à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Templafy SAML2 à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Templafy SAML2**, sélectionnez **Templafy SAML2** dans le panneau Résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Templafy SAML2 dans la liste des résultats](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-saml2"></a>Étape 4. Configurer le provisionnement automatique d’utilisateurs sur Templafy SAML2 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Templafy SAML2 en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Templafy en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Templafy](templafy-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-templafy-saml2-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Templafy SAML2 dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Templafy SAML2**.

    ![Lien Templafy SAML2 dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Capture d’écran des options Gérer avec l’option Provisionnement en évidence.](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Capture d’écran de la liste déroulante Mode de provisionnement avec l’option Automatique en évidence.](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://scim.templafy.com/scim` dans **URL de locataire**. Entrez la valeur de la **clé API SCIM** récupérée dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Templafy. Si la connexion échoue, vérifiez que votre compte Templafy dispose des autorisations d’administrateur, puis réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Templafy SAML2**.

    ![Mappages d’utilisateurs Templafy SAML2](media/templafy-saml-2-provisioning-tutorial/user-mapping.png)

9. Dans la section **Mappage d’attributs**, passez en revue les attributs utilisateur synchronisés entre Azure AD et Templafy SAML2. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans Templafy SAML2 pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

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

    ![Mappages de groupes Templafy SAML2](media/templafy-saml-2-provisioning-tutorial/group-mapping.png)

11. Dans la section **Mappage d’attributs**, passez en revue les attributs de groupe synchronisés entre Azure AD et Templafy SAML2. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les groupes dans Templafy SAML2 pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|Pris en charge pour le filtrage|
      |---|---|---|
      |displayName|String|&check;|
      |membres|Informations de référence|
      |externalId|String|      


12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Si vous souhaitez activer le service de provisionnement Azure AD pour Templafy SAML2, changez l’option **État de provisionnement** en lui affectant la valeur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes à provisionner dans Templafy SAML2 en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

    Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour superviser la progression et suivre les liens vers le rapport d’activité de provisionnement, qui décrit toutes les actions effectuées par le service de provisionnement Azure AD sur Templafy SAML2.

## <a name="step-5-monitor-your-deployment"></a>Étape 5. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

* Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
* Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
* Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
