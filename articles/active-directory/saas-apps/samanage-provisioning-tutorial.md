---
title: 'Didacticiel : Configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et annuler l’approvisionnement de comptes d’utilisateur à Samanage automatiquement.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d474d9bfd6016885eaa21afcea5d44d39c624084
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104627"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Didacticiel : Configurer Samanage pour l’approvisionnement automatique d’utilisateurs

Ce didacticiel présente les étapes à effectuer dans Samanage et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et annuler le déploiement d’utilisateurs et groupes dans Samanage automatiquement.

> [!NOTE]
> Ce didacticiel décrit un connecteur qui repose sur l’utilisateur Azure AD service d’approvisionnement. Pour plus d’informations sur ce service, son fonctionnement et les questions fréquemment posées, consultez [automatiser l’approvisionnement et annulation d’approvisionnement pour les applications de software-as-a-service (SaaS) avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel suppose que vous avez :

* Un locataire Azure AD.
* Un [locataire Samanage](https://www.samanage.com/pricing/) avec le package Professionnel.
* Un compte d’utilisateur dans Samanage avec des autorisations d’administrateur.

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur le [API Rest Samanage](https://www.samanage.com/api/). Cette API est disponible pour les développeurs de Samanage pour les comptes avec le package Professionnel.

## <a name="add-samanage-from-the-azure-marketplace"></a>Ajouter Samanage à partir de la place de marché Azure

Avant de configurer Samanage pour l’approvisionnement avec Azure AD automatique d’utilisateurs, ajouter Samanage à partir de la place de marché Azure à votre liste d’applications SaaS gérées.

Pour ajouter Samanage à partir de la place de marché, procédez comme suit.

1. Dans le [Azure portal](https://portal.azure.com), dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![L’icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Samanage** et sélectionnez **Samanage** le volet de résultats. Pour ajouter l’application, sélectionnez **ajouter**.

    ![Samanage dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Affecter des utilisateurs à Samanage

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, uniquement les utilisateurs ou les groupes qui ont été affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à Samanage. Pour affecter ces utilisateurs ou groupes à Samanage, suivez les instructions de [affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Conseils importants pour l’affectation d’utilisateurs à Samanage

*    Aujourd'hui, les rôles de Samanage sont automatiquement et dynamiquement remplis dans l’interface utilisateur du portail Azure. Avant de vous attribuez des rôles de Samanage pour les utilisateurs, assurez-vous que la synchronisation initiale est effectuée par rapport aux Samanage pour récupérer les rôles plus récentes dans votre locataire Samanage.

*    Nous vous recommandons d’affecter un seul utilisateur Azure AD à Samanage pour tester votre initiale approvisionnement automatique d’utilisateurs configuration. Vous pouvez affecter les groupes et des utilisateurs supplémentaires ultérieurement une fois que les tests réussissent.

*    Lorsque vous affectez un utilisateur à Samanage, sélectionnez un rôle spécifique à l’application valide, s’il est disponible, dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurer l’approvisionnement automatique d’utilisateurs à Samanage

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD. Utilisez-le pour créer, mettre à jour et désactiver des utilisateurs ou groupes dans Samanage en fonction des affectations d’utilisateur ou un groupe dans Azure AD.

> [!TIP]
> Vous pouvez également activer basée sur SAML SSO pour Samanage. Suivez les instructions de la [didacticiel l’authentification unique de le Samanage](samanage-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que ces deux fonctionnalités complètent mutuellement.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurer l’approvisionnement automatique d’utilisateurs pour Samanage dans Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **toutes les applications** > **Samanage**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Samanage**.

    ![Le lien de Samanage dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Mode d’approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Sous le **informations d’identification administrateur** section, entrez le nom d’utilisateur administrateur et le mot de passe administrateur de votre compte Samanage. Voici des exemples de valeurs :

   * Dans le **Admin Username** zone, renseignez le nom d’utilisateur du compte d’administrateur sur votre abonne samanage. Par exemple admin@contoso.com.

   * Dans le **mot de passe administrateur** zone, renseignez le mot de passe du compte d’administrateur qui correspond au nom d’utilisateur administrateur.

6. Une fois que vous remplissez les zones affichées à l’étape 5, sélectionnez **tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Samanage. Si la connexion échoue, assurez-vous que votre compte Samanage dispose des autorisations d’administrateur et réessayez.

    ![Samanage tester la connexion](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Dans le **E-mail de Notification** zone, entrez l’adresse e-mail de la personne ou le groupe pour recevoir les notifications d’erreur d’approvisionnement. Sélectionnez le **envoyer une notification par e-mail lorsqu’une défaillance se produit** case à cocher.

    ![E-mail de Notification de Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Sélectionnez **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Samanage**.

    ![Synchronisation de l’utilisateur Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Samanage dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs d’utilisateur correspondants Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Pour activer les mappages de groupe, dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Samanage**.

    ![Synchronisation de groupe de Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Définissez **Activé** sur **Oui** pour synchroniser les groupes. Passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Samanage dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs de groupe correspondants Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Pour configurer des filtres d’étendue, suivez les instructions de la [didacticiel de filtre d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pour activer l’approvisionnement de service pour Samanage, dans Azure AD le **paramètres** section, remplacez **état d’approvisionnement** à **sur**.

    ![État d’approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Définissez les utilisateurs ou les groupes auxquels vous souhaitez approvisionner sur Samanage. Dans le **paramètres** , sélectionnez les valeurs souhaitées dans **étendue**. Lorsque vous sélectionnez le **synchroniser tous les utilisateurs et groupes** option, tenez compte des restrictions comme décrit dans la section suivante « Limitations de connecteur ».

    ![Étendue de Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Lorsque vous êtes prêt à déployer, sélectionnez **enregistrer**.

    ![Enregistrement de Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis dans **étendue** dans le **paramètres** section. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ tant que le service d’approvisionnement AD Azure s’exécute. 

Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Le rapport décrit toutes les actions effectuées par le service sur Samanage de provisionnement Azure AD.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

Si vous sélectionnez le **synchroniser tous les utilisateurs et groupes** option et une valeur pour le Samanage **rôles** d’attribut, la valeur sous la **valeur par défaut si null (est facultatif)** boîte doit être exprimée dans le format suivant :

- {« displayName » : « rôle »}, où le rôle est la valeur par défaut souhaitée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
