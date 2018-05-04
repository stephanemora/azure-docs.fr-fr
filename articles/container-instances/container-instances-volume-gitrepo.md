---
title: Monter un volume GitRepo dans Azure Container Instances
description: Découvrez comment monter un volume GitRepo pour cloner un référentiel Git dans vos instances de conteneurs
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Monter un volume GitRepo dans Azure Container Instances

Découvrez comment monter un volume *GitRepo* pour cloner un référentiel Git dans vos instances de conteneurs.

> [!NOTE]
> Le montage d’un volume *GitRepo* est actuellement limité aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>Volumes GitRepo

Le volume *GitRepo* monte un répertoire et clone le référentiel Git spécifié dans celui-ci au démarrage du conteneur. L’utilisation d’un volume *GitRepo* dans vos instances de conteneur permet d’éviter d’ajouter du code à cette fin dans vos applications.

Lorsque vous montez un volume *GitRepo*, vous pouvez définir trois propriétés pour le configurer :

| Propriété | Obligatoire | Description |
| -------- | -------- | ----------- |
| `repository` | OUI | URL complète incluant `http://` ou `https://` du référentiel Git à cloner.|
| `directory` | Non  | Répertoire dans lequel le référentiel doit être cloné. Le chemin d’accès ne peut pas contenir ou commencer par « `..` ».  Si vous spécifiez « `.` », le référentiel est cloné dans le répertoire du volume. Autrement, le référentiel Git est cloné dans un sous-répertoire du nom spécifié à l’intérieur du répertoire de volume. |
| `revision` | Non  | Hachage de validation de la révision à cloner. À défaut de spécification, la révision `HEAD` est clonée. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Monter un volume gitRepo : Azure CLI

Pour monter un volume gitRepo lorsque vous déployez des instances de conteneurs avec [Azure CLI](/cli/azure), fournissez les paramètres `--gitrepo-url` et `--gitrepo-mount-path` à la commande [az container create][az-container-create]. Vous pouvez éventuellement spécifier le répertoire dans le volume où effectuer le clonage (`--gitrepo-dir`) et le hachage de validation de la révision à cloner (`--gitrepo-revision`).

Cet exemple de commande clone l’exemple d’application [aci-helloworld][aci-helloworld] dans `/mnt/aci-helloworld` au sein de l’instance de conteneur :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Pour vérifier que le volume gitRepo a été monté, lancez un interpréteur de commandes dans le conteneur avec [az container exec][az-container-exec] et dressez la liste du répertoire :

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Monter un volume gitRepo : Gestionnaire des ressources

Pour monter un volume gitRepo lorsque vous déployez des instances de conteneurs avec un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), commencez par renseigner le tableau `volumes` dans la section `properties` du groupe de conteneurs du modèle. Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter le volume *GitRepo*, renseignez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur.

Par exemple, le modèle Resource Manager suivant crée un groupe de conteneurs consistant en un seul conteneur. Le conteneur clone deux référentiels GitHub spécifiés par les blocs de volume *GitRepo*. Le deuxième volume inclut des propriétés supplémentaires spécifiant un répertoire vers lequel cloner, ainsi que le hachage de validation d’une révision spécifique à cloner.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

La structure ainsi obtenue de répertoire des deux référentiels clonés définis dans le modèle précédent est la suivante :

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Pour voir un exemple de déploiement d’instance de conteneur avec un modèle Azure Resource Manager, consultez [Déployer des groupes de plusieurs conteneurs dans Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un partage de fichiers Azure dans Azure Container Instances](container-instances-volume-azure-files.md)
* [Monter un volume emptyDir dans Azure Container Instances](container-instances-volume-emptydir.md)
* [Monter un volume secret dans Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec