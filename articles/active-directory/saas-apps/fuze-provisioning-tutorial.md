---
title: 'Tutoriel : Configurez Fuze pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a58402297380116f83214e52ae7f2796412755b9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057859"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Tutoriel : Configurez Fuze pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Fuze et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Fuze.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire Fuze](https://www.fuze.com/).
* Un compte d’utilisateur dans Fuze avec des autorisations d’administrateur.

## <a name="assigning-users-to-fuze"></a>Attribution d’utilisateurs à Fuze

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Fuze. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à Fuze en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-fuze"></a>Conseils importants pour l’attribution d’utilisateurs à Fuze

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Fuze afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à Fuze, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue Attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-fuze-for-provisioning"></a>Configurez Fuze pour l’approvisionnement

Avant de configurer Fuze pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM sur Fuze. 

1. Contactez d’abord votre représentant Fuze pour obtenir les informations requises suivantes :

    * Liste des références SKU de produits Fuze actuellement utilisées dans votre entreprise.
    * Liste des codes d’emplacement des sites de votre entreprise.
    * Liste des codes de service de votre entreprise.

2. Vous trouverez ces références SKU et ces codes dans votre contrat Fuze et vos documents de configuration, ou en contactant votre représentant Fuze.

3. Une fois toutes les exigences remplies, votre représentant Fuze vous fournira le jeton d’authentification Fuze requis pour activer l’intégration. Vous devrez entrer cette valeur dans le champ Jeton secret dans l’onglet Approvisionnement de votre application Fuze dans le portail Microsoft Azure.

## <a name="add-fuze-from-the-gallery"></a>Ajouter Fuze depuis la galerie

Avant de configurer Fuze pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Fuze depuis la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Fuze depuis la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Fuze**, sélectionnez **Fuze** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Fuze dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-fuze"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Fuze 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Fuze en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Fuze en suivant les instructions fournies dans le [didacticiel sur l’authentification unique pour Fuze](fuze-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Fuze dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Fuze**.

    ![Lien Fuze dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://api.fuze.com/scim/v2` dans **URL de locataire**. Saisissez la valeur du **Jeton d’authentification SCIM** que vous avez préalablement obtenue grâce à votre représentant Fuze dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Fuze. Si la connexion échoue, vérifiez que votre compte Fuze dispose des autorisations d’administrateur et réessayez.

    ![Jeton URL de locataire](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Fuze**.

    ![Mappages d’utilisateurs Fuze](media/fuze-provisioning-tutorial/image01.png)

9. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Fuze. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Fuze pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Mappages d’utilisateurs Fuze](media/fuze-provisioning-tutorial/image00.png)

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour Fuze, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur Fuze en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Fuze.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Fuze prend en charge les attributs SCIM personnalisés, appelés **droits**. Ces attributs peuvent uniquement être créés et ne peuvent pas être mis à jour. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md).