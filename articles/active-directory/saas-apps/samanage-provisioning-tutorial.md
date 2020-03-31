---
title: 'Didacticiel : configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et déprovisionner automatiquement des comptes d’utilisateur sur Samanage.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060466"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Didacticiel : configurer Samanage pour l’approvisionnement automatique d’utilisateurs

Ce didacticiel montre comment procéder dans Samanage et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et des groupes sur Samanage.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’approvisionnement d’utilisateurs Azure AD. Pour plus d’informations sur l’objet et le fonctionnement de ce service et pour accéder au forum aux questions, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un locataire Azure AD.
* Un [locataire Samanage](https://www.samanage.com/pricing/) avec le package Professionnel.
* Un compte d’utilisateur dans Samanage avec des autorisations d’administration.

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD repose sur [l’API REST Samanage](https://www.samanage.com/api/). Cette API est disponible pour les développeurs Samanage disposant de comptes avec le package Professionnel.

## <a name="add-samanage-from-the-azure-marketplace"></a>Ajouter Samanage à partir de la Place de marché Azure

Avant de configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure AD, ajoutez Samanage à partir de la Place de marché Azure à votre liste d’applications SaaS managées.

Pour ajouter Samanage à partir de la Place de marché, procédez comme suit.

1. Dans le volet de navigation de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

    ![Icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Samanage**, puis, dans le panneau des résultats, sélectionnez **Samanage**. Pour ajouter l’application, sélectionnez **Ajouter**.

    ![Samanage dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Affecter des utilisateurs à Samanage

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs ou groupes affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, identifiez les utilisateurs ou groupes dans Azure AD qui doivent accéder à Samanage. Pour affecter ces utilisateurs ou groupes à Samanage, suivez les instructions dans [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Conseils importants pour l’affectation d’utilisateurs à Samanage

*    Les rôles Samanage sont désormais indiqués de manière dynamique et automatique dans l’interface utilisateur du Portail Azure. Avant d’affecter des rôles Samanage à des utilisateurs, assurez-vous qu’une synchronisation initiale est effectuée par rapport à Samanage afin de récupérer les rôles les plus récents dans votre locataire Samanage.

*    Nous vous recommandons d’affecter un seul utilisateur Azure AD à Samanage afin de tester votre configuration initiale de l’approvisionnement automatique d’utilisateurs. Vous pourrez affecter des groupes et utilisateurs supplémentaires par la suite, une fois les tests concluants.

*    Quand vous affectez un utilisateur à Samanage, sélectionnez un rôle valide spécifique de l’application, si disponible, dans la boîte de dialogue des affectations. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurer l’approvisionnement automatique d’utilisateurs sur Samanage

Cette section vous guide dans la procédure de configuration du service d’approvisionnement Azure AD. Celui-ci permet de créer, mettre à jour et désactiver des utilisateurs ou des groupes dans Samanage en fonction des affectations d’utilisateurs ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également activer l’authentification unique SAML pour Samanage. Suivez les instructions fournies dans le [didacticiel sur la l’authentification unique pour Samanage](samanage-tutorial.md). La configuration de l’authentification unique est indépendante de celle de l’approvisionnement automatique d’utilisateurs, même si ces deux fonctionnalités se complètent.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurer l’approvisionnement automatique d’utilisateurs pour Samanage dans Azure AD

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications** > **Samanage**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Samanage**.

    ![Lien Samanage dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’**URL du locataire** et le **Jeton secret** de votre compte Samanage. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Samanage. Si la connexion échoue, vérifiez que votre compte Samanage dispose d’autorisations d’administrateur et réessayez.

    ![Test de connexion à Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. Dans la zone **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe devant recevoir les notifications d’erreur d’approvisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification de Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Samanage**.

    ![Synchronisation d’utilisateur Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Samanage dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **Enregistrer**.

    ![Samanage : mise en correspondance des attributs utilisateur](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Pour activer les mappages de groupe, dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Samanage**.

    ![Synchronisation des groupes Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Définissez **Activé** sur **Oui** pour synchroniser les groupes. Passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Samanage dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **Enregistrer**.

    ![Samanage : mise en correspondance des attributs de groupes](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Pour configurer des filtres d’étendue, suivez les instructions fournies dans le [didacticiel sur les filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service d’attribution Azure AD pour Samanage, définissez **État d’attribution** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Définissez les utilisateurs ou groupes que vous souhaitez approvisionner dans Samanage. Dans la section **Paramètres**, sélectionnez les valeurs souhaitées sous **Étendue**. Lorsque vous sélectionnez l’option **Synchroniser l’ensemble des utilisateurs et groupes**, tenez compte des restrictions décrites dans la section Limitations du connecteur ci-dessous.

    ![Étendue de Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Quand vous êtes prêt à effectuer l’approvisionnement, sélectionnez **Enregistrer**.

    ![Enregistrement de Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis sous **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ, tant que le service d’approvisionnement Azure AD s’exécute. 

Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Ce rapport décrit toutes les actions effectuées par le service d’approvisionnement Azure AD sur Samanage.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

Si vous sélectionnez l’option **Synchroniser l’ensemble des utilisateurs et groupes** option et configurez une valeur pour l’attribut **roles** de Samanage, la valeur dans la zone **Valeur par défaut si Null (facultatif)** doit être exprimée dans le format suivant :

- {"displayName":"role"}, où rôle est la valeur par défaut souhaitée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
