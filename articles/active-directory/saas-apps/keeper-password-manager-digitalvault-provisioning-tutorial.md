---
title: 'Didacticiel : Configurer Keeper Password Manager & Digital Vault pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur à Keeper Password Manager & Digital Vault.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: 445579eb780b49f536ef1a9e13e5ca43db6f98f6
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508528"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Didacticiel : Configurer Keeper Password Manager & Digital Vault pour l’approvisionnement automatique

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Keeper Password Manager & Digital Vault et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et retirer automatiquement les utilisateurs et/ou groupes au détenteur de mot de passe Manager & Digital Vault.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales pour les fonctionnalités en version préliminaire, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Keeper Password Manager & Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Un compte d’utilisateur Keeper Password Manager & Digital Vault avec des autorisations d’administrateur.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Ajouter Keeper Password Manager & Digital Vault à partir de la galerie

Avant de configurer Keeper Password Manager & Digital Vault pour l’approvisionnement automatique avec Azure AD, vous devez ajouter Keeper Password Manager & Digital Vault à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Keeper Password Manager & Digital Vault à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le  **[Azure portal](https://portal.azure.com)**, dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, sélectionnez le **nouvelle application** bouton en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Keeper Password Manager & Digital Vault**, sélectionnez **Keeper Password Manager & Digital Vault** dans le volet de résultats, puis cliquez sur le **ajouter**pour ajouter l’application.

    ![Keeper Password Manager & Digital Vault dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Affectation d’utilisateurs à Keeper Password Manager & Digital Vault

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, seuls les utilisateurs et/ou les groupes qui ont été assignés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Keeper Password Manager & Digital Vault. Une fois choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Keeper Password Manager & Digital Vault, en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Conseils importants pour l’affectation d’utilisateurs à Keeper Password Manager & Digital Vault

* Il est recommandé qu’un seul utilisateur Azure AD est affecté à Keeper Password Manager & Digital Vault à tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Keeper Password Manager & Digital Vault, vous devez sélectionner un rôle spécifique à l’application valide (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configuration de l’approvisionnement automatique d’utilisateurs à Keeper Password Manager & Digital Vault 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement de AD Azure pour créer, mettre à jour et désactiver des utilisateurs et/ou groupes dans Keeper Password Manager & Digital Vault basé sur utilisateur et/ou les affectations de groupe dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML SSO pour Keeper Password Manager & Digital Vault en suivant les instructions fournies dans le [Keeper Password Manager & Digital Vault unique authentification didacticiel](keeperpasswordmanager-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Pour configurer l’approvisionnement automatique de Keeper Password Manager & Digital Vault dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis sélectionnez **toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Keeper Password Manager & Digital Vault**.

    ![Lien Keeper Password Manager & Digital Vault dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Configuration](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Configuration](common/provisioning-automatic.png)

5. Sous le **informations d’identification administrateur** section, entrée le **URL de locataire** et **jeton Secret** de votre Keeper Password Manager & compte de Digital Vault comme décrit à l’étape 6.

6. Connectez-vous à votre [Console d’administration Keeper](https://keepersecurity.com/console/#login). Cliquez sur **administrateur** et sélectionnez un nœud existant ou créez-en un. Accédez à la **approvisionnement** onglet et sélectionnez **ajouter une méthode**.

    ![Console d’administration keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Sélectionnez **SCIM (System for Cross-Domain Identity Management**.

    ![Détenteur ajouter SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Cliquez sur **créer un jeton de provisionnement**.

    ![Détenteur créer le point de terminaison](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copiez les valeurs de **URL** et **jeton** et collez-les dans **URL de locataire** et **jeton Secret** dans Azure AD. Cliquez sur **enregistrer** pour terminer l’installation de configuration sur le détenteur.

    ![Créer le jeton keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **tester la connexion** pour vérifier qu’Azure AD peut se connecter à Keeper Password Manager & Digital Vault. Si la connexion échoue, vérifiez que votre compte de Keeper Password Manager & Digital Vault dispose d’autorisations d’administrateur et réessayez.

    ![Jeton et l’URL de locataire](common/provisioning-testconnection-tenanturltoken.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

9. Cliquez sur **Enregistrer**.

10. Sous le **mappages** section, sélectionnez **synchroniser les utilisateurs Azure Active Directory à Keeper Password Manager & Digital Vault**.

    ![Mappages d’utilisateurs keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Keeper Password Manager & Digital Vault dans le **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les comptes d’utilisateur Keeper Password Manager & Digital Vault pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de l’utilisateur keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Sous le **mappages** section, sélectionnez **synchroniser les groupes Azure Active Directory à Keeper Password Manager & Digital Vault**.

    ![Mappages de groupes de keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Passez en revue les attributs groupe qui sont synchronisés à partir d’Azure AD à Keeper Password Manager & Digital Vault dans le **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les groupes Keeper Password Manager & Digital Vault pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs de groupe de keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pour activer l’approvisionnement de service pour Keeper Password Manager & Digital Vault Azure AD, modifiez le **état d’approvisionnement** à **sur** dans le **paramètres** section.

    ![État de l’approvisionnement activé](common/provisioning-toggle-on.png)

16. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner à Keeper Password Manager & Digital Vault en choisissant les valeurs souhaitées dans **étendue** dans le **paramètres** section.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Configuration de l’enregistrement de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers les rapports d’activité, qui décrit toutes les actions effectuées par Azure AD sur Keeper Password Manager de service d’approvisionnement approvisionnement & Digital Vault.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Keeper Password Manager & Digital Vault nécessite **e-mails** et **nom d’utilisateur** pour avoir la même valeur de la source, comme les mises à jour des attributs modifiera l’autre valeur.
* Vous ne devriez pas prendre en charge les suppressions de l’utilisateur, uniquement désactiver keeper Password Manager & Digital Vault. Les utilisateurs désactivés apparaissent comme verrouillé dans l’interface utilisateur de Keeper Admin Console.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

