---
title: 'Didacticiel : Configurer Harness pour le provisionnement automatique des utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateurs sur Harness.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807663"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Didacticiel : Configurer Harness pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Harness et Azure Active Directory (Azure AD) pour configurer Azure AD afin de lui faire provisionner et déprovisionner automatiquement les utilisateurs et/ou les groupes sur Harness.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Harness](https://harness.io/pricing/)
* Un compte d’utilisateur Harness avec des autorisations d’administrateur.

## <a name="assigning-users-to-harness"></a>Affectation d’utilisateurs à Harness

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez identifier les utilisateurs et/ou les groupes dans Azure AD qui ont besoin d’accéder à Harness. Une fois que vous avez fait cela, vous pouvez affecter ces utilisateurs et/ou groupes à Harness en suivant les instructions indiquées ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Conseils importants pour l’affectation d’utilisateurs à Harness

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Harness de façon à tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous affectez un utilisateur à Harness, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="set-up-harness-for-provisioning"></a>Configurer Harness pour le provisionnement

1. Connectez-vous à votre [console d’administration Harness](https://app.harness.io/#/login). Accédez à **Continuous Security > Access Management** (Sécurité continue > Gestion d’accès).

    ![Console d’administration Harness](media/harness-provisioning-tutorial/admin.png)

2.  Cliquez sur **API Keys** (Clés d’API).

    ![Harness – Ajout de SCIM](media/harness-provisioning-tutorial/apikeys.png)

3. Cliquez sur **Add New Key** (Ajouter une nouvelle clé). Dans la boîte de dialogue **Add Api Key** (Ajouter une clé d’API), indiquez un nom dans le champ **Name** et sélectionnez une option dans le menu déroulant **Permissions Inherited from** (Autorisations héritées de). Cliquez sur le bouton **Submit** (Envoyer).

    ![Harness – Ajout d’une nouvelle clé](media/harness-provisioning-tutorial/addkey.png)

    ![Harness – Boîte de dialogue d’ajout de clé](media/harness-provisioning-tutorial/title.png)

3.  Copiez la valeur du champ **Key** (Clé). Cette valeur sera entrée dans le champ Secret Token (Jeton secret) de l’onglet Provisioning (Provisionnement) de votre application Harness sur le portail Azure.

    ![Harness - Créer un jeton](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Ajouter Harness à partir de la galerie

Avant de configurer Harness pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Harness à votre liste d’applications SaaS gérées à partir de la galerie d’applications Azure AD.

**Pour ajouter Harness à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Harness**, sélectionnez **Harness** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Harness dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Configuration du provisionnement automatique d’utilisateurs sur Harness 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Harness en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez aussi choisir d’activer l’authentification unique basée sur SAML pour Harness en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). La configuration de l’authentification unique est indépendante de la configuration du provisionnement automatique d’utilisateurs, même si ces deux fonctionnalités se complètent.

> [!NOTE]
> Pour en savoir plus sur le point de terminaison SCIM de Harness, consultez [ce document](https://docs.harness.io/article/smloyragsm-api-keys).

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Harness dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Harness**.

    ![Lien Harness dans la liste d’applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, saisissez `https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw` dans **URL du locataire**. Saisissez la valeur de **Jeton d’authentification SCIM** récupérée précédemment dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Harness. Si la connexion échoue, vérifiez que votre compte Harness dispose d’autorisations d’administrateur, puis réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Harness**.

    ![Mappages d’utilisateurs Harness](media/harness-provisioning-tutorial/usermappings.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Harness. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateurs dans Harness pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur Harness](media/harness-provisioning-tutorial/userattributes.png)

10. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Harness**.

    ![Mappages de groupes Harness](media/harness-provisioning-tutorial/groupmappings.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Harness. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Harness dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de groupe Harness](media/harness-provisioning-tutorial/groupattributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Harness, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou les groupes que vous souhaitez provisionner dans Harness en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour superviser la progression et suivre les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD dans Harness.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
