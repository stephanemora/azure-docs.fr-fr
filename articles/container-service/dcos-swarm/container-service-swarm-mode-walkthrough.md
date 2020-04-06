---
title: (DÉCONSEILLÉ) Guide de démarrage rapide - Cluster Azure Docker CE pour Linux
description: Découvrez rapidement comment créer un cluster Docker CE pour des conteneurs Linux dans Azure Container Service, avec Azure CLI.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: d4bbd5560681aa73709019e87c6c22470a64ad78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481736"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>(DÉCONSEILLÉ) Déployer le cluster Docker CE

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Dans ce guide de démarrage rapide, un cluster Docker CE est déployé à l’aide d’Azure CLI. Une application de plusieurs conteneurs composée d’un serveur web frontal et d’une instance Redis est déployée, puis exécutée sur le cluster. Ceci fait, l’application est accessible via internet.

Docker CE est en version préliminaire sur Azure Container Service et **ne doit pas être utilisé pour des charges de travail de production**.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westus2*.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Sortie :

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Créer le cluster Docker Swarm

Pour créer un cluster Docker CE dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#az-acs-create). Pour plus d’informations sur la disponibilité de Docker CE selon les régions, consultez [Régions ACS pour Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)

L’exemple ci-après permet de créer un cluster nommé *mySwarmCluster*, qui inclut un nœud maître Linux et trois nœuds agents Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

Dans certains cas, par exemple avec une version d’évaluation limitée, un abonnement Azure dispose d’un accès limité aux ressources Azure. Si le déploiement échoue à cause d’une limitation du nombre de cœurs disponibles, réduisez le nombre d’agents par défaut en ajoutant `--agent-count 1` à la commande [az acs create](/cli/azure/acs#az-acs-create). 

Au bout de quelques minutes, la commande se termine et retourne des informations au format JSON sur le cluster.

## <a name="connect-to-the-cluster"></a>Se connecter au cluster

Dans ce démarrage rapide, vous avez besoin du FQDN du nœud maître Docker Swarm et du pool d’agents Docker. Exécutez la commande suivante pour retourner les noms de domaine complets du nœud maître et de l’agent.

```azurecli
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Sortie :

```output
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Créez un tunnel SSH vers le nœud maître Docker Swarm. Remplacez `MasterFQDN` par l’adresse du nom de domaine complet du nœud maître Docker Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Définissez la variable d’environnement `DOCKER_HOST`. Ceci vous permet d’exécuter des commandes docker pour le Docker Swarm sans avoir à spécifier le nom de l’hôte.

```bash
export DOCKER_HOST=localhost:2374
```

Vous êtes maintenant prêt à exécuter les services Docker sur le Docker Swarm.


## <a name="run-the-application"></a>Exécution de l'application

Créez un fichier nommé `azure-vote.yaml`, puis copiez-y le contenu suivant.


```yaml
version: '3'
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Exécutez la commande [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) pour créer le service Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Sortie :

```output
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Utilisez la commande [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) pour retourner l’état du déploiement de l’application.

```bash
docker stack ps azure-vote
```

L’application est prête lorsque la valeur `CURRENT STATE` de chaque service est `Running`.

```output
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Test de l’application

Naviguez dans le nom de domaine complet du pool d’agents Swarm pour tester l’application Azure Vote.

![Image de la navigation vers Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Supprimer un cluster
Lorsque vous n’avez plus besoin du cluster, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, le service de conteneur et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obtenir le code

Dans ce démarrage rapide, des images conteneur précréées ont été utilisées pour créer un service Docker. Le code de l’application associé, Dockerfile, et le fichier Compose sont disponibles sur GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez déployé un cluster Docker Swarm et y avez déployé une application multiconteneur.

Pour en savoir plus sur l’intégration de Docker Swarm à Azure DevOps, passez à la section CI/CD avec Docker Swarm et Azure DevOps.

> [!div class="nextstepaction"]
> [Intégration et déploiement continus avec Docker Swarm et Azure DevOps](./container-service-docker-swarm-setup-ci-cd.md)
