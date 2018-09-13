---
title: Configurer l’inscription et la connexion avec un compte Microsoft à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes Microsoft dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 338c2a197cb50091c3b272e0ce590341ffda1d7f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341081"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Microsoft à l’aide d’Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft

Pour utiliser un compte Microsoft en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte Microsoft, vous pouvez en obtenir un à l’adresse [https://www.live.com/](https://www.live.com/).

1. Connectez-vous au [Portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) avec vos informations d'identification Microsoft.
2. En haut à droite, sélectionnez **Ajouter une application**.
3. Indiquez un **Nom** pour votre application et cliquez sur **Créer**.
4. Sur la page d’inscription, copiez la valeur de l’**ID de l’application**. Il sert à configurer votre compte Microsoft comme fournisseur d’identité dans votre client.
5. Sélectionnez **Ajouter une plateforme**, puis choisissez **Web**.
6. Entrez `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` dans **URL de redirection**. Remplacez **{tenant}** par votre nom de locataire (par exemple, contosob2c).
7. Sous **Secrets de l’application**, sélectionnez **Générer un nouveau mot de passe**. Copiez le nouveau mot de passe affiché à l’écran. Vous en aurez besoin pour configurer un compte Microsoft comme fournisseur d’identité dans votre client. Ce mot de passe est une information d’identification de sécurité importante.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configuration d’un compte Microsoft en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, saisissez *MSA*.
6. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Compte Microsoft**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’Id d’application enregistré précédemment en tant qu’**ID Client** et entrez le mot de passe enregistré en tant que **clé secrète client** de l’application de compte Microsoft créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration de compte Microsoft.

