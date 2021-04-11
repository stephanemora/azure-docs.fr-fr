---
title: Images multi-architecture dans votre registre
description: Utilisez votre registre de conteneurs Azure pour créer, importer, stocker et déployer des images multi-architecture (multi-arch)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802452"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Images multi-architecture dans votre registre de conteneurs Azure

Cet article présente les images *multi-architecture* (*multi-arch*) et la façon dont vous pouvez utiliser les fonctionnalités d’Azure Container Registry pour les créer, les stocker et les utiliser. 

Une image multi-architecture est un type d’image conteneur qui peut combiner des variantes pour des architectures, voire des systèmes d’exploitation différents. Lorsque vous exécutez une image avec la prise en charge multi-architecture, les clients de conteneur sélectionnent automatiquement une variante de l’image qui correspond à votre système d’exploitation et à votre architecture.

## <a name="manifests-and-manifest-lists"></a>Manifestes et listes de manifestes

Les images multi-architecture sont basées sur des manifestes d’images et des listes de manifestes.

### <a name="manifest"></a>Manifeste

Chaque image conteneur est représentée par un [manifeste](container-registry-concepts.md#manifest). Un manifeste est un fichier JSON qui identifie de façon unique l’image, en référençant ses couches et leurs tailles correspondantes. 

Un manifeste de base pour une image `hello-world` Linux se présente comme suit :

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Vous pouvez afficher un manifeste dans Azure Container Registry à l’aide du portail Azure ou d’outils comme la commande [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) dans Azure CLI.

### <a name="manifest-list"></a>Liste de manifestes

Une *liste de manifestes* pour une image multi-architecture (généralement appelée [index d’images](https://github.com/opencontainers/image-spec/blob/master/image-index.md) pour les images OCI) est une collection (index) d’images, et vous en créez une en spécifiant un ou plusieurs noms d’images. Elle comprend des détails sur chacune des images, comme le système d’exploitation et l’architecture pris en charge, la taille et le résumé du manifeste. La liste de manifestes peut être utilisée de la même façon qu’un nom d’image dans les commandes `docker pull` et `docker run`. 

L’interface CLI `docker` gère les manifestes et les listes de manifestes à l’aide de la commande [docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/).

> [!NOTE]
> Actuellement, la commande `docker manifest` et les sous-commandes sont expérimentales. Pour plus d’informations sur l’utilisation des commandes expérimentales, consultez la documentation Docker.

Vous pouvez afficher une liste de manifestes à l’aide de la commande `docker manifest inspect`. Vous trouverez ci-dessous la sortie de l’image multi-architecture `mcr.microsoft.com/mcr/hello-world:latest`, qui comporte trois manifestes : deux pour les architectures de système d’exploitation Linux et une pour une architecture Windows.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Quand une liste de manifestes multi-architecture est stockée dans Azure Container Registry, vous pouvez également afficher la liste de manifestes à l’aide du portail Azure ou d’outils comme la commande [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests).

## <a name="import-a-multi-arch-image"></a>Importer une image multi-architecture 

Une image multi-architecture existante peut être importée dans un registre de conteneurs Azure à l’aide de la commande [az acr import](/cli/azure/acr#az_acr_import). La syntaxe d’importation de l’image est identique à celle d’une image à architecture unique. À l’instar de l’importation d’une image à architecture unique, l’importation d’une image multi-architecture n’utilise pas de commande Docker. 

Pour plus d’informations, consultez [Importation d’images conteneur dans un registre de conteneurs](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Envoyer (push) une image multi-architecture

Quand vous avez généré des flux de travail pour créer des images conteneur pour différentes architectures, procédez comme suit pour envoyer (push) une image multi-architecture à votre registre de conteneurs Azure.

1. Étiquetez et envoyez (push) chaque image spécifique à une architecture à votre registre de conteneurs. L’exemple suivant illustre deux architectures Linux : arm64 et amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Exécutez `docker manifest create` pour créer une liste de manifestes afin de combiner les images précédentes dans une image multi-architecture.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Envoyez (push) le manifeste à votre registre de conteneurs en utilisant `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Utilisez la commande `docker manifest inspect` pour afficher la liste de manifestes. Un exemple de sortie de commande est illustré dans la section précédente.

Une fois que vous avez envoyé (push) le manifeste multi-architecture à votre registre, utilisez l’image multi-architecture de la même façon que vous le feriez avec une image à architecture unique. Par exemple, extrayez (pull) l’image à l’aide de `docker pull`, puis utilisez les commandes [az acr repository](/cli/azure/acr/repository#az_acr_repository) pour afficher les étiquettes, les manifestes et d’autres propriétés de l’image.

## <a name="build-and-push-a-multi-arch-image"></a>Générer et envoyer (push) une image multi-architecture

À l’aide des fonctionnalités [ACR Tasks](container-registry-tasks-overview.md), vous pouvez générer et envoyer (push) une image multi-architecture vers votre registre de conteneurs Azure. Par exemple, définissez une [tâche à plusieurs étapes](container-registry-tasks-multi-step.md) dans un fichier YAML qui crée une image Linux multi-architecture.

L’exemple suivant suppose que vous avez des fichiers Docker distincts pour deux architectures, arm64 et amd64. Il génère et envoie (push) les images spécifiques à une architecture, puis crée et envoie un manifeste multi-architecture contenant l’étiquette `latest` :

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Étapes suivantes

* Utilisez [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) pour générer des images conteneur pour différentes architectures.
* En savoir plus sur la création d’images multi-plateforme à l’aide du plug-in [buildx](https://docs.docker.com/buildx/working-with-buildx/) Docker expérimental.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
