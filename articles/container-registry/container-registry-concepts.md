---
title: À propos des registres, des référentiels, des images et des artefacts
description: Introduction aux concepts clés des registres de conteneurs Azure, des référentiels et images conteneur et d’autres artefacts.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578126"
---
# <a name="about-registries-repositories-and-artifacts"></a>À propos des registres, des référentiels et des artefacts

Cet article présente les concepts clés des registres de conteneurs, des référentiels, des images conteneur et les artefacts associés. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registre, référentiels et artefacts":::

## <a name="registry"></a>Registre

Un *registre* de conteneurs est un service qui stocke et distribue des images conteneur et des artefacts associés. Docker Hub est un exemple de registre de conteneurs public qui sert de catalogue général d’images conteneur Docker. Azure Container Registry fournit aux utilisateurs un contrôle direct du contenu de leurs conteneurs, avec l’authentification intégrée, la[géoréplication](container-registry-geo-replication.md) prenant en charge la distribution mondiale et la fiabilité pour les déploiements proches du réseau, la [configuration du réseau virtuel avec Private Link](container-registry-private-link.md), le [verrouillage des étiquettes](container-registry-image-lock.md), et de nombreuses autres fonctionnalités avancées. 

En plus des images conteneur compatibles avec Docker, Azure Container Registry prend en charge différents [artefacts de contenu](container-registry-image-formats.md), y compris les charts Helm et le format d’image OCI (Open Container Initiative).

## <a name="repository"></a>Référentiel

