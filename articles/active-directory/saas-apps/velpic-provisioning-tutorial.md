---
title: 'Didacticiel : Configuration de Velpic pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur à Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270901"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Didacticiel : Configuration de Velpic pour l’approvisionnement automatique

L’objectif de ce didacticiel est de vous montrer les étapes que vous devez effectuer dans Velpic et Azure AD pour approvisionner et retirer automatiquement des comptes d’utilisateur à partir d’Azure AD à Velpic.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un client Azure Active Directory
* Un locataire Velpic avec la [plan entreprise](https://www.velpic.com/pricing.html) ou mieux activé
* Un compte d’utilisateur dans Velpic avec des autorisations d’administrateur

## <a name="assigning-users-to-velpic"></a>Affectation d’utilisateurs à Velpic

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes d’utilisateur, les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Velpic. Une fois choisi, vous pouvez affecter ces utilisateurs à votre application Velpic en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Conseils importants pour l’affectation d’utilisateurs à Velpic

* Il est recommandé qu’un seul utilisateur Azure AD être affecté à Velpic pour tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Velpic, vous devez sélectionner le **utilisateur** rôle ou une autre application-rôle valide propre à (si disponible) dans la boîte de dialogue d’attribution. Notez que le **accès par défaut** rôle ne fonctionne pas pour l’approvisionnement, et ces utilisateurs seront ignorées.

## <a name="configuring-user-provisioning-to-velpic"></a>Configuration des utilisateurs à Velpic

Cette section explique comment connecter votre annuaire Azure AD au compte d’utilisateur de Velpic API d’approvisionnement, et configurer le service d’approvisionnement pour créer, mettre à jour et désactiver des comptes utilisateur affectés dans Velpic basé sur l’affectation d’utilisateurs et groupe dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML Single Sign-On pour Velpic, suivant les instructions fournies dans [Azure portal](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Pour configurer l’approvisionnement de comptes d’utilisateur automatique à Velpic dans Azure AD :

1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré Velpic pour l’authentification unique, recherchez votre instance de Velpic à l’aide du champ de recherche. Sinon, sélectionnez **ajouter** et recherchez **Velpic** dans la galerie d’applications. Sélectionnez Velpic dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de Velpic, puis sélectionnez le **approvisionnement** onglet.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Configuration de Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Sous le **informations d’identification administrateur** section, entrée le **URL de locataire & jeton Secret** de Velpic. () Vous pouvez trouver ces valeurs sous votre compte Velpic : **Gérer** > **intégration** > **plug-in** > **SCIM**)

    ![Valeurs d’autorisation](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Dans le portail Azure, cliquez sur **tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Velpic. Si la connexion échoue, vérifiez que votre compte de Velpic dispose des autorisations d’administrateur et réessayez l’étape 5.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case se trouvant en dessous.

8. Cliquez sur **Enregistrer**.

9. Dans la section mappages, sélectionnez **synchroniser les utilisateurs Azure Active Directory à Velpic**.

10. Dans le **des mappages d’attributs** section, passez en revue les attributs utilisateur qui seront synchronisés d’Azure AD à Velpic. Notez que les attributs sélectionnés en tant que **correspondance** propriétés seront utilisées pour faire correspondre les comptes d’utilisateur dans Velpic pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer l’approvisionnement de service pour Velpic Azure AD, modifiez le **état d’approvisionnement** à **sur** dans le **paramètres** section

12. Cliquez sur **Enregistrer**.

Ceci démarrera la synchronisation initiale des utilisateurs et/ou groupes affectés à Velpic dans la section utilisateurs et groupes. Notez que la synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)