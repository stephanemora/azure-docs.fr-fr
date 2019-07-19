---
title: Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C | Microsoft Docs
description: Apprenez à personnaliser une interface utilisateur à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a051b0e853b60dfc1f5b6c3453d9ed8361f1748
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438815"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Après avoir suivi cet article, vous disposerez d’une stratégie personnalisée d’inscription et de connexion avec votre marque et votre apparence. Avec Azure Active Directory B2C (Azure AD B2C), vous contrôlerez presque entièrement le contenu HTML et CSS présenté aux utilisateurs. Lorsque vous utilisez une stratégie personnalisée, vous configurez la personnalisation de l’interface utilisateur dans le code XML au lieu d’utiliser des contrôles dans le portail Azure. 

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). Vous devez disposer d’une stratégie personnalisée fonctionnelle pour l’inscription et la connexion avec des comptes locaux.

## <a name="page-ui-customization"></a>Personnalisation de l’interface utilisateur de la page

Avec la fonctionnalité de personnalisation de l’interface utilisateur de la page, vous pouvez personnaliser l’apparence d’une stratégie personnalisée. Vous pouvez également conserver la cohérence visuelle et de la marque entre votre application et Azure AD B2C.

Voici comment cela fonctionne : Azure AD B2C exécute le code dans le navigateur de votre client et adopte une approche moderne appelée [Partage des ressources cross-origin (CORS)](https://www.w3.org/TR/cors/). Tout d’abord, vous spécifiez une URL dans la stratégie personnalisée avec du contenu HTML personnalisé. Azure AD B2C fusionne des éléments de l’interface utilisateur avec le contenu HTML chargé depuis votre URL, puis affiche la page au client.

## <a name="create-your-html5-content"></a>Créer votre contenu HTML5

Créez du contenu HTML dont le titre intègre le nom de la marque de votre produit.

1. Copiez l’extrait de code HTML suivant. Cet extrait est un code HTML5 bien formé contenant un élément vide appelé *\<div id="api"\>\</div\>* qui se situe entre les balises *\<body\>* . Cet élément indique l’endroit où le contenu Azure AD B2C doit être inséré.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

2. Collez l’extrait de code que vous venez de copier dans un éditeur de texte, puis enregistrez le fichier sous *customize-ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Créer un compte de stockage d’objets blob Azure

>[!NOTE]
> Dans cet article, nous utilisons le stockage Blob Azure pour héberger notre contenu. Vous pouvez choisir d’héberger votre contenu sur un serveur web, mais vous devez [activer CORS sur votre serveur web](https://enable-cors.org/server.html).

Pour héberger ce contenu HTML dans le stockage Blob, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Nouveau** > **Stockage** > **Compte de stockage**.
3. Entrez un **nom** unique pour votre compte de stockage.
4. Le champ **Modèle de déploiement** peut conserver la valeur **Gestionnaire de ressources**.
5. Modifiez **Type de compte** sur **Stockage Blob**.
6. Le champ **Performances** peut conserver la valeur **Standard**.
7. Le champ **Réplication** peut conserver la valeur **RA-GRS**.
8. Le champ **Niveau d’accès** peut conserver la valeur **À chaud**.
9. Le champ **Chiffrement du service de stockage** peut conserver la valeur **Désactivé**.
10. Sélectionnez un **abonnement** pour votre compte de stockage.
11. Créez un **Groupe de ressources** ou sélectionnez-en un.
12. Sélectionnez un **emplacement géographique** pour votre compte de stockage.
13. Cliquez sur **Créer** pour créer le compte de stockage.  
    Une fois le déploiement terminé, le panneau **Compte de stockage** s’ouvre automatiquement.

## <a name="create-a-container"></a>Créez un conteneur.

Pour créer un conteneur public dans le stockage Blob, procédez comme suit :

1. Sous **Service Blob** dans le menu de gauche, sélectionnez **Objets Blob**.
2. Cliquez sur **+Conteneur**.
3. Pour **Nom**, entrez *root*. Cela peut être un nom de votre choix, par exemple *wingtiptoys*, mais nous utilisons *root* dans cet exemple par souci de simplicité.
4. Pour **Niveau d’accès public**, sélectionnez **Objet blob**, puis **OK**.
5. Cliquez sur **root** pour ouvrir le nouveau conteneur.
6. Cliquez sur **Télécharger**.
7. Cliquez sur l’icône de dossier en regard du champ **Sélectionner un fichier**.
8. Accédez au fichier **customize-ui.html** que vous avez précédemment créé dans la section Personnalisation de l'interface utilisateur de la page, et sélectionnez-le.
9. Si vous souhaitez le charger dans un sous-dossier, développez **Avancé** et entrez un nom de dossier dans **Charger dans le dossier**.
10. Sélectionnez **Télécharger**.
11. Sélectionnez l’objet blob **customize-ui.html** que vous venez de charger.
12. À droite de la zone de texte **URL**, sélectionnez l’icône **Copier dans le Presse-papiers** pour copier l’URL dans le Presse-papiers.
13. Dans le navigateur web, accédez à l’URL que vous avez copiée pour vérifier que l’objet blob que vous avez chargé est accessible. S’il est inaccessible, par exemple si vous rencontrez une erreur `ResourceNotFound`, assurez-vous que le type d’accès du conteneur est défini sur **objet blob**.

## <a name="configure-cors"></a>Configuration de CORS

Configurez le stockage Blob pour le partage des ressources cross-origin en procédant comme suit :

1. Dans le menu, sélectionnez **CORS**.
2. Pour **Origines autorisées**, entrez `https://your-tenant-name.b2clogin.com`. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Par exemple : `https://fabrikam.b2clogin.com`. Vous devez utiliser des minuscules quand vous entrez le nom de votre locataire.
3. Pour **Méthodes autorisées**, sélectionnez `GET` et `OPTIONS`.
4. Pour **En-têtes autorisés**, saisissez un astérisque (*).
5. Pour **En-têtes exposés**, saisissez un astérisque (*).
6. Pour **Âge maximal**, tapez 200.
7. Cliquez sur **Enregistrer**.

## <a name="test-cors"></a>Tester CORS

Vérifiez que vous êtes prêt en procédant comme suit :

1. Accédez au site web [www.test-cors.org](https://www.test-cors.org/), puis collez l’URL dans le champ **URL distante**.
2. Cliquez sur **Envoyer la demande**.  
    Si vous recevez une erreur, assurez-vous que vos [paramètres CORS](#configure-cors) sont corrects. Vous serez peut-être amené à vider le cache de votre navigateur ou à ouvrir une fenêtre de navigation privée, en appuyant sur Ctrl+Maj+P.

## <a name="modify-the-extensions-file"></a>Modifier le fichier d’extensions

Pour configurer la personnalisation de l’interface utilisateur, copiez l’élément **ContentDefinition** et ses éléments enfants entre le fichier de base et le fichier d’extension.

1. Ouvrez le fichier de base de votre stratégie. Par exemple, ouvrez *TrustFrameworkBase.xml*.
2. Recherchez et copiez le contenu entier de l’élément **ContentDefinitions**.
3. Ouvrez le fichier d’extension. Par exemple, *TrustFrameworkExtensions.xml*. Recherchez l’élément **BuildingBlocks**. Si l’élément n’existe pas, ajoutez-le.
4. Collez le contenu entier de l’élément **ContentDefinitions** que vous avez copié en tant qu’enfant de l’élément **BuildingBlocks**. 
5. Recherchez l’élément **ContentDefinition** contenant `Id="api.signuporsignin"` dans le code XML que vous avez copié.
6. Remplacez la valeur de **LoadUri** par l’URL du fichier HTML que vous avez chargé dans le stockage. Par exemple : `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
    Votre stratégie personnalisée doit ressembler à ce qui suit :

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. Enregistrez le fichier d’extensions.

## <a name="upload-your-updated-custom-policy"></a>Téléchargement de votre stratégie personnalisée mise à jour

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Infrastructure d’expérience d’identité**.
2. Cliquez sur **Toutes les stratégies**.
3. Cliquez sur **Charger la stratégie**.
4. Chargez le fichier d’extensions que vous avez modifié précédemment.

## <a name="test-the-custom-policy-by-using-run-now"></a>Tester la stratégie personnalisée à l’aide de l’option **Exécuter maintenant**

1. À partir du panneau **Azure AD B2C**, accédez à **Toutes les stratégies**.
2. Sélectionnez la stratégie personnalisée que vous avez téléchargée, puis cliquez sur le bouton **Exécuter maintenant**.
3. Vous devriez pouvoir vous inscrire avec une adresse e-mail.

## <a name="reference"></a>Informations de référence

### <a name="sample-templates"></a>Exemples de modèles
Vous trouverez ici des exemples de modèles pour la personnalisation de l’interface utilisateur :

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Le dossier sample_templates/wingtip contient les fichiers HTML suivants :

| Modèle HTML5 | Description |
|----------------|-------------|
| *phonefactor.html* | Utilisez ce fichier en tant que modèle pour une page d’authentification multifacteur. |
| *resetpassword.html* | Utilisez ce fichier en tant que modèle pour une page relative à un mot de passe oublié. |
| *selfasserted.html* | Utilisez ce fichier en tant que modèle pour une page d’inscription à un compte de réseau social, une page d’inscription à un compte local ou une page de connexion à un compte local. |
| *unified.html* | Utilisez ce fichier en tant que modèle pour une page de connexion ou d’inscription unifiée. |
| *updateprofile.html* | Utilisez ce fichier en tant que modèle pour une page de mise à jour de profil. |

Voici les étapes indiquant comment utiliser l’exemple. 
1. Clonez le référentiel sur votre ordinateur local. Choisissez un dossier de modèle sous sample_templates. Vous pouvez utiliser `wingtip` ou `contoso`.
2. Chargez tous les fichiers sous les dossiers `css`, `fonts` et `images` vers le stockage d’objets blob, comme décrit dans les sections précédentes. 
3. Ensuite, ouvrez chaque fichier \*.html dans le dossier racine `wingtip` ou `contoso` (selon celui que vous avez sélectionné dans la première étape) et remplacez toutes les instances de « http://localhost  » par les URL des fichiers css, images et de polices que vous avez téléchargés à l’étape 2.
4. Enregistrez les fichiers \*.html et chargez-les dans le stockage d’objets blob.
5. À présent, modifiez le fichier d’extensions, comme indiqué précédemment dans [Modifier le fichier d’extensions](#modify-the-extensions-file).
6. Si vous voyez des polices, des images ou des feuilles de style CSS manquants, vérifiez vos références dans la stratégie d’extensions et les fichiers \*.html.

### <a name="content-defintion-ids"></a>ID de définition de contenu

À la section Modifier votre stratégie personnalisée d'inscription ou de connexion, vous avez configuré la définition de contenu de `api.idpselections`. L’ensemble des identifiants de définition du contenu reconnus par l’infrastructure d’expérience d’identité Azure AD B2C et leurs descriptions sont regroupés dans le tableau suivant :

| ID de définition du contenu | Description | 
|-----------------------|-------------|
| *api.error* | **Page d’erreur**. Cette page s’affiche lorsqu’une exception ou une erreur est rencontrée. |
| *api.idpselections* | **Page de sélection du fournisseur d’identité**. Cette page contient une liste de fournisseurs d’identité parmi lesquels l’utilisateur peut faire son choix lors de la connexion. Il s’agit de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| *api.idpselections.signup* | **Sélection du fournisseur d’identité pour l’inscription**. Cette page contient une liste de fournisseurs d’identité parmi lesquels l’utilisateur peut faire son choix lors de l’inscription. Il s’agit de fournisseurs d’identité d’entreprise, de fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou de comptes locaux. |
| *api.localaccountpasswordreset* | **Page de mot de passe oublié**. Cette page contient un formulaire que l’utilisateur doit remplir pour lancer une réinitialisation de mot de passe.  |
| *api.localaccountsignin* | **Page de connexion à un compte local**. Cette page contient un formulaire de connexion que l’utilisateur doit renseigner lors de la connexion à un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir une zone de saisie de texte et une zone de saisie de mot de passe. |
| *api.localaccountsignup* | **Page d’inscription à un compte local**. Cette page contient un formulaire d’inscription que l’utilisateur doit renseigner lors de la connexion à un compte local basé sur une adresse e-mail ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles que la zone de saisie de texte, celle du mot de passe, un bouton radio, les zones de liste déroulante à sélection unique ou les cases à sélection multiples. |
| *api.phonefactor* | **Page d’authentification multi-facteur**. Cette page permet aux utilisateurs de vérifier leurs numéros de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. |
| *api.selfasserted* | **Page d’inscription à un compte de réseau social**. Cette page contient un formulaire d’inscription que l’utilisateur doit remplir lors de l’inscription à l’aide d’un compte existant proposé par un fournisseur d’identité de réseaux sociaux tel que Facebook ou Google +. Cette page est similaire à la page d’inscription au compte de réseau social ci-dessus, à l’exception des champs de saisie de mot de passe. |
| *api.selfasserted.profileupdate* | **Page de mise à jour de profil**. Cette page contient un formulaire dont l’utilisateur peut se servir pour mettre à jour son profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. |
| *api.signuporsignin* | **Page de connexion ou d’inscription unifiée**. Cette page gère l’inscription et la connexion des utilisateurs, qui peuvent utiliser les fournisseurs d’identité d’entreprise ou de réseaux sociaux, tels que Facebook ou Google+, ou de comptes locaux.  |

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus les éléments personnalisables de l’interface utilisateur, lisez le [guide de référence relatif à la personnalisation de l’interface utilisateur pour des stratégies intégrées](active-directory-b2c-reference-ui-customization.md).
