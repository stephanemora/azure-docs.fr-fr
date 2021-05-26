---
title: Alternatives pour le portail des développeurs auto-hébergé
titleSuffix: Azure API Management
description: Découvrez les autres approches que vous pouvez utiliser lorsque vous auto-hébergez un portail des développeurs dans Gestion des API Azure.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 207f5cbfb0e8f6d9d52920f7e4c72d7e663489fc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110459347"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Autres approches du portail des développeurs auto-hébergés

Il existe plusieurs autres approches que vous pouvez explorer lorsque vous [auto-hébergez un portail des développeurs](developer-portal-self-host.md) :

* Utilisez des builds de production du concepteur et de l’éditeur.

* Utilisez une application de fonction Azure pour publier votre portail.

* Préfacez les fichiers de votre portail avec un réseau de distribution de contenu (CDN) pour réduire les temps de chargement des pages.

Cet article fournit des informations sur chacune de ces approches. 

Si vous ne l’avez pas déjà fait, configurez un [environnement local](developer-portal-self-host.md#step-1-set-up-local-environment) pour la dernière version du portail des développeurs.

## <a name="build-for-production"></a>Build pour la production

Si vous souhaitez héberger l’environnement de développement du portail en ligne à des fins de collaboration, utilisez les builds de production du concepteur et de l’éditeur. Les builds de production regroupent les fichiers, excluent les mappages de source, etc.

Créez un bundle dans le répertoire `./dist/designer` en exécutant la commande :

```sh
npm run build-designer
```

Le résultat étant une application à page unique, vous pouvez toujours la déployer sur un hôte web statique, tel que le site web statique du stockage d’objets blob Azure.

De même, placez un serveur de publication compilé et optimisé dans le dossier `./dist/publisher` :

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Utiliser une application de fonction pour publier le portail

Exécutez l’étape de publication dans le cloud plutôt que localement.

Pour implémenter la publication avec une application de fonction Azure, vous avez besoin des composants suivants :

- [Créez une fonction Azure](../azure-functions/functions-get-started.md). La fonction doit être une fonction de langage JavaScript.
- Installez Azure Functions Core Tools :
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Étape 1 : Configurer le stockage de sortie

Chargement du contenu directement sur le site web d’hébergement (conteneur « $web » du stockage de sortie) au lieu d’un dossier local. Configurez cette modification dans le fichier `./src/config.publish.json` :

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Étape 2 : Générer et déployer l’application de fonction

Le dossier `./examples` contient un exemple de fonction de déclencheur HTTP. Pour le générer et le placer dans `./dist/function`, exécutez la commande suivante :

```sh
npm run build-function
```

Ensuite, connectez-vous à Azure CLI et déployez-le :

```azurecli
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Une fois déployé, vous pouvez l’appeler avec un appel HTTP :

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hébergement et CDN

Dans [Auto-héberger un portail des développeurs](developer-portal-self-host.md), nous avons suggéré d’utiliser un compte de stockage Azure pour héberger votre site web. Toutefois, vous pouvez publier les fichiers par le biais de n’importe quelle solution, y compris les services des fournisseurs d’hébergement.

Vous pouvez également initier les fichiers avec un réseau de distribution de contenu (CDN) pour réduire les temps de chargement des pages. Nous vous recommandons d’utiliser [Azure CDN](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le portail des développeurs :

- [Vue d’ensemble du portail des développeurs Gestion des API Azure](api-management-howto-developer-portal.md)
