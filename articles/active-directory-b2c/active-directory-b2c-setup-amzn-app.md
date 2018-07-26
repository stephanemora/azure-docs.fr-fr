---
title: Configurer l’inscription et la connexion avec un compte Amazon à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Amazon dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5fb6289f75f0c98cc218233d8adb900484ee4a17
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916494"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Amazon à l’aide d’Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Création d’une application Amazon

Pour utiliser un compte Amazon comme fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application qui la représente dans votre locataire (tenant). Si vous ne disposez pas déjà d’un compte Amazon, vous pouvez en créer un à l’adresse [http://www.amazon.com/](http://www.amazon.com/).

1. Connectez-vous au [Centre de développement Amazon](https://login.amazon.com/) avec les informations d’identification de votre compte Amazon.
2. Si ce n’est déjà fait, cliquez sur **Sign Up**, suivez la procédure d’inscription pour développeur et acceptez la politique d’utilisation.
3. Cliquez sur **Enregistrer une nouvelle application**.
4. Entrez un **Nom**, une **Description** et une **URL d’avis de confidentialité**, puis cliquez sur **Enregistrer**.
5. Dans la section **Paramètres web**, copiez les valeurs **ID client**. Sélectionnez **Montrer le secret** pour obtenir la clé secrète client et copiez-la. Vous aurez besoin de ces deux valeurs pour configurer un compte Amazon en tant que fournisseur d’identité dans votre locataire. **Client Secret** est une information d’identification de sécurité importante.
6. Dans la section **Paramètres web**, sélectionnez **Modifier**, puis entrez `https://login.microsoftonline.com` dans **Origines JavaScript autorisées** et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans **URL de retour autorisées**. Remplacez **{tenant}** par votre nom de client (par exemple, contoso.onmicrosoft.com). 
7. Cliquez sur **Enregistrer**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurer un compte Amazon comme fournisseur d’identité

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Saisissez un **Nom**. Par exemple, entrez *Amazon*.
6. Sélectionnez **Type de fournisseur d’identité** et **Amazon**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID client enregistré précédemment en tant **qu’ID Client** ainsi que la clé secrète client enregistrée en tant que **Clé secrète client** de l’application Amazon créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Amazon.

