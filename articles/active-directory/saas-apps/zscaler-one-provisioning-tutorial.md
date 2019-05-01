---
title: 'Didacticiel : Configurer Zscaler One pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et annuler l’approvisionnement de comptes d’utilisateurs à Zscaler One automatiquement.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706595"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Didacticiel : Configurer Zscaler One pour l’approvisionnement automatique

Ce didacticiel présente les étapes à effectuer dans Zscaler One et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et annuler le déploiement d’utilisateurs et groupes à Zscaler One automatiquement.

> [!NOTE]
> Ce didacticiel décrit un connecteur qui repose sur l’utilisateur Azure AD service d’approvisionnement. Pour plus d’informations sur ce service, son fonctionnement et les questions fréquemment posées, consultez [automatiser l’approvisionnement et annulation d’approvisionnement pour les applications de software-as-a-service (SaaS) avec Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ce connecteur est actuellement disponible en version préliminaire. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales pour les fonctionnalités en version préliminaire, consultez [conditions d’utilisation supplémentaires des versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel suppose que vous avez :

* Un locataire Azure AD.
* Un locataire Zscaler One.
* Un compte d’utilisateur dans Zscaler One avec des autorisations d’administrateur.

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur l’API de SCIM Zscaler One. Cette API est disponible pour les développeurs pour les comptes avec le package d’entreprise Zscaler One.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Ajouter Zscaler One à partir de la place de marché Azure

Avant de configurer Zscaler One pour l’approvisionnement avec Azure AD automatique d’utilisateurs, ajouter Zscaler One à partir de la place de marché Azure à votre liste d’applications SaaS gérées.

Pour ajouter Zscaler One à partir de la place de marché, procédez comme suit.

1. Dans le [Azure portal](https://portal.azure.com), dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![L’icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Zscaler One** et sélectionnez **Zscaler One** le volet de résultats. Pour ajouter l’application, sélectionnez **ajouter**.

    ![Zscaler One dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Affecter des utilisateurs à Zscaler One

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, uniquement les utilisateurs ou les groupes qui ont été affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à Zscaler One. Pour affecter ces utilisateurs ou groupes à Zscaler One, suivez les instructions de [affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Conseils importants pour l’affectation d’utilisateurs à Zscaler One

* Nous vous recommandons d’affecter un seul utilisateur Azure AD à Zscaler One pour tester la configuration du provisionnement automatique d’utilisateurs. Vous pouvez affecter des utilisateurs ou des groupes plus tard.

* Lorsque vous affectez un utilisateur à Zscaler One, sélectionnez un rôle spécifique à l’application valide, s’il est disponible, dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurer l’approvisionnement automatique d’utilisateurs à Zscaler One

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD. Utilisez-le pour créer, mettre à jour et désactiver des utilisateurs ou groupes dans Zscaler One basé sur les affectations de groupe ou utilisateur dans Azure AD.

> [!TIP]
> Vous pouvez également activer basée sur SAML SSO pour Zscaler One. Suivez les instructions de la [Zscaler One unique authentification didacticiel](zscaler-One-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que ces deux fonctionnalités complètent mutuellement.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurer l’approvisionnement automatique pour Zscaler One dans Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **applications d’entreprise** > **toutes les applications** > **Zscaler One**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Zscaler One**.

    ![Le lien Zscaler One dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Configuration de Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Un Mode d’approvisionnement Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sous le **informations d’identification administrateur** section, renseignez le **URL de locataire** et **jeton Secret** cases avec les paramètres pour votre Zscaler One compte comme décrit à l’étape 6.

6. Pour obtenir le locataire URL et le jeton secret, accédez à **Administration** > **paramètres d’authentification** dans l’interface utilisateur du portail Zscaler One. Sous **Type d’authentification**, sélectionnez **SAML**.

    ![Paramètres d’authentification Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Sélectionnez **configurer SAML** pour ouvrir le **configurer SAML** options.

    ![Zscaler One configurer SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Sélectionnez **Enable SCIM-Based approvisionnement** pour obtenir les paramètres dans **une URL de Base** et **le jeton du porteur**. Puis enregistrez les paramètres. Copie le **une URL de Base** à **URL de locataire** dans le portail Azure. Copie le **le jeton du porteur** à **jeton Secret** dans le portail Azure.

7. Une fois que vous remplissez les zones affichées à l’étape 5, sélectionnez **tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Zscaler One. Si la connexion échoue, vérifiez que votre compte Zscaler One dispose des autorisations d’administrateur et réessayez.

    ![Connexion d’un Test Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Dans le **E-mail de Notification** zone, entrez l’adresse e-mail de la personne ou le groupe pour recevoir les notifications d’erreur d’approvisionnement. Sélectionnez le **envoyer une notification par e-mail lorsqu’une défaillance se produit** case à cocher.

    ![E-mail de Notification de Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Sélectionnez **Enregistrer**.

10. Sous le **mappages** section, sélectionnez **synchroniser les utilisateurs Azure Active Directory à Zscaler One**.

    ![Synchronisation de l’utilisateur Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Zscaler One dans le **des mappages d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les comptes d’utilisateur dans Zscaler One pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Zscaler One attributs utilisateur correspondants](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Sous le **mappages** section, sélectionnez **synchroniser les groupes Azure Active Directory à Zscaler One**.

    ![Synchronisation des groupes de Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Passez en revue les attributs groupe qui sont synchronisés à partir d’Azure AD à Zscaler One dans le **des mappages d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les groupes dans Zscaler One pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs de groupe correspondants Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Pour configurer des filtres d’étendue, suivez les instructions de la [didacticiel de filtre d’étendue](./../active-directory-saas-scoping-filters.md).

15. Pour activer l’approvisionnement de service pour Zscaler One, dans Azure AD le **paramètres** section, remplacez **état d’approvisionnement** à **sur**.

    ![Un état d’approvisionnement Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Définissez les utilisateurs ou les groupes auxquels vous souhaitez approvisionner dans Zscaler One. Dans le **paramètres** , sélectionnez les valeurs souhaitées dans **étendue**.

    ![Étendue de Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Lorsque vous êtes prêt à déployer, sélectionnez **enregistrer**.

    ![Zscaler un enregistrement](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis dans **étendue** dans le **paramètres** section. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ tant que le service d’approvisionnement AD Azure s’exécute. 

Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Le rapport décrit toutes les actions effectuées par le service sur Zscaler One de provisionnement Azure AD.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
