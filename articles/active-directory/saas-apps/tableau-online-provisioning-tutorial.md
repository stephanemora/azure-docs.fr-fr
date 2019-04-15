---
title: 'Didacticiel : Configurer Tableau Online pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur sur Tableau Online.
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
ms.openlocfilehash: f732eebd410a6b52a21a46925a29bf4676f7c8cb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270784"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Didacticiel : Configurer Tableau Online pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Tableau Online et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le déprovisionnement automatiques d’utilisateurs et/ou de groupes sur Tableau Online.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   un locataire Azure AD ;
*   Un [locataire Tableau Online](https://www.tableau.com/)
*   Un compte d’utilisateur dans Tableau Online doté d’autorisations d’administrateur

> [!NOTE]
> L’intégration du provisionnement Azure AD repose sur [l’API REST Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), qui est disponible pour les développeurs Tableau Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Ajout de Tableau Online à partir de la galerie
Avant de configurer Tableau Online pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Tableau Online à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Tableau Online à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Tableau Online**, sélectionnez **Tableau Online** dans le volet de résultats puis cliquez sur **ajouter** pour ajouter l’application.

    ![En ligne de tableau dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-tableau-online"></a>Affectation d’utilisateurs à Tableau Online

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes Azure AD ont besoin d’accéder à Tableau Online. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à Tableau Online en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Conseils importants concernant l’affectation d’utilisateurs à Tableau Online

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à Tableau Online afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Lorsque vous assignez un utilisateur à Tableau Online, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Configuration de l’approvisionnement automatique d’utilisateurs dans Tableau Online

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Tableau Online en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Tableau Online en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Tableau Online](tableauonline-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Tableau Online dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Tableau Online**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Tableau Online**.

    ![Le lien de Tableau Online dans la liste des Applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Domaine**, le **Nom d’utilisateur de l’administrateur**, le **Mot de passe d’administrateur** et l’**URL du contenu** de votre compte Tableau Online :

   * Dans le champs **Domaine**, indiquez le sous-domaine en fonction de l’étape 6.

   * Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Clarizen. Exemple : admin@contoso.com.

   * Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe du compte administrateur correspondant au nom de l’utilisateur administrateur.

   * Dans le champs **URL du contenu**, indiquez le sous-domaine en fonction de l’étape 6.

6. Lorsque vous êtes connecté à votre compte d’administration de Tableau Online, vous pouvez extraire les valeurs **Domaine** et **URL du contenu** à partir de l’URL de la page d’administration.

    * Le **domaine** de votre compte Tableau Online peut être copié à partir de cette partie de l’URL :

        ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * L’**URL du contenu** de votre compte Tableau Online peut être copié à partir de cette section, et est une valeur définie pendant la configuration du compte. Dans cet exemple, la valeur est « contoso » :

        ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Votre **domaine** peut être différente de celui illustré ici.

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Tableau Online. Si la connexion échoue, vérifiez que votre compte Tableau Online dispose d’autorisations d’administrateur et réessayez.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Tableau Online**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateurs qui sont synchronisés entre Azure AD et Tableau Online. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Tableau Online pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Tableau Online**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Dans la section **Mappages des attributs**, passez en revue les attributs de groupes qui sont synchronisés entre Azure AD et Tableau Online. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Tableau Online pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pour activer le service d’approvisionnement Azure AD pour Tableau Online, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Tableau Online en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement de Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Tableau Online.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
