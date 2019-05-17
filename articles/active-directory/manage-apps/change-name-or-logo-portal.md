---
title: Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory | Microsoft Docs
description: Comment modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780918"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory

Il est facile de modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations appropriées pour effectuer ces modifications, et vous devez être le créateur de l’application personnalisée.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Comment modifier le nom ou le logo d’une application d’entreprise ?

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/) avec un compte qui est un administrateur global pour le répertoire. Le **centre d’administration Azure Active Directory** page s’affiche.
2. Dans le volet gauche, sélectionnez **Applications d’entreprise**. La liste de vos applications d’entreprise s’affiche.
3. Sélectionnez une application. La page de vue d’ensemble d’application s’affiche.
4. Dans le volet de vue d’ensemble d’application, sous la **gérer** titre, sélectionnez **propriétés**. Le **propriétés** page s’affiche.
5. Si vous souhaitez modifier le nom, sélectionnez le **nom** zone, tapez le nouveau nom et appuyez sur **entrée**.
6. Si vous souhaitez modifier le logo, recherchez le **Logo** champ et sélectionnez l’icône de dossier en regard le **sélectionner un fichier** zone, qui est sous l’image du logo de l’application actuelle.

   ![Sélection de la commande Propriétés](./media/change-name-or-logo-portal/change-logo.png)

   Sinon, si vous n’êtes pas remplacer le logo, passez à l’étape 8.
7. Dans le sélecteur de fichiers, sélectionnez le fichier que vous souhaitez en tant que le nouveau logo. Le nom du fichier apparaît dans la zone située sous l’image du logo actuel.

   > [!NOTE]
   > Azure nécessite l’image du logo pour être un fichier PNG, et il applique des limites sur la largeur, hauteur et taille de fichier.
8. Sélectionnez **Enregistrer**. Si vous avez choisi un nouveau logo, le **Logo** modifications de l’image du champ afin de refléter le nouveau fichier de logo.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Afficher les groupes et les membres de votre organisation dans Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](assign-user-or-group-access-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](remove-user-or-group-access-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](disable-user-sign-in-portal.md)
