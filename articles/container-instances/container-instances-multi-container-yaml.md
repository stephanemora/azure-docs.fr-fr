---
title: Déployer des groupes multiconteneurs dans Azure Container Instances avec Azure CLI et YAML
description: Découvrez comment déployer un groupe comportant plusieurs conteneurs dans Azure Container Instances avec Azure CLI et un fichier YAML.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.openlocfilehash: 5dfee15e978d2dba0f50d1dc4b78953698389950
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851131"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Déployer un groupe multiconteneur avec YAML

Azure Container Instances prend en charge le déploiement de plusieurs conteneurs sur un seul hôte dans un [groupe de conteneurs](container-instances-container-groups.md). Les groupes multiconteneurs sont utiles pour créer un side-car d’application pour la journalisation, le monitoring ou toute autre configuration dans laquelle un service a besoin d’un deuxième processus associé.

Il existe deux moyens de déployer des groupes à conteneurs multiples avec Azure CLI :

* Déploiement de fichier YAML (cet article)
* [Déploiement de modèle Resource Manager](container-instances-multi-container-group.md)

En raison de la nature plus concise du format YAML, le déploiement avec un fichier YAML est recommandé lorsque le déploiement comprend *uniquement* les instances de conteneur. Si vous avez besoin de déployer des ressources de service Azure supplémentaires (par exemple, un partage Azure Files) au moment du déploiement des instances de conteneur, le déploiement de modèle Resource Manager est recommandé.

> [!NOTE]
> Les groupes à plusieurs conteneurs sont actuellement restreints aux conteneurs Linux. Nous travaillons actuellement à proposer toutes les fonctionnalités dans les conteneurs Windows. En attendant, vous retrouverez les différences actuelles entre les plateformes dans [Disponibilité des régions et quotas d’Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Configurer le fichier YAML

Pour déployer un groupe multiconteneur avec la commande [az container create][az-container-create] dans Azure CLI, vous devez spécifier la configuration du groupe de conteneurs dans un fichier YAML, puis passer le fichier YAML en paramètre de la commande.

Commencez par copier le YAML suivant dans un nouveau fichier nommé **deploy-aci.yaml**.

Ce fichier YAML définit un groupe de conteneurs qui comprend deux conteneurs, une adresse IP publique et deux ports exposés. Le premier conteneur du groupe exécute une application web accessible sur Internet. Le second conteneur, le side-car, envoie régulièrement des requêtes HTTP à l’application web en cours d’exécution dans le premier conteneur par le biais du réseau local du groupe de conteneurs.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>Déployer le groupe de conteneurs

Créez un groupe de ressources avec la commande [az group create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Déployez le groupe de conteneurs avec la commande [az container create][az-container-create], en transmettant le fichier YAML en argument :

```azurecli-interactive
az container create --resource-group myResourceGroup --name myContainerGroup -f deploy-aci.yaml
```

Après quelques secondes, vous devriez recevoir une réponse initiale d’Azure.

## <a name="view-deployment-state"></a>Afficher l’état du déploiement

Pour afficher l’état du déploiement, utilisez la commande [az container show][az-container-show] suivante :

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Pour voir l’application en cours d’exécution, accédez à son adresse IP dans votre navigateur. Par exemple, l’adresse IP est `52.168.26.124` dans cet exemple de sortie :

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Consulter les journaux

Consultez la sortie du journal d’un conteneur à l’aide de la commande [az container logs][az-container-logs]. L’argument `--container-name` spécifie le conteneur à partir duquel extraire les journaux. Dans cet exemple, le premier conteneur est spécifié.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Output:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Pour afficher les journaux du conteneur annexe, exécutez la même commande, en spécifiant le nom du deuxième conteneur.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Output:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Comme vous pouvez le voir, l’annexe envoie régulièrement une requête HTTP à l’application web principale via le réseau local du groupe pour s’assurer qu’il fonctionne. Cet exemple d’annexe peut être étendu pour déclencher une alerte suite à la réception d’un code de réponse HTTP autre que 200 OK.

## <a name="deploy-from-private-registry"></a>Déployer à partir d’un registre privé

Pour utiliser un registre d’images conteneur privé, intégrez le YAML suivant en modifiant les valeurs en fonction de votre environnement :

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Par exemple, le YAML suivant déploie un groupe comportant un seul conteneur dont l’image est extraite d’un registre Azure Container Registry privé nommé « myregistry » :

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Exporter le groupe de conteneurs vers YAML

Vous pouvez exporter la configuration d’un groupe de conteneurs existant dans un fichier YAML avec la commande Azure CLI [az container export][az-container-export].

Utile pour conserver la configuration d’un groupe de conteneurs, l’exportation permet de stocker cette configuration dans la gestion de version pour la « configuration en tant que code ». Vous pouvez également utiliser le fichier exporté comme point de départ pour développer une nouvelle configuration dans YAML.

Exportez la configuration du groupe de conteneurs que vous avez créé précédemment en émettant la commande [az container export][az-container-export] suivante :

```azurecli-interactive
az container export --resource-group rg604 --name myContainerGroup --file deployed-aci.yaml
```

Aucune sortie ne s’affiche si la commande a réussi, mais vous pouvez afficher le contenu du fichier pour voir le résultat. Voici par exemple les premières lignes avec `head` :

```console
$ head deployed-aci.yaml
apiVersion: 2018-02-01-preview
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports:
```

> [!NOTE]
> À compter de la version 2.0.34 d’Azure CLI, il existe un [problème connu][cli-issue-6525] : les groupes de conteneurs exportés spécifient une ancienne version de l’API, **2018-02-01-preview** (vu dans le précédent exemple de sortie JSON). Si vous souhaitez relancer le déploiement avec le fichier YAML exporté, vous pouvez sans risque remplacer la valeur `apiVersion` par **2018-06-01** dans le fichier YAML exporté.

## <a name="next-steps"></a>Étapes suivantes

Ce document a couvert les étapes nécessaires pour le déploiement d’une instance Azure Container à plusieurs conteneurs. Pour une expérience Azure Container Instances de bout en bout, utilisation d’un registre Azure Container Registry privé comprise, consultez le didacticiel d’Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
