---
title: 'Didacticiel : Configurer Tableau Online pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et annuler l’approvisionnement de comptes d’utilisateur à Tableau Online automatiquement.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123752"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Didacticiel : Configurer Tableau Online pour l’approvisionnement automatique d’utilisateurs

Ce didacticiel présente les étapes à effectuer dans Tableau Online et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et annuler le déploiement d’utilisateurs et groupes dans Tableau Online automatiquement.

> [!NOTE]
> Ce didacticiel décrit un connecteur qui repose sur l’utilisateur Azure AD service d’approvisionnement. Pour plus d’informations sur ce service, son fonctionnement et les questions fréquemment posées, consultez [automatiser l’approvisionnement et annulation d’approvisionnement pour les applications de software-as-a-service (SaaS) avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel suppose que vous avez :

*   Un locataire Azure AD.
*   Un [locataire Tableau Online](https://www.tableau.com/).
*   Un compte d’utilisateur dans Tableau Online avec les autorisations d’administrateur.

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur le [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Cette API est disponible pour les développeurs Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Ajouter Tableau Online à partir de la place de marché Azure
Avant de configurer Tableau Online pour l’approvisionnement avec Azure AD automatique d’utilisateurs, ajouter Tableau Online à partir de la place de marché Azure à votre liste d’applications SaaS gérées.

Pour ajouter Tableau Online à partir de la place de marché, procédez comme suit.

1. Dans le [Azure portal](https://portal.azure.com), dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![L’icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Tableau Online** et sélectionnez **Tableau Online** le volet de résultats. Pour ajouter l’application, sélectionnez **ajouter**.

    ![Tableau Online dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Affecter des utilisateurs à Tableau Online

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, uniquement les utilisateurs ou les groupes qui ont été affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à Tableau Online. Pour affecter ces utilisateurs ou groupes à Tableau Online, suivez les instructions de [affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Conseils importants concernant l’affectation d’utilisateurs à Tableau Online

*   Nous vous recommandons d’affecter un seul utilisateur Azure AD à Tableau Online pour tester la configuration du provisionnement automatique d’utilisateurs. Vous pouvez affecter des utilisateurs ou des groupes plus tard.

*   Lorsque vous affectez un utilisateur à Tableau Online, sélectionnez un rôle spécifique à l’application valide, s’il est disponible, dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurer l’approvisionnement automatique d’utilisateurs à Tableau Online

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD. Utilisez-le pour créer, mettre à jour et désactiver des utilisateurs ou groupes dans Tableau Online basé sur les affectations de groupe ou utilisateur dans Azure AD.

> [!TIP]
> Vous pouvez également activer basée sur SAML SSO pour Tableau Online. Suivez les instructions de la [didacticiel d’authentification unique Tableau Online](tableauonline-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que ces deux fonctionnalités complètent mutuellement.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurer l’approvisionnement automatique de Tableau Online dans Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **applications d’entreprise** > **toutes les applications** > **Tableau Online**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Tableau Online**.

    ![Le lien de Tableau Online dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Mode d’approvisionnement en ligne de tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Sous le **informations d’identification administrateur** section, entrée de domaine, nom d’utilisateur administrateur, mot de passe administrateur, les URL du contenu de votre compte Tableau Online :

   * Dans le **domaine** zone, renseignez le sous-domaine en fonction de l’étape 6.

   * Dans le **Admin Username** zone, renseignez le nom d’utilisateur du compte d’administrateur sur votre locataire Clarizen. Par exemple admin@contoso.com.

   * Dans le **mot de passe administrateur** zone, renseignez le mot de passe du compte d’administrateur qui correspond au nom d’utilisateur administrateur.

   * Dans le **URL de contenu** zone, renseignez le sous-domaine en fonction de l’étape 6.

6. Après vous être connecté à votre compte d’administration pour le Tableau Online, vous pouvez obtenir les valeurs des **domaine** et **URL de contenu** à partir de l’URL de la page d’administration.

    * Le **domaine** de votre compte Tableau Online peut être copié à partir de cette partie de l’URL :

        ![Domaine en ligne de tableau](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Le **URL de contenu** pour votre Tableau Online compte peut être copié à partir de cette section. C’est une valeur qui est définie pendant l’installation de compte. Dans cet exemple, la valeur est « contoso » :

        ![URL de contenu en ligne de tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Votre **domaine** peut être différent de celui indiqué ici.

7. Une fois que vous remplissez les zones affichées à l’étape 5, sélectionnez **tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Tableau Online. Si la connexion échoue, vérifiez que votre compte Tableau Online dispose des autorisations d’administrateur et réessayez.

    ![Connexion de Test en ligne de tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Dans le **E-mail de Notification** zone, entrez l’adresse e-mail de la personne ou le groupe pour recevoir les notifications d’erreur d’approvisionnement. Sélectionnez le **envoyer une notification par e-mail lorsqu’une défaillance se produit** case à cocher.

    ![E-mail de Notification en ligne de tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Sélectionnez **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Tableau Online**.

    ![Synchronisation de l’utilisateur en ligne de tableau](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Tableau Online dans le **des mappages d’attributs** section. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Tableau Online pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs utilisateur correspondants en ligne de tableau](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Tableau Online**.

    ![Synchronisation de groupe en ligne de tableau](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Passez en revue les attributs groupe qui sont synchronisés à partir d’Azure AD à Tableau Online dans le **des mappages d’attributs** section. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Tableau Online pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs de groupe correspondants en ligne de tableau](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Pour configurer des filtres d’étendue, suivez les instructions de la [didacticiel de filtre d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pour activer l’approvisionnement de service pour le Tableau Online, dans Azure AD le **paramètres** section, remplacez **état d’approvisionnement** à **sur**.

    ![État de configuration en ligne de tableau](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Définissez les utilisateurs ou les groupes auxquels vous souhaitez approvisionner à Tableau Online. Dans le **paramètres** , sélectionnez les valeurs souhaitées dans **étendue**.

    ![Étendue en ligne de tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Lorsque vous êtes prêt à déployer, sélectionnez **enregistrer**.

    ![Tableau en ligne d’enregistrement](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis dans **étendue** dans le **paramètres** section. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ tant que le service d’approvisionnement AD Azure s’exécute. 

Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Le rapport décrit toutes les actions effectuées par le service sur Tableau Online de provisionnement Azure AD.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
