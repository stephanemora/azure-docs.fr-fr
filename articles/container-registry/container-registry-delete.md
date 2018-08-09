---
title: Supprimer des ressources d’images dans Azure Container Registry
description: Explique en détail comment gérer efficacement la taille du registre en supprimant les données d’image conteneur.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 07/27/2018
ms.author: marsma
ms.openlocfilehash: 6ab667a01eddd84d1145868a3ae499e7497035c9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266832"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Supprimer des images conteneur dans Azure Container Registry

Pour limiter la taille de votre registre de conteneurs Azure, vous devez supprimer régulièrement les données d’image obsolètes. Certaines images conteneur déployées dans l’environnement de production peuvent nécessiter un stockage à long terme, mais d’autres peuvent être supprimées plus rapidement. Par exemple, dans un scénario de génération et de tests automatisés, votre registre peut se remplir rapidement d’images qui peuvent ne jamais être déployées, et peuvent donc être vidées peu après la génération et la réussite des tests.

Il existe plusieurs façons de supprimer les données d’image, et il est important de comprendre comment chaque opération de suppression affecte l’utilisation du stockage. Cet article présente d’abord les composants d’un registre Docker et des images conteneur, puis il présente plusieurs méthodes de suppression pour les données d’image.

## <a name="registry"></a>Registre

Un *registre* de conteneurs est un service qui stocke et distribue des images conteneur. Docker Hub est un registre de conteneurs Docker public, alors qu’Azure Container Registry fournit des registres de conteneurs Docker privés dans Azure.

## <a name="repository"></a>Référentiel

