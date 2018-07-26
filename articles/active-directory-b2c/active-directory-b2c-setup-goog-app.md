---
title: Configurer l’inscription et la connexion avec un compte Google à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Google dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd0bf50d73b70e37195e8e5e45336b68e4e883e7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915637"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Google à l’aide d’Azure Active Directory B2C

## <a name="create-a-google-application"></a>Créer une application Google

Pour utiliser un compte Google comme fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application qui la représente dans votre locataire (tenant). Si vous ne disposez pas déjà d’un compte Google, vous pouvez en créer un à l’adresse [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Connectez-vous à la [Console Google Developers](https://console.developers.google.com/) avec les informations d’identification de votre compte Google.
2. Sélectionnez **Créer un projet**, puis cliquez sur **Créer**. Si vous avez déjà créé des projets, sélectionnez la liste des projets, puis **Nouveau projet**.
3. Entrez un **Nom de projet**, puis cliquez sur **Créer**.
3. Sélectionnez **Informations d’identification** dans le menu de gauche, puis **Créer des informations d’identification** > **ID client OAuth**.
4. Sélectionnez **Configurer l’écran de consentement**.
5. Sélectionnez ou spécifiez une **Adresse e-mail** valide, indiquez un **Nom de produit présenté aux utilisateurs**, puis cliquez sur **Enregistrer**.
6. Sous **Type d’application**, sélectionnez **Application web**.
7. Entrez un **Nom** pour votre application, saisissez `https://login.microsoftonline.com` dans **Origines JavaScript autorisées** et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans **URI de redirection autorisés**. Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com).
8. Cliquez sur **Créer**.
9. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer Google comme fournisseur d’identité dans votre locataire. **Client secret** est une information d’identification de sécurité importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurer un compte Google comme fournisseur d’identité

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Saisissez un **Nom**. Par exemple, entrez *Google*.
6. Sélectionnez **Type de fournisseur d’identité** et **Google**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID client enregistré précédemment en tant **qu’ID Client** ainsi que la clé secrète client enregistrée en tant que **Clé secrète client** de l’application Google créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Google.

