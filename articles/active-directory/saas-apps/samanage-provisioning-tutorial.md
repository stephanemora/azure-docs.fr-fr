---
title: 'Didacticiel : Configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Samanage.
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
ms.openlocfilehash: ca43b62e66e3a736aa52fdd10fe36e635daba245
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280347"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Didacticiel : Configurer Samanage pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Samanage et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le déprovisionnement automatiques d’utilisateurs et/ou de groupes sur Samanage.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* un locataire Azure AD ;
* Un [locataire Samanage](https://www.samanage.com/pricing/) avec le package Professionnel
* Un compte d’utilisateur dans Samanage avec des autorisations d’administrateur

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur l’[API REST Samanage](https://www.samanage.com/api/), qui est disponible pour les développeurs Samanage disposant de comptes pourvus du package Professionnel.

## <a name="adding-samanage-from-the-gallery"></a>Ajout de Samanage à partir de la galerie

Avant de configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Samanage à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Samanage à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Samanage**, sélectionnez **Samanage** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Samanage dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-samanage"></a>Affectation d’utilisateurs à Samanage

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Samanage. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à Samanage en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Conseils importants pour l’affectation d’utilisateurs à Samanage

*    Les rôles Samanage sont désormais indiqués de manière dynamique et automatique dans l’interface utilisateur du Portail Azure. Avant d’assigner des rôles Samanage aux utilisateurs, assurez-vous que la synchronisation initiale est effectuée par rapport à Samanage afin de récupérer les rôles les plus récents dans votre tenant Samanage.

*    Il est recommandé d’assigner un seul utilisateur Azure AD à Samanage afin de tester la configuration initiale du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être assignés ultérieurement, une fois les tests réussis.

*   Quand vous assignez un utilisateur à Samanage, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Samanage

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Samanage en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Samanage en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Samanage](samanage-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Samanage dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Samanage**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Samanage**.

    ![Lien Samanage dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d'utilisateur de l'administrateur** et le **Mot de passe d'administrateur** de votre compte Samanage. Voici des exemples de valeurs :

   * Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Samanage. Exemple : admin@contoso.com.

   * Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe du compte administrateur correspondant au nom de l’utilisateur administrateur.

6. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Samanage. Si la connexion échoue, vérifiez que votre compte Samanage dispose d’autorisations d’administrateur et réessayez.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Samanage**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Samanage dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Pour activer les mappages de groupe, dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Samanage**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Définissez **Activé** sur **Oui** pour synchroniser les groupes. Passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Samanage dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pour activer le service d’approvisionnement Azure AD pour Samanage, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Samanage en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**. Lorsque vous choisissez l’option **Synchroniser l'ensemble des utilisateurs et groupes**, tenez compte des restrictions, comme décrit dans la section **Limitations du connecteur** ci-dessous.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement de Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour analyser la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrit toutes les actions effectuées par le service d’approvisionnement Azure AD sur Samanage.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Si l’option **Synchroniser l'ensemble des utilisateurs et groupes** est sélectionnée et que la valeur par défaut est configurée pour l’attribut **roles** Samanage, vérifiez que la valeur souhaitée dans le champ **Default value if null (is optional)** est exprimée au format suivant **{"displayName":"role"}** où le rôle est la valeur par défaut souhaitée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