Les registres de conteneurs gèrent des *référentiels*, qui sont des collections d’images conteneur ayant le même nom, mais des étiquettes différentes. Par exemple, les trois images suivantes se trouvent dans le référentiel « acr-helloworld » :

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Les noms des référentiels peuvent également comprendre des [espaces de noms](container-registry-best-practices.md#repository-namespaces). Les espaces de noms permettent de grouper des images à l’aide de noms de référentiels délimités par des barres obliques, par exemple :

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>Composants d’une image

Une image conteneur qui se trouve dans le registre est associée à une ou plusieurs étiquettes, est constituée d’un ou plusieurs calques, et est identifiable par son manifeste. Le fait de comprendre la relation qui existe entre ces composants peut vous aider à déterminer la meilleure façon de libérer de l’espace dans votre registre.

### <a name="tag"></a>Tag

*L’étiquette* d’une image spécifie sa version. Dans un référentiel, une image peut être associée à une ou plusieurs étiquettes, mais elle peut aussi n’être associée à aucune étiquette. Autrement dit, vous pouvez supprimer toutes les étiquettes d’une image, tout en conservant les données de l’image (ses calques) dans le registre.

Le nom d’une image est défini par le référentiel (ou le référentiel et l’espace de noms) et l’étiquette. Vous pouvez envoyer (push) et tirer (pull) une image en spécifiant son nom dans l’opération push ou pull.

Dans un registre privé comme Azure Container Registry, le nom de l’image comprend également le nom complet de l’hôte du registre. Le nom de l’hôte du registre pour les images ACR est au format *acrname.azurecr.io*. Par exemple, le nom complet de la première image dans l’espace de noms « marketing » de la section précédente serait :

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

Pour connaître les bonnes pratiques relatives au choix des étiquettes pour les images, consultez le billet de blog [Docker Tagging: Best practices for tagging and versioning docker images][tagging-best-practices] sur MSDN.

### <a name="layer"></a>Couche

Les images sont constituées d’un ou plusieurs *calques*, correspondant chacun à une ligne dans le fichier Dockerfile qui définit l’image. Les images d’un registre ont des calques en commun, dans le but d’accroître l’efficacité du stockage. Par exemple, des images situées dans des référentiels différents peuvent partager le même calque de base Alpine Linux, qui n’est stocké qu’une seule fois dans le registre.

Le partage des calques optimise également la distribution des calques vers les nœuds qui contiennent plusieurs images ayant des calques en commun. Par exemple, si une image déjà présente dans un nœud a comme base un calque Alpine Linux, le prochain tirage (pull) d’une autre image référençant le même calque ne va pas transférer le calque vers le nœud. Au lieu de cela, il va référencer le calque déjà présent dans le nœud.

### <a name="manifest"></a>Manifeste

Chaque image conteneur envoyée (push) à un registre de conteneurs est associée à un *manifeste*. Le manifeste, qui est généré par le registre lorsque l’image est envoyée, identifie l’image de façon unique et spécifie ses calques. Vous pouvez répertorier les manifestes d’un référentiel avec la commande Azure CLI [az acr repository show-manifests][az-acr-repository-show-manifests] :

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Par exemple, voici la liste des codes de hachage de manifeste pour le référentiel « acr-helloworld » :

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

Le manifeste abordé ici est différent du manifeste d’image que vous voyez dans le portail Azure ou que vous obtenez avec la commande [docker manifest inspect][docker-manifest-inspect]. Dans la section suivante, « code de hachage du manifeste » fait référence au code de hachage généré par l’opération d’envoi (push), et non au *config.digest* du manifeste de l’image. Vous pouvez tirer (pull) et supprimer des images avec du **code de hachage de manifeste**, mais pas avec config.digest. L’image suivante montre deux types de code de hachage.

![Code de hachage de manifeste et config.digest dans le portail Azure][manifest-digest]

### <a name="manifest-digest"></a>Code de hachage de manifeste

Les manifestes sont identifiables par un code de hachage SHA-256 unique, appelé *code de hachage de manifeste*. Chaque image, qu’elle soit ou non associée à une étiquette, est identifiable par son code de hachage. La valeur du code de hachage est unique, même si les données de calque de l’image sont identiques à celles d’une autre image. Ce mécanisme permet d’envoyer (push) à plusieurs reprises des images ayant la même étiquette vers le registre. Par exemple, vous pouvez envoyer `myimage:latest` à plusieurs reprises vers votre registre sans vous tromper, car chaque image est identifiable par son propre code de hachage.

Vous pouvez tirer (pull) une image à partir du registre en spécifiant son code de hachage dans l’opération de tirage. Certains systèmes peuvent être configurés pour tirer en fonction du code de hachage, car celui-ci garantit la version de l’image tirée, même si une image avec la même étiquette est ensuite envoyée (push) vers le registre.

Voici un exemple de tirage d’une image à partir du référentiel « acr-helloworld » en fonction du code de hachage du manifeste :

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Si vous envoyez plusieurs fois des images modifiées ayant une même étiquette, vous risquez de créer des images orphelines (c’est-à-dire sans étiquette), mais qui occupent toujours de l’espace dans votre registre. Lorsque vous répertoriez les images par étiquette, les images sans étiquette ne s’affichent pas dans Azure CLI ni dans le portail Azure. Toutefois, leurs calques se trouvent toujours dans le registre et consomment de l’espace de stockage. La section [Supprimer les images sans étiquette](#delete-untagged-images) de cet article explique comment libérer l’espace utilisé par les images sans étiquette.

## <a name="delete-image-data"></a>Supprimer les données d’image

Vous pouvez supprimer les données d’image du registre de conteneurs de plusieurs façons :

* En supprimant un [référentiel](#delete-repository) : de cette façon, vous supprimez toutes les images et tous les calques contenus dans le référentiel.
* En supprimant en fonction de [l’étiquette](#delete-by-tag) : de cette façon, vous supprimez une image, son étiquette, tous les calques référencés par l’image et toutes les autres étiquettes associées à l’image.
* En supprimant en fonction du [code de hachage du manifeste](#delete-by-manifest-digest) : de cette façon, vous supprimez une image, tous les calques référencés par l’image et toutes les autres étiquettes associées à l’image.

## <a name="delete-repository"></a>Supprimer le référentiel

La suppression d’un référentiel supprime toutes les images qu’il contient, ainsi que leurs balises, calques propres et manifestes. Lorsque vous supprimez un référentiel, vous récupérez l’espace de stockage utilisé par les images qui s’y trouvaient.

La commande suivante d’Azure CLI supprime le référentiel « acr-helloworld », ainsi que toutes les étiquettes et tous les manifestes. Si les calques référencés par les manifestes supprimés ne sont pas référencés par d’autres images du registre, les données de calques sont elles aussi supprimées.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Supprimer en fonction de l’étiquette

Vous pouvez supprimer des images d’un référentiel en spécifiant le nom du référentiel et l’étiquette dans l’opération de suppression. Lorsque vous supprimez une étiquette, vous récupérez l’espace de stockage utilisé par les calques propres à l’image (c’est-à-dire les calques qui ne sont partagés par aucune autre image du registre).

Pour supprimer l’étiquette, utilisez [az acr repository delete][az-acr-repository-delete] et spécifiez le nom de l’image dans le paramètre `--image`. Tous les calques propres à l’image, et toutes les autres étiquettes associées à l’image, sont supprimés.

Voici un exemple de suppression de l’image « acr-helloworld:latest » dans le registre « myregistry » :

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> La suppression *en fonction de l’étiquette* ne doit pas être confondue avec la suppression d’une étiquette. Vous pouvez supprimer une étiquette avec la commande [az acr repository untag][az-acr-repository-untag] d’Azure CLI. Aucun espace n’est libéré lorsque vous supprimez l’étiquette d’une image, car son [manifeste](#manifest) et ses données de calque sont conservés dans le registre. Seule la référence de l’étiquette est supprimée.

## <a name="delete-by-manifest-digest"></a>Supprimer en fonction du code de hachage du manifeste

Un [code de hachage de manifeste](#manifest-digest) peut être associé à une ou plusieurs étiquettes, ou à aucune. Lorsque vous supprimez en fonction du code de hachage, toutes les étiquettes référencées par le manifeste sont supprimées, comme le sont les données des calques propres à l’image. Les données de calques partagées ne sont pas supprimées.

Pour supprimer en fonction du code de hachage, commencez par répertorier les codes de hachage de manifeste du référentiel qui contient les images que vous souhaitez supprimer. Par exemple : 

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Ensuite, spécifiez le code de hachage que vous souhaitez supprimer dans la commande [az acr repository delete][az-acr-repository-delete]. Le format de la commande est le suivant :

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Par exemple, pour supprimer le dernier manifeste répertorié dans la sortie précédente (avec l’étiquette « v2 ») :

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

L’image « acr-helloworld:v2 » est supprimée du registre, comme le sont les données de calques propres à cette image. Si un manifeste est associé à plusieurs étiquettes, toutes les étiquettes associées sont également supprimées.

## <a name="delete-untagged-images"></a>Supprimer les images sans étiquette

Comme mentionné dans la section [Code de hachage de manifeste](#manifest-digest), l’envoi (push) d’une image modifiée à l’aide d’une étiquette existante entraîne la **suppression de l’étiquette** de l’image précédemment envoyée, ce qui a pour résultat une image orpheline (ou « non résolue »). L’image du manifeste précédemment envoyée (et ses données de calque) est conservée dans le registre. Regardez la séquence d’événements suivante :

1. Envoyer (push) l’image *acr-helloworld* avec l’étiquette **latest** : `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Vérifier les manifestes du référentiel *acr-helloworld* :

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modifier le Dockerfile de *acr-helloworld*
1. Envoyer (push) l’image *acr-helloworld* avec l’étiquette **latest** : `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Vérifier les manifestes du référentiel *acr-helloworld* :

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": null,
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Comme vous pouvez le voir dans la sortie de la dernière étape de la séquence, il existe maintenant un manifeste orphelin dont la propriété `"tags"` est `null`. Ce manifeste se trouve toujours dans le registre, ainsi que toutes les données des calques propres qu’il référence. **Pour supprimer ces images orphelines et leurs données de calque, vous devez supprimer en fonction du code de hachage du manifeste**.

### <a name="list-untagged-images"></a>Répertorier les images sans étiquette

Vous pouvez répertorier toutes les images sans étiquette de votre référentiel à l’aide de la commande Azure CLI suivante. Remplacez `<acrName>` et `<repositoryName>` par les valeurs adaptées à votre environnement.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>  --query "[?tags==null].digest"
```

### <a name="delete-all-untagged-images"></a>Supprimer toutes les images sans étiquette

Utilisez les exemples de scripts suivants avec prudence : la suppression des données d’image est irrécupérable.

**Azure CLI dans Bash**

Le script Bash suivant supprime toutes les images sans étiquette d’un référentiel. Ce script nécessite Azure CLI et **xargs**. Par défaut, le script n’effectue aucune suppression. Remplacez la valeur `ENABLE_DELETE` par `true` pour activer la suppression des images.

> [!WARNING]
> Si vous disposez de systèmes qui tirent (pull) les images en fonction du code de hachage du manifeste (et non en fonction du nom de l’image), vous ne devez pas exécuter ce script. La suppression des images sans étiquette va empêcher ces systèmes de tirer les images à partir du registre. Au lieu de tirer en fonction du manifeste, envisagez d’adopter un schéma *d’étiquetage unique* (il s’agit là d’une [bonne pratique)][tagging-best-practices].

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**Azure CLI dans PowerShell**

Le script PowerShell suivant supprime toutes les images sans étiquette d’un référentiel. Il nécessite PowerShell et Azure CLI. Par défaut, le script n’effectue aucune suppression. Remplacez la valeur `$enableDelete` par `$TRUE` pour activer la suppression des images.

> [!WARNING]
> Si vous disposez de systèmes qui tirent (pull) les images en fonction du code de hachage du manifeste (et non en fonction du nom de l’image), vous ne devez pas exécuter ce script. La suppression des images sans étiquette va empêcher ces systèmes de tirer les images à partir du registre. Au lieu de tirer en fonction du manifeste, envisagez d’adopter un schéma *d’étiquetage unique* (il s’agit là d’une [bonne pratique)][tagging-best-practices].

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage des images dans Azure Container Registry, consultez [Stockage des images conteneur dans Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
