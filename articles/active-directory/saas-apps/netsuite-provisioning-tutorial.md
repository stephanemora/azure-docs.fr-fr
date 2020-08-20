---
title: 'Tutoriel : Configurer Netsuite OneWorld pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Netsuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 158b66d63292a1ffb503f3f87eddfd33a2b09b71
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554479"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutoriel : Configuration de Netsuite pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de vous montrer la procédure à suivre dans Netsuite OneWorld et Azure AD pour provisionner et déprovisionner automatiquement des comptes utilisateur d’Azure AD vers Netsuite.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory.
*   Un abonnement Netsuite OneWorld. Notez que le provisionnement automatique d’utilisateurs n’est actuellement pris en charge que par Netsuite OneWorld.
*   Un compte utilisateur dans Netsuite avec des autorisations d’administrateur.

## <a name="assigning-users-to-netsuite-oneworld"></a>Affectation d’utilisateurs à Netsuite OneWorld

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, seuls les utilisateurs et les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à votre application Netsuite. Une fois que vous avez effectuer votre choix, vous pouvez affecter ces utilisateurs à votre application Netsuite en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Conseils importants pour l’affectation d’utilisateurs à Netsuite OneWorld

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à Netsuite afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous affectez un utilisateur à Netsuite, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-user-provisioning"></a>Activer l’approvisionnement des utilisateurs

Cette section explique comment connecter votre Azure AD à l’API d’approvisionnement de comptes utilisateur de Netsuite pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Netsuite en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP] 
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Netsuite en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-user-account-provisioning"></a>Pour configurer l’approvisionnement de comptes utilisateur :

L’objectif de cette section est d’expliquer comment activer l’approvisionnement utilisateur des comptes utilisateur Active Directory sur Netsuite.

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

1. Si vous avez déjà configuré Netsuite pour l’authentification unique, recherchez votre instance de Netsuite à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Netsuite** dans la galerie d’applications. Sélectionnez Netsuite dans les résultats de recherche et ajoutez-la à votre liste d’applications.

1. Sélectionnez votre instance de Netsuite, puis sélectionnez l’onglet **Approvisionnement**.

1. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Dans la section **Informations d’identification de l’administrateur**, fournissez les paramètres de configuration suivants :
   
    a. Dans la zone de texte **Nom d'utilisateur Admin**, tapez le nom d’un compte Netsuite auquel le profil **System Administrator** (Administrateur système) est affecté dans Netsuite.com.
   
    b. Dans la zone de texte **Mot de passe d’administrateur**, entrez le mot de passe de ce compte.
      
1. Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Netsuite.

1. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case.

1. Cliquez sur **Enregistrer.**

1. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Netsuite**.

1. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Netsuite. Remarque : les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes utilisateur dans Netsuite, en vue d’opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

1. Pour activer le service d’approvisionnement Azure AD pour Netsuite, affectez la valeur **Activé** au paramètre **État de l’approvisionnement** dans la section Paramètres.

1. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à Netsuite dans la section Utilisateurs et Groupes. Notez que la synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application Netsuite.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’authentification unique](netsuite-tutorial.md)
