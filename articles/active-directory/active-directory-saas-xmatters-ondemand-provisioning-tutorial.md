---
title: 'Tutoriel : Configurer xMatters OnDemand pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateur sur xMatters OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 52fe23f5353a68e3afbac6a91955a61c7b8fb44c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292668"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Tutoriel : Configurer xMatters OnDemand pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans xMatters OnDemand et Azure AD (Azure Active Directory) afin de configurer Azure AD pour le provisionnement et le déprovisionnement automatiques d’utilisateurs et/ou de groupes sur xMatters OnDemand.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   un locataire Azure AD ;
*   Un locataire xMatters OnDemand avec le plan [Starter](https://www.xmatters.com/pricing) ou mieux activé 
*   Un compte d’utilisateur dans xMatters OnDemand avec des autorisations d’administrateur

> [!NOTE]
> L’intégration du provisionnement d’utilisateurs Azure AD repose sur [l’API xMatters OnDemand](https://help.xmatters.com/xmAPI), qui est mise à la disposition des équipes xMatters OnDemand qui relèvent au moins du plan Starter.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Ajout de xMatters OnDemand à partir de la galerie

Avant de configurer xMatters OnDemand pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter xMatters OnDemand à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter xMatters OnDemand à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]
    
3. Pour ajouter xMatters OnDemand, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **xMatters OnDemand**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. Dans le volet de résultats, sélectionnez **xMatters OnDemand**, puis cliquez sur le bouton **Ajouter** pour ajouter xMatters OnDemand à votre liste d’applications SaaS.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Affectation d’utilisateurs à xMatters OnDemand

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à xMatters OnDemand. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs et/ou groupes à votre application xMatters OnDemand en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Conseils importants pour l’affectation d’utilisateurs à xMatters OnDemand

*   Nous vous recommandons de n’affecter qu’un seul utilisateur Azure AD à xMatters OnDemand afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous affectez un utilisateur à xMatters OnDemand, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Configuration du provisionnement automatique d’utilisateurs sur xMatters OnDemand 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans xMatters OnDemand en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour xMatters OnDemand en suivant les instructions fournies dans le [tutoriel sur l’authentification unique xMatters OnDemand](active-directory-saas-xmatters-ondemand-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour xMatters OnDemand dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez xMatters OnDemand dans la liste de vos applications SaaS.
 
    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Sélectionnez l’onglet **Approvisionnement**.
    
    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d’utilisateur de l’administrateur**, le **Mot de passe d’administrateur** et le **Domaine** de votre compte xMatters OnDemand.

    *   Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire xMatters OnDemand. Exemple : admin@contoso.com.

    *   Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe correspondant au nom de l’utilisateur administrateur.

    *   Dans le champ **Domaine**, spécifiez le sous-domaine de votre locataire xMatters OnDemand.
    Exemple : pour un compte avec une URL de locataire https://my-tenant.xmatters.com, votre sous-domaine serait **my-tenant**.

6. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à xMatters OnDemand. Si la connexion échoue, vérifiez que votre compte xMatters OnDemand dispose des autorisations d’administrateur et réessayez.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur de provisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec xMatters OnDemand**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers xMatters OnDemand dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans xMatters OnDemand pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec xMatters OnDemand**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Passez en revue les attributs de groupes qui sont synchronisés d’Azure AD vers xMatters OnDemand dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans xMatters OnDemand pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](./active-directory-saas-scoping-filters.md).

14. Pour activer le service de provisionnement Azure AD pour xMatters OnDemand, affectez la valeur **Activé** au paramètre **État de l’approvisionnement** dans la section **Paramètres**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Définissez les utilisateurs et/ou groupes que vous voulez provisionner sur xMatters OnDemand en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Provisionnement xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur xMatters OnDemand.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
