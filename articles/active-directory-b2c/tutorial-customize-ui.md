---
title: 'Tutoriel : Personnaliser l’interface utilisateur de vos applications dans Azure Active Directory B2C | Microsoft Docs'
description: Découvrez comment personnaliser l’interface utilisateur de vos applications dans Azure Active Directory B2C à l’aide du portail Azure.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1c95772eeb6057b4ff7b12a79897fda73e1e017c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156653"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Tutoriel : Personnaliser l’interface utilisateur de vos applications dans Azure Active Directory B2C

Pour des expériences utilisateur plus courantes telles que l’inscription, la connexion et la modification du profil, vous pouvez utiliser des [flux d’utilisateur](active-directory-b2c-reference-policies.md) dans Azure Active Directory (Azure AD) B2C. Les informations contenues dans ce tutoriel vous aideront à découvrir comment [personnaliser l’interface utilisateur](customize-ui-overview.md) de ces expériences à l’aide de vos propres fichiers HTML et CSS.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer des fichiers de personnalisation de l’interface utilisateur
> * Créer un flux d’utilisateur d’inscription et de connexion qui utilise les fichiers
> * Tester l’interface utilisateur personnalisée

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore créé votre propre [locataire Azure AD B2C](tutorial-create-tenant.md), créez-en un maintenant. Vous pouvez utiliser un locataire existant si vous en avez créé un dans un tutoriel précédent.

## <a name="create-customization-files"></a>Créer des fichiers de personnalisation

Vous créez un conteneur et un compte de stockage Azure, puis vous placez les fichiers HTML et CSS de base dans le conteneur.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Bien que vous puissiez stocker vos fichiers de plusieurs façons, pour les besoins de ce tutoriel vous allez les stocker dans le [stockage d’objets blob Azure](../storage/blobs/storage-blobs-introduction.md).

1. Veillez à bien utiliser l’annuaire qui contient votre abonnement Azure. Sélectionnez le **filtre Répertoire et abonnement** dans le menu supérieur et sélectionnez l’annuaire qui contient votre abonnement. Cet annuaire est différent de celui qui contient votre locataire Azure B2C.

    ![Basculer vers l’annuaire de l’abonnement](./media/tutorial-customize-ui/switch-directories.png)

2. Choisissez Tous les services dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Comptes de stockage**. 
3. Sélectionnez **Ajouter**.
4. Sous **Groupe de ressources**, sélectionnez **Créer**, entrez un nom pour le nouveau groupe de ressources, puis cliquez sur **OK**.
5. Nommez le compte de stockage. Le nom que vous choisissez doit être unique dans Azure, et contenir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
6. Sélectionnez l’emplacement du compte de stockage ou acceptez l’emplacement par défaut. 
7. Acceptez toutes les autres valeurs par défaut, sélectionnez **Vérifier + créer**, puis cliquez sur **Créer**.
8. Une fois le compte de stockage créé, sélectionnez **Accéder à la ressource**.

### <a name="create-a-container"></a>Créez un conteneur.

1. Dans la page de vue d’ensemble du compte de stockage, sélectionnez **Objets blob**.
2. Sélectionnez **Conteneur**, entrez un nom pour le conteneur, choisissez **Blob (accès en lecture anonyme pour les objets blob uniquement)**, puis cliquez sur **OK**.

### <a name="enable-cors"></a>Activez CORS

 Le code Azure AD B2C dans un navigateur utilise une approche moderne et standard pour charger le contenu personnalisé à partir d’une URL que vous spécifiez dans un flux d’utilisateur. Le partage des ressources Cross-Origin (CORS) permet de demander des ressources restreintes dans une page web à partir d’autres domaines.

