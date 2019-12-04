---
title: Autorisations d’accès aux référentiels
description: Créer un jeton avec des autorisations étendues à des référentiels spécifiques dans un registre pour tirer (pull) ou envoyer (push) des images
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454916"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Autorisations d’étendue de référentiel dans Azure Container Registry 

Azure Container Registry prend en charge plusieurs [options d’authentification](container-registry-authentication.md) à l’aide d’identités qui ont [accès en fonction du rôle](container-registry-roles.md) à un registre entier. Toutefois, pour certains scénarios, vous devrez peut-être fournir un accès uniquement à des *référentiels* spécifiques dans un registre. 

Cet article explique comment créer et utiliser un jeton d’accès qui dispose des autorisations nécessaires pour effectuer des actions uniquement sur des référentiels spécifiques d’un registre. Avec un jeton d’accès, vous pouvez fournir aux utilisateurs ou aux services un accès étendu et limité dans le temps aux référentiels pour tirer (pull) ou envoyer (push) des images ou effectuer d’autres actions. 

Consultez [À propos des autorisations d’étendue de référentiel](#about-repository-scoped-permissions), plus loin dans cet article pour obtenir un arrière-plan sur les concepts et scénarios.

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Cette fonctionnalité est disponible uniquement dans un registre de conteneurs **Premium**. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Références SKU Azure Container Registry](container-registry-skus.md).
* Vous ne pouvez pas actuellement attribuer des autorisations d’étendue de référentiel à un objet Azure Active Directory tel qu’un principal de service ou une identité managée.

## <a name="prerequisites"></a>Prérequis

* **Azure CLI** : cet article requiert une installation locale d’Azure CLI (version 2.0.76 ou ultérieure). Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).
* **Docker** : pour vous authentifier auprès du registre, vous avez également besoin d’une installation du Docker locale. Docker fournit des instructions d’installation pour les systèmes [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registre de conteneurs avec référentiels** : créez un registre de conteneurs dans votre abonnement Azur, si vous n’en avez pas. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md). 

  À des fins de test, [envoyer (push)](container-registry-get-started-docker-cli.md) ou [importer](container-registry-import-images.md) un ou plusieurs exemples d’images dans le registre. Les exemples de cet article font référence aux images suivantes dans deux référentiels : `samples/hello-world:v1` et `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Créer un jeton d’accès

Créez un jeton à l’aide de la commande [az acr token create][az-acr-token-create]. Lorsque vous créez un jeton, spécifiez un ou plusieurs référentiels et actions associées sur chaque référentiel ou spécifiez un mappage d’étendue existant avec ces paramètres.

### <a name="create-access-token-and-specify-repositories"></a>Créer un jeton d’accès et spécifier des référentiels

L’exemple suivant crée un jeton d’accès avec les autorisations nécessaires pour effectuer des actions `content/write` et `content/read` sur le référentiel `samples/hello-world`, ainsi que l’action `content/read` sur le référentiel `samples/nginx`. Par défaut, la commande génère deux mots de passe. 

Cet exemple définit l’état du jeton sur `enabled` (paramètre par défaut), mais vous pouvez mettre à jour le jeton à tout moment et définir l’état sur `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

La sortie affiche des détails sur le jeton, y compris les mots de passe générés et le mappage de l’étendue. Il est recommandé d’enregistrer les mots de passe dans un endroit sûr pour les utiliser ultérieurement avec `docker login`. Les mots de passe ne peuvent pas être récupérés à nouveau, mais d’autres peuvent être générés.

La sortie indique également qu’un mappage d’étendue est créé automatiquement, nommé `MyToken-scope-map`. Vous pouvez utiliser le mappage d’étendue pour appliquer les mêmes actions de référentiel à d’autres jetons. Ou, mettez à jour le mappage d’étendue ultérieurement pour modifier les autorisations de jeton.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>Créer un mappage d’étendue et un jeton associé

Vous pouvez également spécifier un mappage d’étendue avec les référentiels et les actions associées lors de la création d’un jeton. Pour créer un mappage d’étendue, utilisez la commande [az acr scope-map create][az-acr-scope-map-create].

