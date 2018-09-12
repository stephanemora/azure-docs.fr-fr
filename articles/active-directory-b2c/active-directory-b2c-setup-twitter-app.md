---
title: Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant d’un compte Twitter dans vos applications avec Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6d8e9245e95c08aad69cd05f338b6260e554469b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337788"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Twitter à l’aide d’Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Création d'une application Twitter

Pour utiliser un compte Twitter comme fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application qui la représente dans votre locataire (tenant). Si vous ne disposez pas déjà d’un compte Twitter, vous pouvez en créer un à l’adresse [https://twitter.com/signup](https://twitter.com/signup).

1. Connectez-vous aux [Applications Twitter](https://apps.twitter.com/) avec vos informations d’identification Twitter.
2. Sélectionnez **Create New App** (Créer une application).
3. Entrez un **Nom**, une **Description** et un **Site web**.
4. Entrez `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp` dans **URL de rappel**. Remplacez **{locataire}** par le nom de votre locataire (par exemple, contosob2c), et **{policyId}** par votre ID de stratégie (par exemple, b2c_1_policy). Ajoutez une URL de rappel pour toutes les stratégies qui utilisent le compte Twitter. 
5. Acceptez le **Contrat du développeur** et sélectionnez **Créer votre application Twitter**.
7. Sélectionnez l’onglet **Keys and Access Tokens** .
8. Copiez les informations de **Consumer Key** (Clé du client) et de **Consumer Secret** (Secret du client). Vous aurez besoin de ces deux valeurs pour configurer un compte Twitter en tant que fournisseur d’identité dans votre locataire.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurer Twitter en tant que fournisseur d’identité dans votre locataire

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Donnez-lui un **Nom**. Par exemple, entrez *Twitter*.
6. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Twitter**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité** et entrez la clé du consommateur comme **ID client** et le **Secret du consommateur** comme **Clé secrète client**.
8. Cliquez sur **OK**, puis sur **Create** (Créer) pour enregistrer votre configuration Twitter.