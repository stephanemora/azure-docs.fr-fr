---
title: 'Didacticiel : Configurer Dynamic Signal pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur à Dynamic Signal.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: zchia
ms.openlocfilehash: fec6a7e3433eb5d657deac8c1b2ceb327f8d32e4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159404"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Didacticiel : Configurer Dynamic Signal pour l’approvisionnement automatique

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Dynamic Signal et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et retirer automatiquement les utilisateurs et/ou groupes à Dynamic Signal.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales pour les fonctionnalités en version préliminaire, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Dynamic Signal](https://dynamicsignal.com/)
* Un compte d’utilisateur dans Dynamic Signal avec des autorisations d’administrateur.

## <a name="add-dynamic-signal-from-the-gallery"></a>Ajouter Dynamic Signal dans la galerie

Avant de configurer Dynamic Signal pour l’approvisionnement avec Azure AD automatique d’utilisateurs, vous devez ajouter Dynamic Signal à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Dynamic Signal à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le  **[Azure portal](https://portal.azure.com)**, dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, sélectionnez le **nouvelle application** bouton en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Dynamic Signal**, sélectionnez **Dynamic Signal** dans le volet de résultats, puis cliquez sur le **ajouter** pour ajouter l’application.

    ![Dynamic Signal dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Affectation d’utilisateurs à Dynamic Signal

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, seuls les utilisateurs et/ou les groupes qui ont été assignés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Dynamic Signal. Une fois choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Dynamic Signal, en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Conseils importants pour l’affectation d’utilisateurs à Dynamic Signal

* Il est recommandé qu’un seul utilisateur Azure AD est affecté à Dynamic Signal à tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Dynamic Signal, vous devez sélectionner un rôle spécifique à l’application valide (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Configuration de l’approvisionnement automatique d’utilisateurs à Dynamic Signal 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement de AD Azure pour créer, mettre à jour et désactiver des utilisateurs et/ou groupes dans Dynamic Signal basé sur utilisateur et/ou les affectations de groupe dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML SSO pour Dynamic Signal en suivant les instructions fournies dans le [didacticiel l’authentification unique de le Dynamic Signal](dynamicsignal-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Pour configurer l’approvisionnement automatique pour Dynamic Signal dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Dynamic Signal**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Dynamic Signal**.

    ![Lien Dynamic Signal dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Configuration](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Configuration](common/provisioning-automatic.png)

5. Sous le **informations d’identification administrateur** section, entrée le **URL de locataire** et **jeton Secret** du compte de votre Dynamic Signal comme décrit à l’étape 6.

6. Dans la console d’administration Dynamic Signal, accédez à **Admin > Avancé > API**.

    ![Configuration de Dynamic Signal](./media/dynamicsignal-provisioning-tutorial/secret-token-1.png)

    Copie le **URL de l’API SCIM** à **URL de locataire**. Cliquez sur **générer un nouveau jeton** pour générer un **le jeton du porteur** et copiez la valeur à **jeton Secret**.

    ![Configuration de Dynamic Signal](./media/dynamicsignal-provisioning-tutorial/secret-token-2.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **tester la connexion** pour vérifier qu’Azure AD peut se connecter à Dynamic Signal. Si la connexion échoue, vérifiez que votre compte de Dynamic Signal dispose des autorisations d’administrateur et réessayez.

    ![Jeton et l’URL de locataire](common/provisioning-testconnection-tenanturltoken.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

9. Cliquez sur **Enregistrer**.

10. Sous le **mappages** section, sélectionnez **synchroniser les utilisateurs Azure Active Directory à Dynamic Signal**.

    ![Mappages utilisateur Dynamic Signal](media/dynamicsignal-provisioning-tutorial/user-mappings.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Dynamic Signal dans le **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les comptes d’utilisateur dans Dynamic Signal pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de l’utilisateur keeper](media/dynamicsignal-provisioning-tutorial/user-mapping-attributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer l’approvisionnement de service pour Dynamic Signal Azure AD, modifiez le **état d’approvisionnement** à **sur** dans le **paramètres** section.

    ![État de l’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définir les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Dynamic Signal, en choisissant les valeurs souhaitées dans **étendue** dans le **paramètres** section.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Configuration de l’enregistrement de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers des rapports d’activité, qui décrit toutes les actions effectuées par le service sur Dynamic Signal de provisionnement Azure AD d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Dynamic Signal ne prend pas en charge les suppressions utilisateur permanente à partir d’Azure AD. Pour supprimer définitivement un utilisateur Dynamic Signal, l’opération doit être effectuée via la console d’administration Dynamic Signal l’interface utilisateur. 
* Dynamic Signal ne prend pas en charge les groupes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
