---
title: Définir des attributs personnalisés dans Azure Active Directory B2C | Microsoft Docs
description: Définissez des attributs personnalisés pour votre application dans Azure Active Directory B2C afin de recueillir des informations sur vos clients.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186047"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Définir des attributs personnalisés dans Azure Active Directory B2C

 Toutes les applications orientées client ont des exigences uniques concernant les informations qui doivent être recueillies. Votre locataire Azure Active Directory B2C (Azure AD B2C) est fourni avec un ensemble intégré d’informations stockées dans des attributs, comme le prénom, le nom, la ville et le code postal. Avec Azure AD B2C, vous pouvez étendre l’ensemble d’attributs stockés sur chaque compte client.

 Vous pouvez créer des attributs personnalisés dans le [portail Azure](https://portal.azure.com/) et les utiliser dans vos flux d’utilisateur d’inscription, de connexion ou de modification de profil. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Création d’un attribut personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**.

    ![Basculez vers votre client Azure AD B2C.](./media/user-flow-custom-attributes/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Locataire B2C mis en surbrillance dans le répertoire et le filtre d’abonnement](./media/user-flow-custom-attributes/select-directory.PNG)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Sélectionnez **Attributs utilisateur**, puis **Ajouter**.
5. Fournissez un **nom** pour l’attribut personnalisé (par exemple, « ShoeSize »).
6. Choisissez un **Type de données**. Seules les valeurs **String**, **Boolean** et **Int** sont disponibles.
7. Si vous le souhaitez, entrez une **Description** à titre d’information.
8. Cliquez sur **Créer**.

L’attribut personnalisé est actuellement disponible dans la liste des **attributs utilisateur**, et pour une utilisation dans vos flux d’utilisateur. Un attribut personnalisé est créé lors de sa première utilisation dans un flux d’utilisateur, et non pas quand vous l’ajoutez à la liste des **Attributs utilisateur**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Utilisation d’un attribut personnalisé dans votre flux d’utilisateur

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**.
1. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir.
1. Sélectionnez **Attributs d’utilisateur**, puis sélectionnez l’attribut personnalisé (par exemple, « ShoeSize »). Cliquez sur **Enregistrer**.
1. Sélectionnez **Revendications d’applications**, puis sélectionnez l’attribut personnalisé.
1. Cliquez sur **Enregistrer**.

Une fois que vous avez créé un utilisateur à l’aide d’un flux d’utilisateur qui utilise l’attribut personnalisé nouvellement créé, l’objet peut être interrogé dans l’[Explorateur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Vous pouvez également utiliser la fonctionnalité [Exécuter le flux d’utilisateur](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) sur le flux d’utilisateur pour vérifier l’expérience utilisateur. Vous devez maintenant voir **ShoeSize** dans la liste d’attributs collectés lors de l’inscription, et le voir dans le jeton retourné à votre application.
