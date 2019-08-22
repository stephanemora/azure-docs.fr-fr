---
title: 'Didacticiel : Configurer Insight4GRC pour l’approvisionnement d’utilisateurs automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et déprovisionner automatiquement des comptes d’utilisateur dans Insight4GRC.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7e8c6c2277f29fc114033aac24844e9e85816b78
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950758"
---
# <a name="tutorial-configure-insight4grc--for-automatic-user-provisioning"></a>Didacticiel : Configurer Insight4GRC pour l’approvisionnement utilisateur automatique

L'objectif de ce didacticiel est de présenter les étapes à suivre dans Insight4GRC et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l'approvisionnement et le déprovisionnement automatiques d'utilisateurs et/ou de groupes sur Insight4GRC.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un abonné Insight4GRC](https://www.rsmuk.com/).
* Un compte d’utilisateur Insight4GRC avec des autorisations d’administrateur.

## <a name="assigning-users-to-insight4grc"></a>Attribution d’utilisateurs à Insight4GRC 

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Insight4GRC. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à Insight4GRC en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-insight4grc"></a>Conseils importants pour l’attribution d’utilisateurs à Insight4GRC 

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Insight4GRC afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à Insight4GRC, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-insight4grc-for-provisioning"></a>Configurer Insight4GRC pour l’approvisionnement

Avant de configurer Insight4GRC pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM sur Insight4GRC.

1. Pour obtenir le jeton du porteur, le client final doit contacter [l’équipe du support technique](mailto:support.ss@rsmuk.com) et fournir le jeton du porteur aux clients.

2. Pour obtenir l’URL de point de terminaison SCIM, vous devez disposer de votre nom de domaine Insight4GRC, car il sera utilisé pour construire votre URL de point de terminaison SCIM. Vous pouvez récupérer votre nom de domaine Insight4GRC dans le cadre de l’achat initial de logiciels avec Insight4GRC.


## <a name="add-insight4grc--from-the-gallery"></a>Ajouter Insight4GRC à partir de la galerie

Pour configurer Insight4GRC pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Insight4GRC à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Insight4GRC à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, saisissez **Insight4GRC**, sélectionnez **Insight4GRC** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Insight4GRC dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-insight4grc"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Insight4GRC  

Cette section vous guide tout au long des étapes de configuration du service d'approvisionnement d'Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Insight4GRC en fonction des attributions d'utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Insight4GRC, en suivant les instructions fournies dans le [didacticiel sur l’authentification unique pour Insight4GRC](insight4grc-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc--in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Insight4GRC dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Insight4GRC**.

    ![Le lien Insight4GRC dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5.  Dans la section informations d’identification de l'administrateur, entrez `https://{Insight4GRC Domain Name}.insight4grc.com/public/api/scim/v2` dans **URL de l’abonné** à l’aide de la valeur {Insight4GRC Domain Name} récupérée précédemment. Saisissez la **valeur du Jeton** que vous avez préalablement récupérée dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Insight4GRC. Si la connexion échoue, vérifiez que votre compte Insight4GRC dispose des autorisations d’administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Insight4GRC**.

    ![Mappages d’utilisateurs Insight4GRC](media/insight4grc-provisioning-tutorial/usermapping.png)

9. Dans la section **Mappages des attributs**, consultez les attributs utilisateur qui sont synchronisés entre Azure AD et Insight4GRC. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Insight4GRC pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Mappages d’utilisateurs Insight4GRC](media/insight4grc-provisioning-tutorial/userattribute.png)

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Insight4GRC**

    ![Mappages de groupes Insight4GRC](media/insight4grc-provisioning-tutorial/groupmapping.png)

11. Dans la section **Mappages des attributs**, consultez les attributs de groupe qui sont synchronisés entre Azure AD et Insight4GRC. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes de groupe dans Insight4GRC pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Mappages de groupes Insight4GRC](media/insight4grc-provisioning-tutorial/groupattribute.png)

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour Insight4GRC, modifiez **Statut d’approvisionnement** par **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur Insight4GRC en choisissant les valeurs souhaitées dans le champ **Étendue** de la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement, qui décrit toutes les actions effectuées par le service d’approvisionnement Azure AD sur Insight4GRC.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md).