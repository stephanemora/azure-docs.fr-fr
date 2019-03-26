---
title: 'Didacticiel : Configurer ThousandEyes pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur sur ThousandEyes.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f008e981abb11a4927ec045c33342bbac9a05bd8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436793"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Didacticiel : Configurer ThousandEyes pour l’approvisionnement automatique d’utilisateurs


L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans ThousandEyes et Azure AD pour approvisionner et retirer automatiquement des comptes d’utilisateur entre Azure AD et ThousandEyes. 

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory
*   Un actif [compte ThousandEyes](https://www.thousandeyes.com/pricing)
*   Un compte d’utilisateur ThousandEyes qui a été attribué un rôle qui inclut les 3 autorisations suivantes :
    * afficher tous les utilisateurs
    * modifier l’utilisateur
    * Autorisations d’accès API

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur le [API ThousandEyes SCIM](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK_ThousandEyes-support-for-SCIM). 

## <a name="assigning-users-to-thousandeyes"></a>Affectation d’utilisateurs à ThousandEyes

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application ThousandEyes. Dès que vous avez fait votre choix, vous pouvez assigner ces utilisateurs à votre application ThousandEyes en suivant les instructions disponibles à la page suivante :

[Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Conseils importants concernant l’affectation d’utilisateurs à ThousandEyes

*   Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à ThousandEyes afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous assignez un utilisateur à ThousandEyes, vous devez sélectionner le **utilisateur** rôle ou un autre valide spécifique à l’application (si disponible) dans la boîte de dialogue d’attribution. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement et ces utilisateurs sont ignorés.

## <a name="configure-auto-provisioned-user-roles-in-thousandeyes"></a>Configurer des rôles d’utilisateur approvisionné automatiquement dans ThousandEyes

Pour chaque groupe de compte, vous êtes le provisionnement automatique des utilisateurs dans vous peuvent configurer un ensemble de rôles à appliquer lorsque le nouveau compte d’utilisateur est créé. Par défaut, l’approvisionnement automatique des utilisateurs sont affectés les _utilisateur standard_ rôle pour le compte de tous les groupes sauf configuration différente.

1. Pour spécifier un nouvel ensemble de rôles pour les utilisateurs d’approvisionné automatiquement en journal ThousandEyes et accédez à la section Paramètres de SCIM **> votre icône de l’utilisateur dans le coin supérieur droit > Paramètres du compte > organisation > sécurité et authentification.** 

   ![Accédez à paramètres de l’API SCIM](https://monosnap.com/file/kqY8Il7eysGFAiCLCQWFizzM27PiBG)

2. Ajoutez une entrée pour chaque groupe de comptes, affecter un ensemble de rôles puis *enregistrer* vos modifications.

   ![Définir des rôles par défaut et les groupes de comptes pour les utilisateurs créés via l’API SCIM](https://monosnap.com/file/16siam6U8xDQH1RTnaxnmIxvsZuNZG)


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configuration de l'approvisionnement d’utilisateurs pour ThousandEyes 

Cette section va vous guider afin de connecter votre instance Azure AD à l’API d’approvisionnement de comptes d’utilisateur de ThousandEyes, puis configurer le service d’approvisionnement pour créer, mettre à jour et désactiver des comptes d’utilisateur affectés dans ThousandEyes, en fonction des affectations d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML unique Sign-On (SSO) pour ThousandEyes, suivant la [instructions fournies dans la base de connaissances Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-tutorial) pour terminer l’authentification unique. L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que ces deux fonctionnalités se complètent.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurer l’approvisionnement automatique de comptes d’utilisateur pour ThousandEyes dans Azure AD


1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré ThousandEyes pour l’authentification unique, recherchez votre instance de ThousandEyes à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **ThousandEyes** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez ThousandEyes, puis ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de ThousandEyes, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Sous le **informations d’identification administrateur** section, entrée le **jeton du porteur OAuth** généré par compte des votre ThousandEyes (vous pouvez trouver et générer un jeton sous votre compte ThousandEyes  **Profil** section).

    ![Approvisionnement de ThousandEyes](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application ThousandEyes. Si la connexion échoue, vérifiez que votre compte ThousandEyes dispose des autorisations d’administrateur et réessayez l’étape 5.

7. Entrez l’adresse de courrier d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case « Envoyer une notification par e-mail en cas de défaillance ».

8. Cliquez sur **Enregistrer**. 

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ThousandEyes**.

10. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers ThousandEyes. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans ThousandEyes pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour ThousandEyes, accédez à la section **Paramètres** et définissez le **Statut de l’approvisionnement** sur **Activé**.

12. Cliquez sur **Enregistrer**. 

Cette opération démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à ThousandEyes dans la section Utilisateurs et groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
