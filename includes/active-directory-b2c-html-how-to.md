---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80116889"
---
## <a name="use-custom-page-content"></a>Utiliser un contenu de page personnalisé

Avec la fonctionnalité de personnalisation de l’interface utilisateur de la page, vous pouvez personnaliser l’apparence d’une stratégie personnalisée. Vous pouvez également conserver la cohérence visuelle et de la marque entre votre application et Azure AD B2C.

### <a name="how-it-works"></a>Fonctionnement

Azure AD B2C exécute le code dans le navigateur de votre client à l’aide de [Partage des ressources cross-origin (CORS)](https://www.w3.org/TR/cors/). Au moment de l’exécution, le contenu est chargé depuis une URL que vous spécifiez dans votre flux utilisateur ou stratégie personnalisée. Chaque page de l’expérience utilisateur charge son contenu à partir de l’URL que vous spécifiez pour cette page. Une fois le contenu chargé à partir de votre URL, il est fusionné avec un fragment HTML inséré par Azure AD B2C, puis la page est présentée à votre client.

![Marge du contenu de page personnalisé](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Contenu de page HTML personnalisé

Créez une page HTML avec votre propre personnalisation pour servir le contenu de votre page personnalisée. Cette page peut être une page `*.html` statique ou une page dynamique comme .NET, Node.js ou PHP.

Le contenu de votre page personnalisée peut contenir tous les éléments HTML, y compris CSS et JavaScript, mais ne peut pas inclure d’éléments non sécurisés tels que des IFrames. Le seul élément requis dans votre page HTML est un élément div dont `id` est défini sur `api`, `<div id="api"></div>` par exemple.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personnaliser les pages Azure AD B2C par défaut

Au lieu de créer le contenu de votre page personnalisée à partir de zéro, vous pouvez personnaliser le contenu de la page par défaut d’Azure AD B2C.

Le tableau suivant répertorie le contenu de page par défaut fourni par Azure AD B2C. Téléchargez les fichiers et utilisez-les comme point de départ pour créer vos propres pages personnalisées.

| Page par défaut | Description | ID de définition du contenu<br/>(stratégie personnalisée uniquement) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Page d’erreur**. Cette page s’affiche lorsqu’une exception ou une erreur est rencontrée. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Page autodéclarée**. Utilisez ce fichier en tant que contenu de page personnalisé pour une page d’inscription à un compte de réseau social, une page d’inscription à un compte local, une page de connexion à un compte local, une page de réinitialisation du mot de passe et bien plus encore. Le formulaire peut contenir différentes commandes de saisie telles qu’une zone de saisie de texte, une zone de saisie de mot de passe, un bouton radio, des zones de liste déroulante à sélection unique ou des cases à sélection multiples. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Page d’authentification multi-facteur**. Cette page permet aux utilisateurs de vérifier leurs numéros de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Page de mise à jour de profil**. Cette page contient un formulaire auquel l’utilisateur peut accéder pour mettre à jour son profil. Cette page est similaire à la page d’inscription au compte de réseau social, à l’exception des champs de saisie de mot de passe. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Page de connexion ou d’inscription unifiée**. Cette page gère le processus d’inscription et de connexion des utilisateurs. Les utilisateurs peuvent utiliser des fournisseurs d’identité d’entreprise, des fournisseurs d’identité de réseaux sociaux tels que Facebook et Google + ou des comptes locaux. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hébergement du contenu de la page

Lorsque vous utilisez vos propres fichiers HTML et CSS pour personnaliser l’interface utilisateur, hébergez votre contenu d’interface utilisateur sur n’importe quel point de terminaison HTTPS disponible publiquement qui prend en charge CORS. Par exemple, [Stockage Blob Azure](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App Services](/azure/app-service/), serveurs web, réseaux de distribution de contenu, AWS S3 ou systèmes de partage de fichiers.

## <a name="guidelines-for-using-custom-page-content"></a>Instructions pour l’utilisation de contenu de page personnalisé

- Utilisez une URL absolue lorsque vous incluez des ressources externes telles que des fichiers multimédias, CSS et JavaScript dans votre fichier HTML.
- Avec la [version 1.2.0 de la mise en page](../articles/active-directory-b2c/page-layout.md) et les suivantes, vous pouvez ajouter l’attribut `data-preload="true"` dans vos balises HTML pour contrôler l’ordre de chargement des fichiers CSS et JavaScript. Grâce à `data-preload=true`, la page est construite avant d’être affichée à l’utilisateur. Cet attribut permet d’éviter le « scintillement » de la page en préchargeant le fichier CSS, sans que le code HTML non stylisé soit affiché à l’utilisateur. L’extrait de code HTML suivant montre l’utilisation de la balise `data-preload`.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Nous vous recommandons de commencer par le contenu de la page par défaut et de créer par-dessus.
- Vous pouvez inclure du code JavaScript dans votre contenu personnalisé pour les [flux utilisateur](../articles/active-directory-b2c/user-flow-javascript-overview.md) et les [stratégies personnalisées](../articles/active-directory-b2c/javascript-samples.md).
- Les versions de navigateur prises en charge sont les suivantes :
  - Internet Explorer 11, 10 et Microsoft Edge
  - Prise en charge limitée pour Internet Explorer 9 et 8
  - Google Chrome 42.0 et ultérieur
  - Mozilla Firefox 38.0 et ultérieur
  - Safari pour iOS et macOS, version 12 et versions ultérieures
- En raison des restrictions de sécurité, Azure AD B2C ne prend pas en charge les éléments HTML `frame`, `iframe` ni `form`.

## <a name="custom-page-content-walkthrough"></a>Procédure pas à pas pour le contenu de page personnalisé

Voici une vue d’ensemble du processus :

1. Préparez un emplacement pour héberger le contenu de votre page personnalisée (un point de terminaison HTTPS accessible publiquement et compatible avec CORS).
1. Téléchargez et personnalisez un fichier de contenu de page par défaut, par exemple `unified.html`.
1. Publiez le contenu de votre page personnalisée sur votre point de terminaison HTTPS disponible publiquement.
1. Définir un partage de ressources cross-origin (CORS) pour votre application web.
1. Pointez votre stratégie vers votre URI de contenu de stratégie personnalisé.

### <a name="1-create-your-html-content"></a>1. Créer votre contenu HTML

Créez un contenu de page personnalisé dont le titre intègre le nom de la marque de votre produit.

1. Copiez l’extrait de code HTML suivant. Il s’agit de code HTML5 bien formé comportant un élément vide appelé *\<div id="api"\>\</div\>* , situé dans les balises *\<body\>* . Cet élément indique l’endroit où le contenu Azure AD B2C doit être inséré.

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

1. Collez l’extrait de code que vous venez de copier dans un éditeur de texte, puis enregistrez le fichier sous *customize-ui.html*.

> [!NOTE]
> Les éléments de formulaire HTML seront supprimés à cause des restrictions de sécurité si vous utilisez login.microsoftonline.com. Si vous souhaitez utiliser des éléments de formulaire HTML dans votre contenu HTML personnalisé, [utilisez b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Créer un compte de stockage d’objets blob Azure

Dans cet article, nous utilisons le stockage Blob Azure pour héberger notre contenu. Vous pouvez choisir d’héberger votre contenu sur un serveur web, mais vous devez [activer CORS sur votre serveur web](https://enable-cors.org/server.html).

Pour héberger votre contenu HTML dans Stockage Blob, effectuez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu **Hub**, sélectionnez **Nouveau** > **Stockage** > **Compte de stockage**.
1. Sélectionnez un **abonnement** pour votre compte de stockage.
1. Créez un **Groupe de ressources** ou sélectionnez-en un.
1. Entrez un **nom** unique pour votre compte de stockage.
1. Sélectionnez un **emplacement géographique** pour votre compte de stockage.
1. Le champ **Modèle de déploiement** peut conserver la valeur **Gestionnaire de ressources**.
1. Le champ **Performances** peut conserver la valeur **Standard**.
1. Modifiez **Type de compte** sur **Stockage Blob**.
1. Le champ **Réplication** peut conserver la valeur **RA-GRS**.
1. Le champ **Niveau d’accès** peut conserver la valeur **À chaud**.
1. Sélectionnez **Vérifier + créer** pour créer le compte de stockage.
    Une fois le déploiement terminé, la page **Compte de stockage** s’ouvre automatiquement.

#### <a name="21-create-a-container"></a>2.1 Créer un conteneur

Pour créer un conteneur public dans Stockage Blob, effectuez les étapes suivantes :

1. Sous **Service Blob** dans le menu de gauche, sélectionnez **Objets Blob**.
1. Sélectionnez **+ Conteneur**.
1. Pour **Nom**, entrez *root*. Il peut s’agir du nom de votre choix, par exemple *contoso*, mais nous utilisons *root* dans cet exemple par souci de simplicité.
1. Pour **Niveau d’accès public**, sélectionnez **Objet blob**, puis **OK**.
1. Sélectionnez **root** pour ouvrir le nouveau conteneur.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Charger vos fichiers de contenu de page personnalisé

1. Sélectionnez **Télécharger**.
1. Sélectionnez l’icône de dossier à côté du champ **Sélectionner un fichier**.
1. Accédez au fichier **customize-ui.html** que vous avez précédemment créé dans la section Personnalisation de l’interface utilisateur de la page, et sélectionnez-le.
1. Si vous souhaitez le charger dans un sous-dossier, développez **Avancé** et entrez un nom de dossier dans **Charger dans le dossier**.
1. Sélectionnez **Télécharger**.
1. Sélectionnez l’objet blob **customize-ui.html** que vous venez de charger.
1. À droite de la zone de texte **URL**, sélectionnez l’icône **Copier dans le Presse-papiers** pour copier l’URL dans le Presse-papiers.
1. Dans le navigateur web, accédez à l’URL que vous avez copiée pour vérifier que l’objet blob que vous avez chargé est accessible. S’il est inaccessible, par exemple si vous rencontrez une erreur `ResourceNotFound`, assurez-vous que le type d’accès du conteneur est défini sur **objet blob**.

### <a name="3-configure-cors"></a>3. Configuration de CORS

Configurez Stockage Blob pour le Partage des ressources cross-origin en effectuant les étapes suivantes :

1. Dans le menu, sélectionnez **CORS**.
1. Pour **Origines autorisées**, entrez `https://your-tenant-name.b2clogin.com`. Remplacez `your-tenant-name` par le nom de votre locataire Azure AD B2C. Par exemple : `https://fabrikam.b2clogin.com`. Utilisez des minuscules quand vous entrez le nom de votre locataire.
1. Pour **Méthodes autorisées**, sélectionnez `GET` et `OPTIONS`.
1. Pour **En-têtes autorisés**, saisissez un astérisque (*).
1. Pour **En-têtes exposés**, saisissez un astérisque (*).
1. Pour **Âge maximal**, tapez 200.
1. Sélectionnez **Enregistrer**.

#### <a name="31-test-cors"></a>3.1 Tester CORS

Vérifiez que vous êtes prêt en effectuant les étapes suivantes :

1. Répétez l’étape Configuration de CORS. Dans **Origines autorisées**, entrez `https://www.test-cors.org`.
1. Accédez à [www.test-cors.org](https://www.test-cors.org/). 
1. Dans la zone **URL distante**, collez l’URL de votre fichier HTML. Par exemple : `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Sélectionnez **Envoyer une demande**.
    Le résultat devrait être `XHR status: 200`. 
    Si vous recevez une erreur, assurez-vous que vos paramètres CORS sont corrects. Vous serez peut-être amené à vider le cache de votre navigateur ou à ouvrir une fenêtre de navigation privée, en appuyant sur Ctrl+Maj+P.