1. Dans le menu, sélectionnez **CORS**.
2. Pour **Origines autorisées**, entrez `https://your-tenant-name.b2clogin.com`. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Par exemple : `https://fabrikam.b2clogin.com`. Vous devez utiliser des minuscules quand vous entrez le nom de votre locataire.
3. Pour **Méthodes autorisées**, sélectionnez `GET` et `OPTIONS`.
4. Pour **En-têtes autorisés**, saisissez un astérisque (*).
5. Pour **En-têtes exposés**, saisissez un astérisque (*).
6. Pour **Âge maximal**, tapez 200.

    ![Activez CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Cliquez sur **Enregistrer**.

### <a name="create-the-customization-files"></a>Créer les fichiers de personnalisation

Pour personnaliser l’interface utilisateur de l’expérience d’inscription, commencez par créer un simple fichier HTML et CSS. Vous pouvez configurer votre code HTML comme vous le souhaitez, mais il doit avoir un élément **div** avec `api` comme identificateur. Par exemple : `<div id="api"></div>`. Azure AD B2C injecte des éléments dans le conteneur `api` quand la page s’affiche.

1. Dans un dossier local, créez le fichier suivant en n’oubliant pas de remplacer `your-storage-account` par le nom du compte de stockage et `your-container` par le nom du conteneur que vous avez créé. Par exemple : `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Vous pouvez concevoir la page comme bon vous semble, mais l’élément div **api** est obligatoire pour tout fichier de personnalisation HTML que vous créez. 

3. Enregistrez le fichier sous *custom-ui.html*.
4. Créez la feuille CSS simple suivante, qui centre tous les éléments dans la page d’inscription ou de connexion, y compris les éléments injectés par Azure AD B2C.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Enregistrez le fichier sous *style.css*.

### <a name="upload-the-customization-files"></a>Charger les fichiers de personnalisation

Dans ce tutoriel, vous stockez les fichiers que vous avez créés dans le compte de stockage afin qu’Azure AD B2C puisse y accéder.

1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Comptes de stockage**.
2. Sélectionnez le compte de stockage que vous avez créé, sélectionnez **Blobs**, puis sélectionnez le conteneur que vous avez créé.
3. Sélectionnez **Charger**, accédez au fichier *custom-ui.html* et sélectionnez-le, puis cliquez sur **Charger**.

    ![Charger des fichiers de personnalisation](./media/tutorial-customize-ui/upload-blob.png)

4. Copiez l’URL du fichier que vous avez chargé afin de l’utiliser ultérieurement dans ce tutoriel.
5. Répétez les étapes 3 et 4 pour le fichier *style.css*.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription et de connexion

Pour effectuer les dernières étapes de ce tutoriel, vous devez créer une application test et un flux d’utilisateur d’inscription ou de connexion dans Azure AD B2C. Vous pouvez appliquer les principes décrits dans ce tutoriel aux autres expériences utilisateur, telles que la modification de profil.

### <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue par le biais d’une application que vous créez dans votre locataire. Les étapes suivantes créent une application qui redirige le jeton d’autorisation retourné vers [https://jwt.ms](https://jwt.ms).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application (par exemple, *testapp1*).
6. Pour **Application/API web**, sélectionnez `Yes`, puis entrez `https://jwt.ms` pour l’**URL de réponse**.
7. Cliquez sur **Créer**.

### <a name="create-the-user-flow"></a>Créer le flux d’utilisateur

Pour tester vos fichiers de personnalisation, vous créez un flux d’utilisateur d’inscription ou de connexion intégré qui utilise l’application créée précédemment.

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**, puis cliquez sur **Nouveau flux d’utilisateur**.
2. Sous l’onglet **Recommandé**, cliquez sur **Inscription et connexion**.
3. Entrez un nom pour le flux d’utilisateur. Par exemple, *signup_signin*. Le préfixe *B2C_1* est ajouté automatiquement au nom lors de la création du flux d’utilisateur.
4. Sous **Fournisseurs d’identité**, sélectionnez **Inscription par courrier électronique**.
5. Sous **Attributs utilisateur et revendications**, cliquez sur **Afficher plus**.
6. Dans la colonne **Collecter l’attribut**, choisissez les attributs que vous souhaitez collecter auprès du client lors de l’inscription. Par exemple, définissez **Pays/région**, **Nom d’affichage** et **Code postal**.
7. Dans la colonne **Revendication de retour**, choisissez les revendications à retourner à votre application dans les jetons d’authentification après une expérience d’inscription ou de connexion réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal**, **Nouvel utilisateur** et **ID d’objet de l’utilisateur**.
8. Cliquez sur **OK**.
9. Cliquez sur **Créer**.
10. Sous **Personnaliser**, sélectionnez **Mises en page**. Sélectionnez **Page unifiée d’inscription ou de connexion**, puis cliquez sur **Oui** pour **Utiliser un contenu de page personnalisé**.
11. Dans **URI la page personnalisée**, entrez l’URL du fichier *custom-ui.html* notée précédemment.
12. En haut de la page, cliquez sur **Enregistrer**.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateur**, puis sélectionnez le flux d’utilisateur que vous avez créé. Par exemple, *B2C_1_signup_signin*.
2. En haut de la page, cliquez sur **Exécuter le flux d’utilisateur**.
3. Cliquez sur le bouton **Exécuter le flux d’utilisateur**.

    ![Exécuter un flux d’utilisateur d’inscription ou de connexion](./media/tutorial-customize-ui/run-user-flow.png)

    Vous devez voir une page semblable à l’exemple suivant avec les éléments centrés conformément au fichier CSS que vous avez créé :

    ![Résultats du flux d’utilisateur](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des fichiers de personnalisation de l’interface utilisateur
> * Créer un flux d’utilisateur d’inscription et de connexion qui utilise les fichiers
> * Tester l’interface utilisateur personnalisée

> [!div class="nextstepaction"]
> [Personnalisation de la langue dans Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
