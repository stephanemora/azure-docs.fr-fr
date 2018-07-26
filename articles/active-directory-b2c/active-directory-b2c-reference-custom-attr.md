---
title: Définir des attributs personnalisés dans Azure Active Directory B2C | Microsoft Docs
description: Définissez des attributs personnalisés pour votre application dans Azure Active Directory B2C afin de recueillir des informations sur vos clients.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968772"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Définir des attributs personnalisés dans Azure Active Directory B2C

 Toutes les applications orientées client ont des exigences uniques concernant les informations qui doivent être recueillies. Votre locataire Azure Active Directory (Azure AD) B2C est fourni avec un ensemble intégré d’informations stockées dans des attributs : le prénom, le nom, la ville et le code postal. Avec Azure AD B2C, vous pouvez étendre l’ensemble d’attributs stockés sur chaque compte client. 
 
 Vous pouvez créer des attributs personnalisés dans le [portail Azure](https://portal.azure.com/) et les utiliser dans vos stratégies d’inscription, de connexion ou de modification de profil. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md). Les attributs personnalisés dans Azure AD B2C utilisent les [Extensions de schéma de répertoire de l’API Azure AD Graph](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Création d’un attribut personnalisé

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Sélectionnez **Attributs utilisateur**, puis **Ajouter**.
5. Fournissez un **nom** pour l’attribut personnalisé (par exemple, « ShoeSize »).
6. Choisissez un **Type de données**. Seules les valeurs **String**, **Boolean** et **Int** sont disponibles.
7. Si vous le souhaitez, entrez une **Description** à titre d’information. 
8. Cliquez sur **Créer**.

L’attribut personnalisé est actuellement disponible dans la liste des **attributs utilisateur**, et pour une utilisation dans vos stratégies. Un attribut personnalisé est créé lors de sa première utilisation dans une stratégie, et non pas lorsque vous l’ajoutez à la liste des **Attributs utilisateur**.

## <a name="use-a-custom-attribute-in-your-policy"></a>Utilisation d’un attribut personnalisé dans votre stratégie

1. Dans votre locataire Azure AD B2C, sélectionnez **Stratégies d’inscription ou de connexion**.
2. Sélectionnez votre stratégie (par exemple, « B2C_1_SignupSignin ») pour l’ouvrir. 
3. Cliquez sur **Modifier**.
4. Sélectionnez **Attributs d’inscription**, puis sélectionnez l’attribut personnalisé (par exemple, « ShoeSize »). Cliquez sur **OK**.
5. Sélectionnez **Revendications d’applications**, puis sélectionnez l’attribut personnalisé. Cliquez sur **OK**.
6. Cliquez sur **Enregistrer**.

La fonctionnalité **Exécuter maintenant** de la stratégie permet de vérifier l’expérience client. Vous devez maintenant voir **ShoeSize** dans la liste d’attributs collectés lors de l’inscription, et le voir dans le jeton retourné à votre application.

