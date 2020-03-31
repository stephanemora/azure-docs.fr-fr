---
title: 'Tutoriel : Configurer Zscaler pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et retirer automatiquement des comptes d’utilisateur sur Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062708"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Tutoriel : Configurer Zscaler pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Zscaler et Azure Active Directory (Azure AD) afin de configurer Azure AD pour le provisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Zscaler.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un locataire Azure AD.
* Un locataire Zscaler.
* Un compte d’utilisateur Zscaler ayant des autorisations d’administrateur.

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur l’API SCIM Zscaler, qui est disponible pour les développeurs Zscaler disposant de comptes pourvus du package Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Ajouter Zscaler à partir de la galerie

Avant de configurer Zscaler pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Zscaler à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Zscaler à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Zscaler**, sélectionnez **Zscaler** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Zscaler dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Affectation d’utilisateurs à Zscaler

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Zscaler. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Zscaler en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Conseils importants pour l’attribution d’utilisateurs à Zscaler

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Zscaler afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Zscaler, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configuration du provisionnement automatique d’utilisateurs sur Zscaler

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Zscaler en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Zscaler en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Zscaler](zscaler-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Zscaler dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Zscaler**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Zscaler**.

    ![Lien Zscaler dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Dans la section **Admin Credentials** (Informations d’identification d’administrateur), entrez les valeurs **Tenant URL** (URL de locataire) et **Secret Token** (Jeton secret) de votre compte Zscaler, comme cela est décrit dans l’étape 6.

6. Pour obtenir les valeurs **Tenant URL** (URL de locataire) et **Secret Token** (Jeton secret), accédez à **Administration > Authentication Settings** (Paramètres d’authentification) dans l’interface utilisateur du portail Zscaler, puis sélectionnez **SAML** sous **Authentication Type** (Type d’authentification).

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Cliquez sur **Configurer SAML** pour ouvrir les options de **Configuration SAML**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Sélectionnez **Enable SCIM-Based Provisioning** (Activer le provisionnement SCIM) pour copier les valeurs **Base URL** (URL de base) et **Bearer Token** (Jeton du porteur), puis enregistrez les paramètres. Copiez **Base URL** (URL de base) dans **Tenant URL** (URL de locataire) et **Bearer Token** (Jeton du porteur) sur **Secret Token** (Jeton secret) dans le portail Azure.

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Zscaler. Si la connexion échoue, vérifiez que votre compte Zscaler dispose des autorisations d’administrateur et réessayez.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/notification.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to Zscaler** (Synchroniser les utilisateurs Azure Active Directory avec Zscaler).

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Zscaler. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des comptes d’utilisateur dans Zscaler dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Groups to Zscaler** (Synchroniser les groupes Azure Active Directory avec Zscaler).

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Dans la section **Mappages des attributs**, passez en revue les attributs groupe qui sont synchronisés entre Azure AD et Zscaler. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Zscaler dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](./../active-directory-saas-scoping-filters.md).

15. Pour activer le service de provisionnement Azure AD pour Zscaler, définissez le paramètre **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner sur Zscaler en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/scoping.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement de Zscaler](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur Zscaler.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