Un *référentiel* est une collection d’images conteneur ou d’autres artefacts dans un registre ayant le même nom, mais des étiquettes différentes. Par exemple, les trois images suivantes se trouvent dans le référentiel `acr-helloworld` :

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Les noms des référentiels peuvent également comprendre des [espaces de noms](container-registry-best-practices.md#repository-namespaces). Les espaces de noms vous permettent d’identifier des référentiels associés et la propriété d’artefact dans votre organisation en utilisant des noms de délimités par des barres obliques. Toutefois, le registre gère tous les référentiels indépendamment, et non en tant que hiérarchie. Par exemple :

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Les noms de référentiel ne peuvent inclure que des caractères alphanumériques en minuscules, des points, des tirets, des tirets du bas et des barres obliques. 

Pour connaître les règles complètes de nommage de référentiel, consultez la [spécification de distribution Open Container Initiative](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Artefact

Une image conteneur (ou un autre artefact qui se trouve dans le registre) est associée à une ou plusieurs étiquettes, est constituée d’un ou plusieurs calques, et est identifiable par son manifeste. Comprendre comment ces composants sont liés entre eux peut vous aider à gérer de manière efficace votre registre.

### <a name="tag"></a>Tag

L’*étiquette* pour une image ou autre artefact spécifie sa version. Dans un référentiel, un artefact peut être associé à une ou plusieurs étiquettes, mais il peut aussi n’être associée à aucune étiquette. Autrement dit, vous pouvez supprimer toutes les étiquettes d'une image tout en conservant les données de cette image (ses calques) dans le registre.

Le nom d’une image est défini par le référentiel (ou le référentiel et l’espace de noms) et l’étiquette. Vous pouvez envoyer (push) et tirer (pull) une image en spécifiant son nom dans l’opération push ou pull. L’étiquette `latest` est utilisée par défaut si vous n’en fournissez pas une dans vos commandes Docker.

La façon dont vous étiquettez les images conteneur est guidée par vos scénarios pour les développer ou les déployer. Par exemple, les étiquettes stables sont recommandées pour gérer vos images de base et les étiquettes uniques pour le déploiement d’images. Pour plus d’informations, consultez les [Recommandations pour les images conteneur de balisage et le contrôle de version](container-registry-image-tag-version.md).

Pour connaître les règles de nommage d’étiquette, consultez la [documentation Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Couche

Les images conteneur et les artefacts sont constitués d’une ou plusieurs *couches*. Les différents types d’artefacts définissent les couches différemment. Par exemple, dans une image conteneur Docker, chaque couche correspond à une ligne dans le fichier Dockerfile qui définit l’image :

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Couches d’une image conteneur":::

Les artefacts d’un registre partagent des couches communes, ce qui augmente l’efficacité du stockage. Par exemple, plusieurs images dans des référentiels différents peuvent partager une couche de base ASP.NET Core, mais une seule copie de cette couche est stockée dans le registre. Le partage des couches optimise également la distribution des couches sur les nœuds, avec plusieurs artefacts partageant des couches communes. Si une image déjà présente sur un nœud a comme base une couche ASP.NET Core, le tirage suivant d’une autre image référençant la même couche ne va pas transférer la couche au nœud. Au lieu de cela, il va référencer le calque déjà présent dans le nœud.

Pour fournir une isolation sécurisée et une protection contre la manipulation de calques potentiels, les calques ne sont pas partagés entre les registres.

### <a name="manifest"></a>Manifeste

Chaque image conteneur (ou artefact) envoyée (push) à un registre de conteneurs est associée à un *manifeste*. Le manifeste, qui est généré par le registre quand l’image est tirée, identifie les artefacts de façon univoque et spécifie les couches. Vous pouvez lister les manifestes d’un référentiel avec la commande Azure CLI [az acr repository show-manifests][az-acr-repository-show-manifests]. 

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

Vous pouvez répertorier les manifestes d’un référentiel avec la commande Azure CLI [az acr repository show-manifests][az-acr-repository-show-manifests] :

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Par exemple, voici la liste des manifestes pour le référentiel « acr-helloworld » :

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

### <a name="manifest-digest"></a>Code de hachage de manifeste

Les manifestes sont identifiables par un code de hachage SHA-256 unique, appelé *code de hachage de manifeste*. Chaque image (ou artefact), qu’elle soit ou non associée à une étiquette, est identifiable par son code de hachage. La valeur du code de hachage est unique, même si les données de couche de l’image sont identiques à celles d’un autre artefact. Ce mécanisme permet d’envoyer (push) à plusieurs reprises des images ayant la même étiquette vers le registre. Par exemple, vous pouvez envoyer `myimage:latest` à plusieurs reprises vers votre registre sans vous tromper, car chaque image est identifiable par son propre code de hachage.

Vous pouvez tirer (pull) une image à partir d’un registre en spécifiant son code de hachage dans l’opération de tirage. Certains systèmes peuvent être configurés pour tirer en fonction du code de hachage, car celui-ci garantit la version de l’image tirée, même si une image avec la même étiquette est ensuite poussée (push) au registre.

> [!IMPORTANT]
> Si vous poussez (push) plusieurs fois des artefacts modifiés avec des étiquettes identiques, vous risquez de créer des images « orphelines » (des artefacts sans étiquette), mais qui occupent encore de l’espace dans votre registre. Lorsque vous répertoriez les images par étiquette, les images sans étiquette ne s’affichent pas dans Azure CLI ni dans le portail Azure. Toutefois, leurs calques se trouvent toujours dans le registre et consomment de l’espace de stockage. La suppression d’une image sans balise libère de l’espace dans le Registre lorsque le manifeste est le seul ou le dernier à pointer vers une couche particulière. Pour plus d’informations sur la libération d’espace utilisé par les images non balisées, consultez [Supprimer des images conteneur dans Azure Container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Adressage d’un artefact

Pour adresser un artefact de registre pour les opérations de poussée (push) et de tirage (pull) avec Docker ou d’autres outils clients, combinez le nom de registre complet, le nom du référentiel (y compris le chemin de l’espace de noms, le cas échéant), et une étiquette d’artefact ou un code de hachage de manifeste. Pour plus d’explications sur ces termes, consultez les sections précédentes.

  **Adresse par étiquette** : `[loginServerUrl]/[repository][:tag]`
    
  **Adresse par code de hachage** : `[loginServerUrl]/[repository@sha256][:digest]`  

Quand vous utilisez Docker ou d’autres outils clients pour tirer ou envoyer des artefacts à un registre de conteneurs Azure, utilisez l’URL complète du registre, qui est également appelée « nom du *serveur de connexion* ». Dans le cloud Azure, l’URL complète d’un registre de conteneurs Azure est au format `myregistry.azurecr.io` (tout en minuscules).

> [!NOTE]
> * Vous ne pouvez pas spécifier un numéro de port dans l’URL du serveur de connexion du registre, par exemple `myregistry.azurecr.io:443`. 
> * L’étiquette `latest` est utilisée par défaut si vous n’en fournissez pas une dans votre commande.  

   
### <a name="push-by-tag"></a>Poussée (push) par étiquette

Exemples : 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Tirage (pull) par étiquette

Exemple : 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Tirage (pull) par code de hachage de manifeste


Exemple :

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [stockage de registre](container-registry-storage.md) et les [formats de contenu pris en charge](container-registry-image-formats.md) dans Azure Container Registry.

Découvrez comment [envoyer et tirer](container-registry-get-started-docker-cli.md) des images dans Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


