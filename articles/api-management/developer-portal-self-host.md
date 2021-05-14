---
title: Auto-héberger le portail des développeurs
titleSuffix: Azure API Management
description: Découvrez comment auto-héberger le portail des développeurs Gestion des API.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e63a329a10699102802af6d544ab55aa19513f17
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741212"
---
# <a name="self-host-the-api-management-developer-portal"></a>Auto-héberger le portail des développeurs Gestion des API

Ce tutoriel explique comment auto-héberger le [portail des développeurs Gestion des API](api-management-howto-developer-portal.md). L’auto-hébergement vous offre la possibilité d’étendre le portail des développeurs avec une logique personnalisée et des widgets qui personnalisent dynamiquement les pages lors de l’exécution. Vous pouvez auto-héberger plusieurs portails pour votre instance Gestion des API, avec des fonctionnalités différentes. Lorsque vous auto-hébergez un portail, vous êtes responsable de sa maintenance et de ses mises à niveau. 

Les étapes suivantes montrent comment configurer votre environnement de développement local, comment effectuer des modifications dans le portail des développeurs, et comment les publier et les déployer dans un compte de stockage Azure.

Si vous avez déjà chargé ou modifié des fichiers multimédias dans le portail managé, consultez [Passer du portail des développeurs managé au portail auto-hébergé](#move-from-managed-to-self-hosted-developer-portal), plus loin dans cet article.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prérequis

Pour configurer un environnement de développement local, vous devez disposer des éléments suivants :

- Une instance du service Gestion des API. Si vous n’en avez pas, consultez [Démarrage rapide : Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Un compte de stockage Azure où la [fonctionnalité Sites web statiques](../storage/blobs/storage-blob-static-website.md) est activée. Voir [Créer un compte de stockage](../storage/common/storage-account-create.md).
- Git sur votre machine. Installez-le en suivant [ce tutoriel Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Node.js (version LTS `v10.15.0` ou ultérieure) et npm sur votre machine. Consultez [Téléchargement et installation de Node.js et de npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
- Azure CLI. Suivez les [étapes d’installation d’Azure CLI](/cli/azure/install-azure-cli-windows).

## <a name="step-1-set-up-local-environment"></a>Étape 1 : Configurer votre environnement local

Pour configurer votre environnement local, vous devez cloner le référentiel, passer à la dernière version du portail des développeurs et installer les packages npm.

1. Clonez le dépôt [api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) à partir de GitHub :

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Accédez à la copie locale du dépôt :

    ```console
    cd api-management-developer-portal
    ```

1. Découvrez la dernière version du portail.

    Avant d’exécuter le code suivant, vérifiez l’étiquette de la version actuelle dans la [section Releases du dépôt](https://github.com/Azure/api-management-developer-portal/releases) et remplacez la valeur `<current-release-tag>` par l’étiquette de version la plus récente.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Installez tous les packages npm disponibles :

    ```console
    npm install
    ```

> [!TIP]
> Utilisez toujours la [dernière version du portail](https://github.com/Azure/api-management-developer-portal/releases) et maintenez à jour votre portail dupliqué. Les ingénieurs logiciels utilisent la branche `master` de ce dépôt pour leurs tâches de développement quotidiennes. Cette branche contient des versions instables du logiciel.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Étape 2 : Configurer des fichiers JSON, un site web statique et des paramètres CORS

Le portail des développeurs nécessite l’API REST Gestion des API pour gérer le contenu.

### <a name="configdesignjson-file"></a>Fichier config.design.json

Accédez au dossier `src` et ouvrez le fichier `config.design.json`.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Configurez le fichier :

1. Dans la valeur `managementApiUrl`, remplacez `<service-name>` par le nom de votre instance Gestion des API. Si vous avez configuré un [domaine personnalisé](configure-custom-domain.md), utilisez-le à la place (par exemple, `https://management.contoso.com`).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Créez manuellement un jeton SAS](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken) pour activer l’accès direct de l’API REST à votre instance Gestion des API.

1. Copiez le jeton généré et collez-le en tant que valeur `managementApiAccessToken`.

1. Dans la valeur `backendUrl`, remplacez `<service-name>` par le nom de votre instance Gestion des API. Si vous avez configuré un [domaine personnalisé](configure-custom-domain.md), utilisez-le à la place (par exemple, `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Si vous souhaitez activer CAPTCHA dans votre portail des développeurs, consultez [Activer CAPTCHA](#enable-captcha).

### <a name="configpublishjson-file"></a>Fichier config.publish.json

Accédez au dossier `src` et ouvrez le fichier `config.publish.json`.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Configurez le fichier :

1. Copiez-collez les valeurs `managementApiUrl` et `managementApiAccessToken` à partir du fichier de configuration précédent.

1. Si vous souhaitez activer CAPTCHA dans votre portail des développeurs, consultez [Activer CAPTCHA](#enable-captcha).

### <a name="configruntimejson-file"></a>Fichier config.runtime.json

Accédez au dossier `src` et ouvrez le fichier `config.runtime.json`.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Configurez le fichier :

1. Copiez-collez la valeur `managementApiUrl` à partir du fichier de configuration précédent.

1. Dans la valeur `backendUrl`, remplacez `<service-name>` par le nom de votre instance Gestion des API. Si vous avez configuré un [domaine personnalisé](configure-custom-domain.md), utilisez-le à la place (par exemple, `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Configurer le site web statique

Configurez la fonctionnalité **Site web statique** dans votre compte de stockage en fournissant les routes vers les pages d’index et d’erreur :

1. Accédez à votre compte de stockage dans le portail Azure, puis sélectionnez **Site web statique** dans le menu de gauche.

1. Dans la page **Site web statique**, sélectionnez **Activé**.

1. Dans le champ **Nom du document d’index**, entrez *index.html*.

1. Dans le champ **Chemin du document d’erreur**, entrez *404/index.html*.

1. Sélectionnez **Enregistrer**.

### <a name="configure-the-cors-settings"></a>Configurer les paramètres CORS

Configurez les paramètres du partage des ressources cross-origin (CORS) :

1. Accédez à votre compte de stockage dans le portail Azure, puis sélectionnez **CORS** dans le menu de gauche.

1. Sous l’onglet **Service BLOB**, configurez les règles suivantes :

    | Règle | Value |
    | ---- | ----- |
    | Origines autorisées | * |
    | Méthodes autorisées | Sélectionnez tous les verbes HTTP. |
    | En-têtes autorisés | * |
    | En-têtes exposés | * |
    | Âge maximal | 0 |

1. Sélectionnez **Enregistrer**.

## <a name="step-3-run-the-portal"></a>Étape 3 : Exécuter le portail

Vous pouvez maintenant générer et exécuter une instance de portail locale en mode Développement. En mode Développement, toutes les optimisations sont désactivées et les mappages de source sont activés.

Exécutez la commande suivante :

```console
npm start
```

Après un bref laps de temps, le navigateur par défaut s’ouvre automatiquement avec votre instance locale du portail des développeurs. L’adresse par défaut est `http://localhost:8080`, mais le port peut changer si `8080` est déjà occupé. Toute modification apportée à la base de code du projet déclenche une regénération et actualise votre fenêtre de navigateur.

## <a name="step-4-edit-through-the-visual-editor"></a>Étape 4 : Apporter des modifications dans l’éditeur visuel

Utilisez l’éditeur visuel pour effectuer les tâches suivantes :

- Personnaliser votre portail
- Créer du contenu
- Organiser la structure du site web
- Styliser son apparence

Pour plus d’informations, consultez [Tutoriel : Accéder et personnaliser le portail des développeurs](api-management-howto-developer-portal-customize.md). Celui-ci aborde les bases de l’interface utilisateur d’administration, et liste les modifications qu’il est recommandé d’apporter au contenu par défaut. Enregistrez toutes les modifications dans l’environnement local, puis appuyez sur **Ctrl + C** pour le fermer.

## <a name="step-5-publish-locally"></a>Étape 5 : Publier localement

Au départ, les données du portail se présentent sous la forme d’objets fortement typés. La commande suivante les traduit en fichiers statiques et place la sortie dans le répertoire `./dist/website` :

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Étape 6 : Charger des fichiers statiques dans un objet blob

Utilisez Azure CLI pour charger les fichiers statiques générés localement dans un objet blob, et vérifiez que les visiteurs peuvent y accéder :

1. Ouvrez l’invite de commandes Windows, PowerShell ou une autre interface de commande.

1. Exécutez la commande d’interface de ligne de commande Azure suivante.
   
    Remplacez `<account-connection-string>` par la chaîne de connexion de votre compte de stockage. Vous pouvez la récupérer dans la section **Clés d’accès** de votre compte de stockage.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Étape 7 : Accéder à votre site web

Votre site web est désormais en ligne sous le nom d’hôte qui est spécifié dans vos propriétés de stockage Azure (**Point de terminaison principal** dans **Sites web statiques**).

## <a name="step-8-change-api-management-notification-templates"></a>Étape 8 : Modifier les modèles de notifications Gestion des API

Remplacez l’URL du portail des développeurs dans les modèles de notifications Gestion des API pour qu’elle pointe vers votre portail auto-hébergé. Consultez [Configuration des notifications et des modèles de messages électroniques dans Gestion des API Azure](api-management-howto-configure-notifications.md).

Vous devez apporter les modifications suivantes aux modèles par défaut :

> [!NOTE]
> Les valeurs des sections **Contenu mis à jour** suivantes supposent que vous hébergez le portail à l’adresse **https:\//portal.contoso.com/** . 

### <a name="email-change-confirmation"></a>Confirmation de modification de l’adresse e-mail

Mettez à jour l’URL du portail des développeurs dans le modèle de notification **Email change confirmation** (Confirmation de modification de l’adresse e-mail) :

**Contenu d’origine**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Updated**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Confirmation du nouveau compte de développeur

Mettez à jour l’URL du portail des développeurs dans le modèle de notification **New developer account confirmation** (Confirmation du nouveau compte de développeur) :

**Contenu d’origine**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Updated**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Invitation de l'utilisateur

Mettez à jour l’URL du portail des développeurs dans le modèle de notification **Invite user** (Inviter un utilisateur) :

**Contenu d’origine**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Updated**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Nouvel abonnement activé

Mettez à jour l’URL du portail des développeurs dans le modèle de notification **Nouvel abonnement activé** :

**Contenu d’origine**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Updated**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Contenu d’origine**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Updated**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Contenu d’origine**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Updated**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Contenu d’origine**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Updated**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Confirmation du changement de mot de passe

Mettez à jour l’URL du portail des développeurs dans le modèle de notification **Password change confirmation** (Confirmation du changement de mot de passe) :

**Contenu d’origine**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Updated**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Tous les modèles

Mettez à jour l’URL du portail des développeurs dans un des modèles qui comportent un lien dans le pied de page :

**Contenu d’origine**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Updated**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Passer du portail des développeurs managé au portail auto-hébergé

Avec le temps, les besoins de votre entreprise peuvent changer. Vous pouvez vous retrouver dans une situation où la version managée du portail des développeurs Gestion des API ne répond plus à vos besoins. Par exemple, une nouvelle exigence peut vous obliger à créer un widget personnalisé qui s’intègre à un fournisseur de données tiers. Contrairement à la version managée, la version auto-hébergée du portail vous offre une flexibilité et une extensibilité totales.

### <a name="transition-process"></a>Processus de transition

Vous pouvez passer de la version managée à une version auto-hébergée au sein de la même instance du service Gestion des API. Le processus conserve les modifications que vous avez effectuées dans la version managée du portail. Veillez à sauvegarder le contenu du portail au préalable. Le script de sauvegarde se trouve dans le dossier `scripts` du [dépôt GitHub](https://github.com/Azure/api-management-developer-portal) qui se trouve sur le portail des développeurs Gestion des API.

Le processus de conversion est presque identique à la configuration d’un portail auto-hébergé générique, comme le montrent les étapes précédentes de cet article. Il y a une exception, qui se trouve au niveau de l’étape de configuration. Le compte de stockage du fichier `config.design.json`doit être le même que celui de la version managée du portail. Pour obtenir des instructions sur la récupération de l’URL SAS, consultez [Tutoriel : Utiliser une identité de VM Linux affectée par le système pour accéder au stockage Azure via des informations d’identification SAS](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls).

> [!TIP]
> Nous vous recommandons d’utiliser un compte de stockage distinct dans le fichier `config.publish.json`. Cette approche vous offre davantage de contrôle et simplifie la gestion du service d’hébergement de votre portail.

## <a name="enable-captcha"></a>Activer CAPTCHA

Quand vous avez configuré le portail auto-hébergé, vous avez peut-être désactivé CAPTCHA via le paramètre `useHipCaptcha`. La communication avec CAPTCHA s’effectue via un point de terminaison, ce qui permet au partage des ressources Cross-Origin (CORS) d’avoir lieu uniquement pour le nom d’hôte du portail des développeurs managé. Si votre portail des développeurs est auto-hébergé, il utilise un nom d’hôte différent et CAPTCHA n’autorisera pas la communication.

### <a name="update-the-json-config-files"></a>Mettre à jour les fichiers de configuration JSON

Pour activer CAPTCHA dans votre portail auto-hébergé :

1. Affectez un domaine personnalisé (par exemple, `api.contoso.com`) au point de terminaison du **Portail des développeurs** de votre service Gestion des API.

    Ce domaine s’applique à la version managée de votre portail ainsi qu’au point de terminaison CAPTCHA. Pour connaître la procédure à suivre, consultez [Configurer un nom de domaine personnalisé pour votre instance Gestion des API Azure](configure-custom-domain.md).

1. Accédez au dossier `src` dans l’[environnement local](#step-1-set-up-local-environment) de votre portail auto-hébergé.

1. Mettez à jour les fichiers `json` de configuration :

    | Fichier | Nouvelle valeur | Notes |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Remplacez `<custom-domain>` par le domaine personnalisé que vous avez configuré lors de la première étape. |
    |  | `"useHipCaptcha": true` | Remplacez la valeur par `true`. |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Remplacez `<custom-domain>` par le domaine personnalisé que vous avez configuré lors de la première étape. |
    |  | `"useHipCaptcha": true` | Remplacez la valeur par `true`. |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Remplacez `<custom-domain>` par le domaine personnalisé que vous avez configuré lors de la première étape. |

1. [Publiez](#step-5-publish-locally) le portail.

1. [Chargez](#step-6-upload-static-files-to-a-blob) et hébergez le portail récemment publié.

1. Exposez le portail auto-hébergé via un domaine personnalisé.

Les premier et deuxième niveaux du domaine du portail doivent correspondre au domaine configuré à la première étape. Par exemple : `portal.contoso.com`. La procédure exacte dépend de la plateforme d’hébergement que vous avez choisie. Si vous avez utilisé un compte de stockage Azure, vous pouvez consulter [Mapper un domaine personnalisé à un point de terminaison de Stockage Blob Azure](../storage/blobs/storage-custom-domain-name.md) pour obtenir des instructions.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [autres approches d’auto-hébergement](developer-portal-alternative-processes-self-host.md)
