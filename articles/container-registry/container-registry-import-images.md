---
title: Importer les images conteneur
description: Importez des images conteneur dans un registre de conteneurs Azure à l’aide d’API Azure sans avoir à exécuter de commandes Docker.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051918"
---
# <a name="import-container-images-to-a-container-registry"></a>Importer des images conteneur dans un registre de conteneurs

Vous pouvez facilement importer (copier) des images conteneur dans un registre de conteneurs Azure sans recourir à des commandes Docker. Vous pouvez par exemple importer des images d’un registre de développement dans un registre de production ou copier des images de base à partir d’un registre public.

Azure Container Registry gère un certain nombre de scénarios courants de copie d’images à partir d’un registre existant :

* Importer à partir d’un registre public

* Importer à partir d’un autre registre de conteneurs Azure dans un abonnement Azure identique ou différent

* Importer à partir d’un registre de conteneurs privé non-Azure

L’importation d’images dans un registre de conteneurs Azure présente les avantages suivants par rapport à l’utilisation de commandes de l’interface CLI Docker :

* Étant donné que votre environnement client ne nécessite pas d’installation Docker locale, vous pouvez importer n’importe quelle image conteneur, quel que soit le type de système d’exploitation pris en charge.

* Quand vous importez des images multi-architecture (notamment des images Docker officielles), les images pour toutes les architectures et plateformes spécifiées dans la liste de manifeste sont copiées.

Pour importer des images conteneur, cet article nécessite que vous exécutiez l’interface Azure CLI dans Azure Cloud Shell ou localement (version 2.0.55 ou ultérieure recommandée). Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli].

> [!NOTE]
> Si vous avez besoin de distribuer des images conteneur identiques dans plusieurs régions Azure, Azure Container Registry prend également en charge la [géoréplication](container-registry-geo-replication.md). En géoréplicant un registre (niveau de service Premium requis), vous pouvez servir plusieurs régions avec des noms d’image et d’étiquette identiques à partir d’un seul registre.
>

## <a name="prerequisites"></a>Prérequis

Si vous ne disposez pas d’un registre de conteneurs Azure, créez-en un. Pour connaître les étapes à suivre, voir [Démarrage rapide : Créer un registre de conteneurs privé avec Azure CLI](container-registry-get-started-azure-cli.md).

Pour importer une image dans un registre de conteneurs Azure, votre identité doit avoir des autorisations en écriture sur le registre cible (au moins le rôle Contributeur). Consultez [Autorisations et rôles Azure Container Registry](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importer à partir d’un registre public

### <a name="import-from-docker-hub"></a>Importer à partir de Docker Hub

Par exemple, utilisez la commande [az acr import][az-acr-import] pour importer l’image `hello-world:latest` multi-architecture à partir de Docker Hub dans un registre nommé *myregistry*. `hello-world` étant une image officielle de Docker Hub, cette image se trouve dans le dépôt `library` par défaut. Indiquez le nom du dépôt et éventuellement une étiquette dans la valeur du paramètre d’image `--source`. (Vous pouvez également identifier une image par code de hachage de manifeste et non par étiquette, ce qui garantit l’obtention d’une version particulière.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Pour vérifier si plusieurs manifestes sont associés à cette image, exécutez la commande `az acr repository show-manifests` :

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

L’exemple suivant importe une image publique à partir du dépôt `tensorflow` dans Docker Hub :

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importer à partir du registre de conteneurs Microsoft

Par exemple, importez la dernière image Windows Server Core à partir du dépôt `windows` dans le registre de conteneurs Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importer à partir d’un registre de conteneurs Azure

Vous pouvez importer une image à partir d’un autre registre de conteneurs Azure à l’aide des autorisations Azure Active Directory intégrées.

* Votre identité doit disposer d’autorisations Azure Active Directory pour lire à partir du registre source (rôle Lecteur) et pour écrire dans le registre cible (rôle Contributeur).

* Le registre peut se trouver dans un abonnement Azure identique ou différent dans le même locataire Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importer à partir d’un registre dans le même abonnement

Par exemple, importez l’image `aci-helloworld:latest` à partir d’un registre source *mysourceregistry* dans *myregistry* dans le même abonnement Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

L’exemple suivant importe une image par code de hachage de manifeste (hachage SHA-256, représenté sous la forme `sha256:...`), et non par étiquette :

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importer à partir d’un registre dans un abonnement différent

Dans l’exemple suivant, *mysourceregistry* est dans un autre abonnement que *myregistry*, dans le même locataire Active Directory. Indiquez l’ID de ressource du registre source avec le paramètre `--registry`. Notez que le paramètre `--source` spécifie uniquement le dépôt source et l’étiquette, pas le nom du serveur de connexion au registre.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importer à partir d’un registre à l’aide des informations d’identification du principal de service

Pour importer à partir d’un registre auquel vous ne pouvez pas accéder à l’aide des autorisations Active Directory, vous pouvez utiliser les informations d’identification du principal de service (si celles-ci sont disponibles). Indiquez l’appID et le mot de passe d’un [principal de service](container-registry-auth-service-principal.md) Active Directory disposant d’un accès ACRPull au registre source. L’utilisation d’un principal de service est utile pour les systèmes de génération et d’autres systèmes sans assistance qui doivent importer des images dans votre registre.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importer à partir d’un registre de conteneurs privé non-Azure

Importez une image à partir d’un registre privé en spécifiant des informations d’identification qui permettent un accès en tirage (pull) au registre. Par exemple, tirez (pull) une image à partir d’un registre Docker privé : 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à importer des images conteneur dans un registre de conteneurs Azure à partir d’un registre public ou d’un autre registre privé. Pour accéder à des options d’importation d’image supplémentaires, consultez les informations de référence sur la commande [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
