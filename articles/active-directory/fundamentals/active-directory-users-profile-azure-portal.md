---
title: Comment ajouter ou mettre à jour les informations de profil pour un utilisateur dans Azure Active Directory | Microsoft Docs
description: Découvrez comment ajouter des informations à un profil utilisateur, dont une image de profil, des informations sur l’emploi, et quelques réglages dans Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: d4e2ebd88430cab1fe0734e342b1c9ba4a29af1b
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728944"
---
# <a name="how-to-add-or-update-a-users-profile-information-using-azure-active-directory"></a>Guide pour ajouter ou mettre à jour les informations de profil pour un utilisateur avec Azure Active Directory
Ajoutez des informations à un profil utilisateur, dont une image de profil, des informations sur l’emploi, et quelques réglages avec Azure Active Directory (Azure AD). Pour en savoir plus sur l’ajout de nouveaux utilisateurs, consultez [Comment ajouter ou supprimer des utilisateurs dans Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Ajouter ou modifier les informations de profil
Vous trouverez bien plus d’informations disponibles dans un profil utilisateur qu’il est possible d’en ajouter lors de la création de l’utilisateur. Toutes ces informations supplémentaires sont facultatives et peuvent être ajoutées si besoin par votre organisation.

## <a name="to-add-or-change-profile-information"></a>Ajouter ou modifier les informations de profil
1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général ou administrateur d’utilisateurs du répertoire.

2. Sélectionnez **Azure Active Directory**, **Utilisateurs**, puis un utilisateur. Par exemple, _Alain Charon_.

    La page **Alain Charon - Profil** s’affiche.

    ![Page du profil utilisateur, avec les informations modifiables](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Sélectionnez **Modifier** pour ajouter ou mettre à jour les informations incluses dans chaque section disponible (facultatif).

    ![Page du profil utilisateur, montrant les zones modifiables](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Image du profil**. Sélectionnez une image miniature pour le compte de l’utilisateur. Cette image s’affiche dans Azure Active Directory et sur la page personnelle de l’utilisateur, comme la page myapps.microsoft.com.

    - **Identité**. Ajoutez toutes les informations relatives au compte, telles que le nom marital ou un nom d’utilisateur modifié. 

    - **Informations sur l’emploi**. Ajoutez toutes les informations relatives à l’emploi, telles que le poste de l’utilisateur, le département ou son responsable.

    - **Réglages**. Décidez si l’utilisateur peut se connecter au locataire Azure Active Directory. Vous pouvez aussi spécifier l’emplacement global de l’utilisateur.

    - **Informations de contact**. Ajoutez les informations de contact importantes de l’utilisateur. Par exemple, une adresse ou un numéro de téléphone.

    - **Informations de contact d’authentification**. Vérifiez ces informations pour vous assurer que le numéro de téléphone et l’adresse e-mail de l’utilisateur sont valides. Azure Active Directory utilise ces informations pour s’assurer que l’utilisateur est véritablement un utilisateur lors de la connexion. Les informations de contact d’authentification ne peuvent être mises à jour que par l’administrateur général.

4. Sélectionnez **Enregistrer**.

    Toutes vos modifications sont enregistrées pour l’utilisateur.

    >[!Note]
    >Vous devez utiliser Windows Server Active Directory pour mettre à jour l’identité, les informations de contact ou sur l’emploi des utilisateurs dont la source d’autorité est Windows Server Active Directory. Une fois la mise à jour terminée, vous devez attendre la fin du prochain cycle de synchronisation pour constater les modifications.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez mis à jour les profils de vos utilisateurs, vous pouvez exécuter les procédures de base suivantes :

- [Ajouter ou supprimer des utilisateurs](add-users-azure-active-directory.md)

- [Attribuer des rôles aux utilisateurs](active-directory-users-assign-role-azure-portal.md)

- [Créer un groupe de base et ajouter des membres](active-directory-groups-create-azure-portal.md)

Vous pouvez également réaliser d’autres tâches de gestion d’utilisateur, comme l’affectation de délégués, l’utilisation de stratégies et le partage de comptes d’utilisateur. Pour en savoir plus sur les autres actions disponibles, consultez la [documentation Gestion des utilisateurs Azure Active Directory](../users-groups-roles/index.yml).
