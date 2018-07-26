---
title: Configurer l’inscription et la connexion avec un compte GitHub à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes GitHub dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 88fffd28319101c112f848eebc6e8ee27f7f863e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952016"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte GitHub à l’aide d’Azure Active Directory B2C

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

Pour utiliser un compte GitHub en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte GitHub, vous pouvez en obtenir un à l’adresse [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Créer une application GitHub OAuth

1. Connectez-vous au site web [Développeur GitHub](https://github.com/settings/developers) avec vos informations d’identification GitHub.
2. Sélectionnez les **applications OAuth**, puis sélectionnez **Inscrire une nouvelle application**.
3. Entrez le **Nom de l’application** et l’**URL de la page d’accueil**.
4. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans l’**URL de rappel d’autorisation**. Remplacez **{tenant}** par le nom de votre client Azure AD B2C (par exemple B2C.onmicrosoft.com).
5. Cliquez sur **Inscrire l’application**.
6. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous avez besoin des deux pour ajouter le fournisseur d’identité à votre locataire.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configuration d’un compte GitHub en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, entrez *GitHub*.
6. Sélectionnez **Type de fournisseur d'identité**, **GitHub (préversion)**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’ID client enregistré précédemment en tant **qu’ID Client** et entrez le secret client enregistré en tant que **Secret client** de l’application de compte Github créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer la configuration de votre compte GitHub.