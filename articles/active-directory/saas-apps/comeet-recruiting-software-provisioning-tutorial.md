---
title: 'Didacticiel : Configurer le logiciel de recrutement Comeet pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur aux logiciels de recrutement Comeet.
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
ms.openlocfilehash: e8414b9737e0ee7f847827a432dd9887931a2532
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470364"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Didacticiel : Configurer le logiciel de recrutement Comeet pour l’approvisionnement automatique

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans le logiciel de recrutement Comeet et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et retirer automatiquement les utilisateurs et/ou groupes aux logiciels de recrutement Comeet.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales pour les fonctionnalités en version préliminaire, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un client de logiciel de recrutement Comeet](https://www.comeet.co/)
* Un compte d’utilisateur dans le logiciel de recrutement Comeet avec des autorisations d’administrateur.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Ajouter des logiciels de recrutement Comeet dans la galerie

Avant de configurer le logiciel de recrutement Comeet pour l’approvisionnement automatique avec Azure AD, vous devez ajouter le logiciel de recrutement Comeet à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter des logiciels de recrutement Comeet à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le  **[Azure portal](https://portal.azure.com)**, dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, sélectionnez le **nouvelle application** bouton en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Comeet recrutement logiciel**, sélectionnez **Comeet recrutement logiciel** dans le volet de résultats, puis cliquez sur le **ajouter** pour ajouter l’application.

    ![Comeet Recruiting Software dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Affectation d’utilisateurs aux logiciels de recrutement Comeet

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, seuls les utilisateurs et/ou les groupes qui ont été assignés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder aux logiciels de recrutement Comeet. Une fois choisi, vous pouvez affecter ces utilisateurs et/ou groupes aux logiciels de recrutement Comeet en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Conseils importants pour l’affectation d’utilisateurs aux logiciels de recrutement Comeet

* Il est recommandé qu’un seul utilisateur Azure AD est affecté aux logiciels de recrutement Comeet pour tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur au logiciel de recrutement Comeet, vous devez sélectionner un rôle spécifique à l’application valide (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configuration de l’approvisionnement automatique d’utilisateurs aux logiciels de recrutement Comeet 

Guides de cette section vous à travers les étapes pour configurer le service d’approvisionnement de AD Azure pour créer, mettre à jour et désactiver des utilisateurs et/ou groupes dans le logiciel de recrutement Comeet basé sur utilisateur et/ou les affectations de groupe dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML SSO pour les logiciels de recrutement Comeet en suivant les instructions fournies dans le [didacticiel l’authentification unique de le Comeet recrutement logiciel](comeetrecruitingsoftware-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Pour configurer l’approvisionnement automatique pour les logiciels de recrutement Comeet dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis sélectionnez **toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Comeet Recruiting Software**.

    ![Lien Comeet Recruiting Software dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Configuration](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Configuration](common/provisioning-automatic.png)

5. Sous le **informations d’identification administrateur** section, entrée le **URL de locataire** et **jeton Secret** du compte Comeet recrutement de votre logiciel comme décrit à l’étape 6.

6. Dans le [console d’administration de logiciels de recrutement Comeet](https://app.comeet.co/), accédez à **Comeet > Paramètres > authentification > Microsoft Azure**et copiez le **le jeton Secret pour votre entreprise**valeur pour le **jeton Secret** champ dans Azure AD.

    ![Comeet recrutement d’approvisionnement en logiciels](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **tester la connexion** pour vérifier qu’Azure AD peut se connecter au logiciel de recrutement Comeet. Si la connexion échoue, vérifiez que votre compte de logiciels de recrutement Comeet dispose des autorisations d’administrateur et réessayez.

    ![Jeton](common/provisioning-testconnection-token.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

9. Cliquez sur **Enregistrer**.

10. Sous le **mappages** section, sélectionnez **synchroniser les utilisateurs Azure Active Directory avec Comeet**.

    ![Comeet recrutement des mappages d’utilisateur de logiciels](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD aux logiciels de recrutement Comeet dans le **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les comptes d’utilisateur dans le logiciel de recrutement Comeet pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Comeet recrutement attributs de groupe de logiciels](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer l’approvisionnement de service pour les logiciels de recrutement Comeet Azure AD, modifiez le **état d’approvisionnement** à **sur** dans le **paramètres** section.

    ![État de l’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner aux logiciels de recrutement Comeet en choisissant les valeurs souhaitées dans **étendue** dans le **paramètres** section.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Configuration de l’enregistrement de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers des rapports d’activité, qui décrit toutes les actions effectuées par le service sur les logiciels de recrutement Comeet de provisionnement Azure AD d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Logiciel de recrutement comeet ne pas actuellement en charge les groupes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

