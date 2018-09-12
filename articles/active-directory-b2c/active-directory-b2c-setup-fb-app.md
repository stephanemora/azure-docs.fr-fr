---
title: Configurer l’inscription et la connexion avec un compte Facebook à l’aide d’Azure Active Directory B2C | Microsoft Docs
description: Proposez l’inscription et la connexion aux clients disposant de comptes Facebook dans vos applications à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 985285b463d66770f97a431705d5b9198b632592
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344604"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurer l’inscription et la connexion avec un compte Facebook à l’aide d’Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Création d’une application Facebook

Pour utiliser un compte Facebook en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application dans votre locataire qui la représente. Si vous n’avez pas encore de compte Facebook, vous pouvez en obtenir un à l’adresse [https://www.facebook.com/](https://www.facebook.com/).

1. Connectez-vous à [Facebook pour les développeurs](https://developers.facebook.com/) avec les informations d’identification de votre compte Facebook.
2. Si ce n’est pas déjà fait, vous devez vous inscrire en tant que développeur Facebook. Pour cela, sélectionnez **S’inscrire** dans le coin supérieur droit de la page, acceptez les politiques de Facebook et suivez les étapes de l’inscription.
3. Sélectionnez **Mes applications**, puis cliquez sur **Ajouter une nouvelle application**. 
4. Entrez un **nom d’affichage** et une **adresse e-mail de contact** valide.
5. Cliquez sur **Créer l’ID d’application**. Vous devrez peut-être accepter les politiques de la plateforme Facebook et effectuer une vérification de sécurité en ligne.
6. Sélectionnez **Paramètres** > **Base**.
7. Au bas de la page, sélectionnez **Ajouter une plateforme**, puis sélectionnez **Site web**.
8. Entrez `https://{tenantname}.b2clogin.com/` dans l’**URL du site**. Entrez une URL pour l’**URL de stratégie de confidentialité**, par exemple `http://www.contoso.com`.
9. Sélectionnez **Enregistrer les modifications**.
11. En haut de la page, copiez la valeur de l’**ID de l’application**. 
12. Cliquez sur **Afficher**, puis copiez la valeur **Clé secrète de l’application**. Vous avez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre client. **App Secret** est une information d’identification de sécurité importante.
13. Sélectionnez **Produits**, puis sélectionnez **Configurer** sous **Connexion Facebook**.
14. Sélectionnez **Paramètres** sous **Connexion Facebook**.
15. Entrez `https://{tenantname}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` dans **URI de redirection OAuth valides**. Remplacez **{locataire}** par le nom de votre locataire (par exemple, contosob2c). Cliquez sur **Save Changes** en bas de la page.
16. Pour rendre votre application Facebook disponible dans Azure AD B2C, sélectionnez **Révision de l’application**, définissez **Rendre mon application publique ?** sur **OUI**, choisissez une catégorie, par exemple `Business and Pages`, puis cliquez sur **Confirmer**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configuration d’un compte Facebook en tant que fournisseur d’identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Assurez-vous que vous utilisez le répertoire qui contient votre locataire Azure AD B2C en l’activant dans l’angle supérieur droit du portail Azure. Sélectionnez les informations sur votre abonnement, puis cliquez sur **Changer de répertoire**. 

    ![Basculez vers votre client Azure AD B2C.](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Choisissez le répertoire qui contient votre locataire.

    ![Sélectionner le répertoire](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
5. Entrez un **nom**. Par exemple, entrez *Facebook*.
6. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Facebook**, puis cliquez sur **OK**.
7. Sélectionnez **Configurer ce fournisseur d’identité**, entrez l’identifiant d’application enregistrée précédemment en tant **qu’ID Client** et entrez le secret d’application enregistré en tant que **Clé secrète client** de l’application Facebook créée précédemment.
8. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Facebook.