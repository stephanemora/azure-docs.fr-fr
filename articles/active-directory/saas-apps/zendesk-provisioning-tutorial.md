---
title: 'Didacticiel : Configurer Zendesk pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et annuler l’approvisionnement de comptes d’utilisateurs à Zendesk automatiquement.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114663"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Didacticiel : Configurer Zendesk pour le provisionnement automatique d’utilisateurs

Ce didacticiel présente les étapes à effectuer dans Zendesk et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et annuler le déploiement d’utilisateurs et groupes dans Zendesk automatiquement.

> [!NOTE]
> Ce didacticiel décrit un connecteur qui repose sur l’utilisateur Azure AD service d’approvisionnement. Pour plus d’informations sur ce service, son fonctionnement et les questions fréquemment posées, consultez [automatiser l’approvisionnement et annulation d’approvisionnement pour les applications de software-as-a-service (SaaS) avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel suppose que vous avez :

* Un locataire Azure AD.
* Un locataire Zendesk avec le [Enterprise](https://www.zendesk.com/product/pricing/) ou supérieur activé.
* Un compte d’utilisateur dans Zendesk avec des autorisations d’administrateur.

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur le [API Rest Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction). Cette API est disponible pour les équipes Zendesk sur le plan d’entreprise ou mieux.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Ajouter Zendesk à partir de la place de marché Azure

Avant de configurer Zendesk pour l’approvisionnement avec Azure AD automatique d’utilisateurs, ajouter Zendesk à partir de la place de marché Azure à votre liste d’applications SaaS gérées.

Pour ajouter Zendesk à partir de la place de marché, procédez comme suit.

1. Dans le [Azure portal](https://portal.azure.com), dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![L’icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Zendesk** et sélectionnez **Zendesk** le volet de résultats. Pour ajouter l’application, sélectionnez **ajouter**.

    ![Zendesk dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Affecter des utilisateurs à Zendesk

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, uniquement les utilisateurs ou les groupes qui ont été affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à Zendesk. Pour affecter ces utilisateurs ou groupes à Zendesk, suivez les instructions de [affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Conseils importants pour l’affectation d’utilisateurs à Zendesk

* Aujourd'hui, les rôles de Zendesk sont automatiquement et dynamiquement remplis dans l’interface utilisateur du portail Azure. Avant de vous attribuez des rôles de Zendesk pour les utilisateurs, assurez-vous que la synchronisation initiale est effectuée par rapport aux Zendesk pour récupérer les rôles les plus récentes dans votre locataire Zendesk.

* Nous vous recommandons d’affecter un seul utilisateur Azure AD à Zendesk pour tester votre initiale approvisionnement automatique d’utilisateurs configuration. Vous pouvez affecter des utilisateurs ou groupes ultérieurement une fois que les tests réussissent.

* Lorsque vous affectez un utilisateur à Zendesk, sélectionnez un rôle spécifique à l’application valide, s’il est disponible, dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurer l’approvisionnement automatique d’utilisateurs à Zendesk 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD. Utilisez-le pour créer, mettre à jour et désactiver des utilisateurs ou groupes dans Zendesk en fonction des affectations d’utilisateur ou un groupe dans Azure AD.

> [!TIP]
> Vous pouvez également activer basée sur SAML SSO pour Zendesk. Suivez les instructions de la [didacticiel l’authentification unique de le Zendesk](zendesk-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que ces deux fonctionnalités complètent mutuellement.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurer l’approvisionnement automatique d’utilisateurs pour Zendesk dans Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **applications d’entreprise** > **toutes les applications** > **Zendesk**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Zendesk**.

    ![Lien Zendesk dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Mode d’approvisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Sous le **informations d’identification administrateur** section, entrez le nom d’utilisateur administrateur, le jeton secret et le domaine de votre compte Zendesk. Voici des exemples de valeurs :

   * Dans le **Admin Username** zone, renseignez le nom d’utilisateur du compte d’administrateur sur votre locataire Zendesk. Par exemple admin@contoso.com.

   * Dans le **jeton Secret** zone, renseignez le jeton secret comme décrit à l’étape 6.

   * Dans le **domaine** zone, renseignez le sous-domaine de votre locataire Zendesk. Par exemple, pour un compte avec une URL de locataire `https://my-tenant.zendesk.com`, votre sous-domaine est **mon locataire**.

6. Le jeton secret pour votre compte Zendesk se trouve dans **administrateur** > **API** > **paramètres**. Assurez-vous que l’option **accès du jeton** a la valeur **activé**.

    ![Paramètres d’administration de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Jeton secret Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Une fois que vous remplissez les zones affichées à l’étape 5, sélectionnez **tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Zendesk. Si la connexion échoue, vérifiez que votre compte Zendesk dispose des autorisations d’administrateur et réessayez.

    ![Zendesk tester la connexion](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Dans le **E-mail de Notification** zone, entrez l’adresse e-mail de la personne ou le groupe pour recevoir les notifications d’erreur d’approvisionnement. Sélectionnez le **envoyer une notification par e-mail lorsqu’une défaillance se produit** case à cocher.

    ![E-mail de Notification de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Sélectionnez **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Zendesk**.

    ![Synchronisation de l’utilisateur de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Zendesk dans la **des mappages d’attributs** section. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Zendesk pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs d’utilisateur Zendesk correspondants](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Sous le **mappages** section, sélectionnez **synchroniser les groupes Azure Active Directory à Zendesk**.

    ![Synchronisation de groupe de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Passez en revue les attributs groupe qui sont synchronisés à partir d’Azure AD à Zendesk dans la **des mappages d’attributs** section. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Zendesk pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Attributs de groupe correspondants Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Pour configurer des filtres d’étendue, suivez les instructions de la [didacticiel de filtre d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pour activer l’approvisionnement de service pour Zendesk, dans Azure AD le **paramètres** section, remplacez **état d’approvisionnement** à **sur**.

    ![État d’approvisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Définir les utilisateurs ou les groupes auxquels vous souhaitez provisionner sur Zendesk. Dans le **paramètres** , sélectionnez les valeurs souhaitées dans **étendue**.

    ![Étendue de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Lorsque vous êtes prêt à déployer, sélectionnez **enregistrer**.

    ![Enregistrement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis dans **étendue** dans le **paramètres** section. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ tant que le service d’approvisionnement AD Azure s’exécute. 

Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Le rapport décrit toutes les actions effectuées par le service sur Zendesk de provisionnement Azure AD.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Zendesk prend en charge l’utilisation de groupes pour les utilisateurs avec **Agent** rôles uniquement. Pour plus d’informations, consultez le [Zendesk documentation](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Lorsqu’un rôle personnalisé est affecté à un utilisateur ou un groupe, l’utilisateur automatique Azure AD également le service d’approvisionnement attribue le rôle par défaut **Agent**. Seuls les Agents peuvent avoir un rôle personnalisé. Pour plus d’informations, consultez le [documentation d’API Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
