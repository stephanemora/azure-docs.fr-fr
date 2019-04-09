---
title: 'Didacticiel : Configurer MerchLogix pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur à MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263591"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Didacticiel : Configurer MerchLogix pour l’approvisionnement automatique

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans MerchLogix et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner automatiquement et retirer les utilisateurs et/ou groupes à MerchLogix.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* Un locataire MerchLogix
* Un contact technique de MerchLogix qui peut fournir l’URL de point de terminaison SCIM et le jeton secret requis pour l’approvisionnement

## <a name="adding-merchlogix-from-the-gallery"></a>Ajout de MerchLogix à partir de la galerie

Avant de configurer MerchLogix pour l’approvisionnement avec Azure AD automatique d’utilisateurs, vous devez ajouter MerchLogix à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter MerchLogix à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]

3. Pour ajouter MerchLogix, cliquez sur le **nouvelle application** bouton en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **MerchLogix**.

5. Dans le volet de résultats, sélectionnez **MerchLogix**, puis cliquez sur le **ajouter** pour ajouter MerchLogix à votre liste d’applications SaaS.

    ![Configuration de MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Affectation d’utilisateurs à MerchLogix

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à MerchLogix. Une fois choisi, vous pouvez affecter ces utilisateurs et/ou groupes à MerchLogix en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Conseils importants pour l’affectation d’utilisateurs à MerchLogix

* Il est recommandé qu’un seul utilisateur Azure AD est affecté à MerchLogix pour tester votre initiale approvisionnement automatique d’utilisateurs configuration. Les autres utilisateurs et/ou groupes peuvent être assignés ultérieurement, une fois les tests réussis.

* Quand vous assignez un utilisateur à MerchLogix, vous devez sélectionner un rôle spécifique à l’application valide (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configuration de l’approvisionnement automatique d’utilisateurs à MerchLogix 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement de AD Azure pour créer, mettre à jour et désactiver des utilisateurs et/ou groupes dans MerchLogix en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML SSO pour MerchLogix en suivant les instructions fournies dans le [didacticiel l’authentification unique de le MerchLogix](merchlogix-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Pour configurer l’approvisionnement automatique pour MerchLogix dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez MerchLogix à partir de votre liste d’applications SaaS.

3. Sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Configuration de MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Sous le **informations d’identification administrateur** section :

    * Dans le **URL de locataire** , entrez l’URL de point de terminaison SCIM fournie par votre contact technique MerchLogix.

    * Dans le **jeton Secret** , entrez le jeton secret fourni par votre contact technique MerchLogix.

6. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **tester la connexion** pour vérifier qu’Azure AD peut se connecter à MerchLogix. Si la connexion échoue, vérifiez que votre compte de MerchLogix dispose des autorisations d’administrateur et réessayez.

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

8. Cliquez sur **Enregistrer**.

9. Sous le **mappages** section, sélectionnez **synchroniser les utilisateurs Azure Active Directory avec MerchLogix**.

10. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à MerchLogix dans la **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les comptes d’utilisateur dans MerchLogix pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

11. Sous le **mappages** section, sélectionnez **synchroniser les groupes Azure Active Directory avec MerchLogix**.

12. Passez en revue les attributs groupe qui sont synchronisés à partir d’Azure AD à MerchLogix dans la **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les groupes dans MerchLogix pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

13. Pour activer l’approvisionnement de service pour MerchLogix Azure AD, modifiez le **état d’approvisionnement** à **sur** dans le **paramètres** section.

14. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers des rapports d’activité, qui décrit toutes les actions effectuées par le service sur MerchLogix de provisionnement Azure AD d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
