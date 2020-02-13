---
title: 'Tutoriel : Configurer Snowflake pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063144"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutoriel : Configurer Snowflake pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Snowflake et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le déprovisionnement automatiques d’utilisateurs et/ou de groupes sur Snowflake.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire Snowflake](https://www.Snowflake.com/pricing/).
* Un compte d’utilisateur dans Snowflake avec des autorisations d’administrateur.

## <a name="assigning-users-to-snowflake"></a>Affectation d’utilisateurs à Snowflake

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Snowflake. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à Snowflake en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Conseils importants pour l’affectation d’utilisateurs à Snowflake

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Snowflake afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Snowflake, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-snowflake-for-provisioning"></a>Configurer Snowflake pour l’approvisionnement

Avant de configurer Snowflake pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez activer le provisionnement SCIM sur Snowflake.

1. Connectez-vous à votre Console d’administration Snowflake. Entrez la requête illustrée ci-dessous dans la feuille de calcul en surbrillance, puis cliquez sur **Exécuter**.

    ![Console d’administrateur Snowflake](media/Snowflake-provisioning-tutorial/image00.png)

2.  Un jeton d’accès SCIM sera généré pour votre locataire Snowflake. Pour le récupérer, cliquez sur le lien en surbrillance ci-dessous.

    ![Ajout SCIM Snowflake](media/Snowflake-provisioning-tutorial/image01.png)

3. Copiez la valeur du jeton généré, puis cliquez sur **Terminé**. Cette valeur devra être entrée dans le champ **Jeton secret** dans l’onglet Approvisionnement de votre application Snowflake dans le portail Azure.

    ![Ajout SCIM Snowflake](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Ajouter Snowflake depuis la galerie

Avant de configurer Snowflake pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Snowflake à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Snowflake à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Snowflake**, sélectionnez **Snowflake** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Snowflake dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Snowflake 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Snowflake en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Snowflake en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Snowflake](Snowflake-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Snowflake dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Snowflake**.

    ![Lien Snowflake dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section Informations d’identification de l’administrateur, entrez `https://<Snowflake Account URL>/scim/v2` dans URL de locataire. Voici un exemple d'URL du locataire : `https://acme.snowflakecomputing.com/scim/v2`

6. Saisissez la valeur de **Jeton d’authentification SCIM** récupérée précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Snowflake. Si la connexion échoue, vérifiez que votre compte Snowflake dispose d’autorisations d’administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Snowflake**.

    ![Mappages d’utilisateurs Snowflake](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateurs qui sont synchronisés entre Azure AD et Snowflake. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Snowflake pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de l’utilisateur Snowflake](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Snowflake**.

    ![Mappages de groupes Snowflake](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Dans la section **Mappages des attributs**, passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Snowflake. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Snowflake pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de groupe Snowflake](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pour activer le service d’approvisionnement Azure AD pour Snowflake, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

15. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Snowflake en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**. Si cette option n’est pas disponible, configurez les champs nécessaires sous Informations d’identification de l’administrateur, cliquez sur **Enregistrer**, puis actualisez la page. 

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

16. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

    Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour analyser la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrit toutes les actions effectuées par le service d’approvisionnement Azure AD sur Snowflake.

    Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitations du connecteur

* Les jetons SCIM générés par Snowflake expirent dans 6 mois. N’oubliez pas que ces derniers doivent être actualisés avant leur expiration pour permettre aux synchronisations de provisionnement de continuer de fonctionner. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md).