L’exemple de commande suivant crée un mappage d’étendue avec les mêmes autorisations que celles utilisées dans l’exemple précédent. Il permet les actions `content/write` et `content/read` sur le référentiel `samples/hello-world`, ainsi que l’action `content/read` sur le référentiel `samples/nginx` :

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

Le résultat ressemble à ce qui suit :

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Exécutez la commande [az acr token create][az-acr-token-create] pour créer un jeton associé au mappage d’étendue *MyScopeMap*. Par défaut, la commande génère deux mots de passe. Cet exemple définit l’état du jeton sur `enabled` (paramètre par défaut), mais vous pouvez mettre à jour le jeton à tout moment et définir l’état sur `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

La sortie affiche des détails sur le jeton, y compris les mots de passe générés et le mappage d’étendue appliqué. Il est recommandé d’enregistrer les mots de passe dans un endroit sûr pour les utiliser ultérieurement avec `docker login`. Les mots de passe ne peuvent pas être récupérés à nouveau, mais d’autres peuvent être générés.

## <a name="generate-passwords-for-token"></a>Générer des mots de passe pour le jeton

Si des mots de passe ont été créés lors de la création du jeton, passez à [Authentifier avec le registre](#authenticate-using-token).

Si vous n’avez pas de mot de passe de jeton ou si vous souhaitez générer de nouveaux mots de passe, exécutez la commande [az acr token credential generate][az-acr-token-credential-generate].

L’exemple suivant génère un nouveau mot de passe pour le jeton que vous avez créé, avec une période d’expiration de 30 jours. Elle stocke le mot de passe dans la variable d’environnement TOKEN_PWD. Cet exemple est mis en forme pour l’interpréteur de commandes Bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Authentifier à l'aide du jeton

Exécutez `docker login` pour vous authentifier auprès du registre à l’aide des informations d’identification du jeton. Entrez le nom du jeton en tant que nom d’utilisateur et fournissez l’un de ses mots de passe. L’exemple suivant est mis en forme pour l’interpréteur de commandes Bash et fournit les valeurs à l’aide de variables d’environnement.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

La sortie doit afficher l’authentification réussie :

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Vérifier l’accès délimité

Vous pouvez vérifier que le jeton fournit des autorisations délimitées aux référentiels dans le registre. Dans cet exemple, les commandes `docker pull` suivantes s’exécutent correctement pour tirer (pull) les images disponibles dans les référentiels `samples/hello-world` et `samples/nginx` :

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Étant donné que l’exemple de jeton autorise l’action `content/write` uniquement sur le référentiel `samples/hello-world`, `docker push` réussit pour ce référentiel mais échoue pour `samples/nginx` :

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Mettre à jour le mappage d’étendue et le jeton

Pour mettre à jour les autorisations de jeton, mettez à jour les autorisations dans le mappage d’étendue associé, à l’aide de la commande [az acr scope-map update][az-acr-scope-map-update]. Par exemple, pour mettre à jour *MyScopeMap* pour supprimer l’action `content/write` sur le référentiel `samples/hello-world` :

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Si le mappage d’étendue est associé à plusieurs jetons, la commande met à jour l’autorisation de tous les jetons associés.

Si vous souhaitez mettre à jour un jeton avec un mappage d’étendue différent, exécutez la commande [az acr token update][az-acr-token-update]. Par exemple :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Après la mise à jour d’un jeton ou d’un mappage d’étendue associé à un jeton, les modifications apportées aux autorisations prennent effet lors de la `docker login` suivante ou d’une autre authentification à l’aide du jeton.

Après la mise à jour d’un jeton, vous souhaiterez peut-être générer de nouveaux mots de passe pour accéder au registre. Exécutez [az acr token credential generate][az-acr-token-credential-generate]. Par exemple :

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>À propos des autorisations délimitées par le référentiel

### <a name="concepts"></a>Concepts

Pour configurer des autorisations délimitées par le référentiel, créez un *jeton d’accès* et un *mappage d’étendue* associé à l’aide des commandes de l’interface de ligne de commande Azure.

* Un **jeton d’accès** est une information d’identification utilisée avec un mot de passe pour s’authentifier auprès du registre. Les actions associées à chaque jeton sont des *actions* autorisées délimitées à un ou plusieurs référentiels. Vous pouvez définir un délai d’expiration pour chaque jeton. 

* Les **actions** sur chaque référentiel spécifié incluent un ou plusieurs des éléments suivants.

  |Action  |Description  |
  |---------|---------|
  |`content/read`     |  Lisez les données du référentiel. Par exemple, tirer (pull) un artefact.  |
  |`metadata/read`    | Lisez les métadonnées du référentiel. Par exemple, répertorier les balises ou afficher les métadonnées du manifeste.   |
  |`content/write`     |  Écrivez des données dans le référentiel. Utilisez avec `content/read` pour envoyer (push) un artefact.    |
  |`metadata/write`     |  Écrivez des métadonnées dans le référentiel. Par exemple, mettre à jour les attributs de manifeste.  |
  |`content/delete`    | Supprimez les données du référentiel. Par exemple, supprimer un référentiel ou un manifeste. |

* Un **mappage d’étendue** est un objet de registre qui regroupe des autorisations de référentiel que vous appliquez à un jeton ou qui peut s’appliquer à nouveau à d’autres jetons. Si vous n’appliquez pas de mappage d’étendue lors de la création d’un jeton, un mappage d’étendue est automatiquement créé pour vous, afin d’enregistrer les paramètres d’autorisation. 

  Un mappage d’étendue vous aide à configurer plusieurs utilisateurs avec un accès identique à un ensemble de référentiels. Azure Container Registry fournit également des mappages d’étendue définis par le système que vous pouvez appliquer lors de la création de jetons d’accès.

L’image suivante résume la relation entre les jetons et les mappages d’étendue. 

![Mappages d’étendues et jetons de registre](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Scénarios

Les scénarios d’utilisation d’un jeton d’accès sont les suivants :

* Fournir aux appareils IoT des jetons individuels pour tirer (pull) une image d’un référentiel
* Fournir à une organisation externe des autorisations sur un référentiel spécifique 
* Limiter l’accès au référentiel à des groupes d’utilisateurs spécifiques dans votre organisation. Par exemple, fournissez un accès en écriture et en lecture aux développeurs qui génèrent des images ciblant des référentiels spécifiques, ainsi qu’un accès en lecture aux équipes qui déploient à partir de ces référentiels.

### <a name="authentication-using-token"></a>Authentification à l’aide d’un jeton

Utilisez un nom de jeton en tant que nom d’utilisateur et l’un de ses mots de passe associés pour l’authentification auprès du registre cible. La méthode d’authentification utilisée dépend des actions configurées.

### <a name="contentread-or-contentwrite"></a>contenu/lecture ou contenu/écriture

Si le jeton autorise uniquement des actions `content/read` ou `content/write`, fournissez les informations d’identification du jeton dans l’un des flux d’authentification suivants :

* S’authentifier avec Docker à l’aide de `docker login`
* S’authentifier avec le registre à l’aide de la commande [az acr login][az-acr-login] dans l’interface de ligne de commande Azure

Après l’authentification, le jeton autorise les actions configurées sur le ou les référentiels délimités. Par exemple, si le jeton autorise l’action `content/read` sur un référentiel, les opérations `docker pull` sont autorisées sur les images dans ce référentiel.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>métadonnées/lecture, métadonnées/écriture ou contenu/supprimer

Si le jeton permet les actions `metadata/read`, `metadata/write` ou `content/delete` sur un référentiel, les informations d’identification du jeton doivent être fournies en tant que paramètres avec les commandes [az acr repository][az-acr-repository] dans l’interface de ligne de commande Azure.

Par exemple, si les actions `metadata/read` sont autorisées sur un référentiel, transmettez les informations d’identification du jeton lors de l’exécution de la commande [az acr repository show-tags][az-acr-repository-show-tags] pour répertorier les balises.

## <a name="next-steps"></a>Étapes suivantes

* Pour gérer les mappages d’étendue et les jetons d’accès, utilisez des commandes supplémentaires dans le groupe de commandes [az acr scope-map][az-acr-scope-map] et [az acr token][az-acr-token].
* Consultez l’[aperçu de l’authentification](container-registry-authentication.md) pour les scénarios permettant de s’authentifier avec un registre de conteneurs Azure à l’aide d’un compte d’administrateur ou d’une identité Azure Active Directory.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
