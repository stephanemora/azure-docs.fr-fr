---
title: Modifier le nom ou le logo d’une application d’entreprise dans Azure AD
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
ms.openlocfilehash: d3dfe0f8788275dd4403b6c9cad99a8eb09e479b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274714"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Modifier le nom ou le logo d’une application d’entreprise dans Azure Active Directory

Il est facile de modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations appropriées pour effectuer ces modifications, et vous devez être le créateur de l’application personnalisée.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Comment modifier le nom ou le logo d’une application d’entreprise ?

1. Connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com/) en utilisant un compte d’administrateur général pour le répertoire. La page du **Centre d’administration Azure Active Directory** s’affiche.
2. Dans le volet gauche, sélectionnez **Applications d’entreprise**. La liste de vos applications d’entreprise s’affiche.
3. Sélectionnez une application. La page de vue d’ensemble de l’application s’affiche.
4. Dans le volet de vue d’ensemble de l’application, sous le titre **Gérer**, sélectionnez **Propriétés**. La page **Propriétés** apparaît.
5. Si vous souhaitez modifier le nom, sélectionnez la zone **Nom**, saisissez le nouveau nom et appuyez sur **Entrée**.
6. Si vous souhaitez modifier le logo, recherchez le champ **Logo** et sélectionnez l’icône de dossier en regard de la zone **Sélectionner un fichier**, sous l’image du logo actuel de l’application.

   ![Sélection de la commande Propriétés](./media/change-name-or-logo-portal/change-logo.png)

   Si vous ne remplacez pas le logo, passez à l’étape 8.
7. Dans le sélecteur de fichiers, sélectionnez le fichier à utiliser en tant que nouveau logo. Le nom du fichier apparaît dans la zone située sous l’image du logo actuel.

   > [!NOTE]
   > Dans Azure, l’image du logo doit être un fichier PNG. Azure applique des limites sur la largeur, la hauteur et la taille de fichier.
8. Sélectionnez **Enregistrer**. Si vous avez choisi un nouveau logo, l’image du champ **Logo** change afin de refléter le nouveau fichier de logo.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Afficher les membres et groupes de votre organisation dans Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](assign-user-or-group-access-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](remove-user-or-group-access-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](disable-user-sign-in-portal.md)
