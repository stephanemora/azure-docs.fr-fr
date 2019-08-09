---
title: Utiliser une identité managée avec Azure Container Registry Tasks
description: Fournissez un accès aux tâches Azure Container Registry aux ressources Azure, y compris aux autres registres de conteneurs privés, en affectant une identité managée pour les ressources Azure.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148026"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Utiliser une identité managée Azure dans ACR Tasks 

Utilisez une [identité managée pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) pour vous authentifier à partir d’ACR Tasks auprès d’un registre de conteneurs Azure ou d’autres ressources Azure, sans avoir à fournir ni à gérer les informations d’identification à l’aide d’un code. Par exemple, utilisez une identité managée pour extraire (pull) ou envoyer (push) des images de conteneur à un autre registre dans le cadre d’une étape d’une tâche.

Dans cet article, vous découvrirez les identités managées et apprendrez à :

> [!div class="checklist"]
> * Activer une identité affectée par l’utilisateur ou par le système sur une tâche ACR
> * Accorder à l’identité l’accès à des ressources Azure, telles qu’à d’autres registres de conteneurs Azure
> * Utiliser l’identité managée pour accéder aux ressources à partir d’une tâche 

Pour créer les ressources Azure, il est nécessaire dans le cadre de cet article d’exécuter Azure CLI version 2.0.66 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli].

## <a name="why-use-a-managed-identity"></a>Pourquoi utiliser une identité managée ?

Une identité managée pour ressources Azure fournit aux services Azure une identité automatiquement managée dans Azure Active Directory (Azure AD). Vous pouvez configurer [certaines ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), y compris ACR Tasks, avec une identité managée. Ensuite, utilisez l’identité pour accéder aux autres ressources Azure, sans indiquer les informations d’identification dans le code ou les scripts.

Les identités managées sont de deux types :

* Les *identités affectées par l’utilisateur*, que vous pouvez affecter à plusieurs ressources et conserver aussi longtemps que vous le souhaitez. Les identités attribuées par l’utilisateur sont actuellement en préversion.

* Une *identité affectée par le système*, propre à une ressource spécifique, telle qu’une tâche ACR, qui possède la même durée de vie que cette ressource.

Après avoir configuré une ressource Azure avec une identité managée, accordez à l’identité l’accès à une autre ressource, tout comme un principal de sécurité quelconque. Par exemple, donnez-lui un rôle doté des autorisations pull (tirage), push/pull (tirage/envoi) ou d’autres autorisations sur un registre de conteneurs privé dans Azure. (Pour connaître la liste complète des rôles de registre, voir [Rôles et autorisations Azure Container Registry](container-registry-roles.md).) Vous pouvez accorder à une identité l’accès à une ou plusieurs ressources.

## <a name="create-container-registries"></a>Créer des registres de conteneurs

Pour ce tutoriel, vous avez besoin de trois registres de conteneurs :

* Vous utilisez le premier registre pour créer et exécuter les tâches ACR. Dans cet article, ce registre source est nommé *myregistry*. 
* Les deuxième et troisième registres sont des registres cibles pour le premier exemple de tâche visant à envoyer (push) une image qu'il génère. Dans cet article, les registres cibles sont nommés *customregistry1* et *customregistry2*.

Remplacez-les par vos propres noms de registre dans les étapes suivantes.

Si vous n’avez pas encore les registres de conteneurs Azure requis, consultez [Démarrage rapide : Créer un registre de conteneurs privé avec Azure CLI](container-registry-get-started-azure-cli.md). Vous n’avez pas encore besoin d’envoyer (push) des images au registre.

## <a name="example-task-with-a-system-assigned-identity"></a>Exemple : Tâche avec une identité affectée par le système

Cet exemple vous montre comment créer une [tâche à plusieurs étapes](container-registry-tasks-multi-step.md) avec une identité affectée par le système. La tâche génère une image, puis utilise l’identité pour s’authentifier avec deux registres cibles pour envoyer (push) l’image.

Les étapes de cet exemple de tâche sont définies dans un [fichier YAML](container-registry-tasks-reference-yaml.md) nommé `testtask.yaml`. Le fichier se trouve dans le répertoire multipleRegistries du référentiel d’exemples [acr-tasks](https://github.com/Azure-Samples/acr-tasks). Le fichier est reproduit ici :

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Créer une tâche avec une identité affectée par le système

Créez la tâche *multiple-reg* en exécutant la commande [az acr task create][az-acr-task-create] suivante. Le contexte de la tâche est le dossier multipleRegistries du référentiel d’exemples et la commande fait référence au fichier `testtask.yaml` dans ce référentiel. Le paramètre `--assign-identity`, sans valeur supplémentaire, crée une identité affectée par le système pour la tâche. Cette tâche est configurée pour que vous deviez la déclencher manuellement, mais vous pouvez la configurer pour l’exécuter quand les validations sont envoyées (push) au référentiel ou qu’une demande de tirage (pull) est effectuée. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

Dans la sortie de commande, la section `identity` montre qu’une identité de type `SystemAssigned` est définie dans la tâche. `principalId` est l’ID du principal de service de l’identité :

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Utilisez la commande [az acr task show][az-acr-task-show] pour stocker `principalId` dans une variable, afin de l’utiliser dans des commandes ultérieures :

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Accorder des autorisations d’envoi (push) d’identité à deux registres de conteneurs cibles

Dans cette section, accordez à l’identité affectée par le système des autorisations d’envoi (push) aux deux registres cibles, nommés *customregistry1* et *customregistry2*.

Tout d’abord, utilisez la commande [az acr show][az-acr-show] pour obtenir l’ID de ressource de chaque registre et stocker les ID dans des variables :

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Utilisez la commande [az role assignment create][az-role-assignment-create] pour affecter à l’identité le rôle `acrpush` à chaque registre. Ce rôle dispose d’autorisations pour tirer (pull) et envoyer (push) des images à un registre de conteneurs.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Ajouter des informations d’identification de registre cible à la tâche

Utilisez maintenant la commande [az acr task credential add][az-acr-task-credential-add] pour ajouter les informations d’identification de l’identité à la tâche afin qu’elle puisse s’authentifier auprès des deux registres cibles.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Exécuter manuellement la tâche

Utilisez la commande [az acr task run][az-acr-task-run] pour déclencher manuellement la tâche. Le paramètre `--set` est utilisé pour transmettre les noms de serveur de connexion des deux registres cibles en tant que valeurs pour les variables de tâche `REGISTRY1` et `REGISTRY2`.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

Le résultat se présente ainsi :

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Exemple : Tâche avec une identité affectée par l’utilisateur

Dans cet exemple, vous créez une identité affectée par l’utilisateur avec des autorisations pour lire des secrets à partir d’un coffre de clés Azure. Vous affectez cette identité à une tâche à plusieurs étapes qui lit le secret, génère une image et se connecte à Azure CLI pour lire la balise d’image.

### <a name="create-a-key-vault-and-store-a-secret"></a>Créer un coffre de clés et stocker un secret

Commencez, si nécessaire, par créer un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* à l’aide de la commande [az group create][az-group-create] suivante :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilisez la commande [az keyvault create][az-keyvault-create] pour créer un coffre de clés. Veillez à spécifier un nom de coffre de clés unique. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Stockez un exemple de secret dans le coffre de clés à l’aide de la commande [az keyvault secret set][az-keyvault-secret-set] :

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Par exemple, vous souhaiterez peut-être stocker les informations d’identification pour vous authentifier auprès d’un registre Docker privé afin de pouvoir tirer (pull) une image privée.

### <a name="create-an-identity"></a>Créer une identité

Créez une identité nommée *myACRTasksId* dans votre abonnement à l’aide de la commande [az identity create][az-identity-create]. Vous pouvez utiliser le même groupe de ressources qui vous a servi à créer un registre de conteneurs ou un coffre de clés, ou bien un autre élément.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Pour configurer l’identité aux étapes suivantes, utilisez la commande [az identity show][az-identity-show] afin de stocker l’ID de ressource et l’ID de principal de service de l’identité dans des variables.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Accorder l’accès d’identité à un coffre de clés pour lire un secret

Exécutez la commande [az keyvault set-policy][az-keyvault-set-policy] suivante pour définir une stratégie d’accès sur le coffre de clés. L’exemple suivant permet à l’identité affectée par l’utilisateur d’obtenir des secrets du coffre de clés. Cet accès est nécessaire ultérieurement pour exécuter correctement une tâche à plusieurs étapes.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Accorder à l’identité un accès en lecture au groupe de ressources pour le registre

Exécutez la commande [az role assignment create][az-role-assignment-create] suivante pour affecter à l’identité un rôle Lecteur, dans ce cas pour le groupe de ressources contenant le registre source. Ce rôle est nécessaire ultérieurement pour exécuter correctement une tâche à plusieurs étapes.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Créer une tâche avec une identité affectée par l’utilisateur

Créez maintenant une [tâche à plusieurs étapes](container-registry-tasks-multi-step.md) et attribuez-lui l’identité affectée par l’utilisateur. Pour cet exemple de tâche, créez un [fichier YAML](container-registry-tasks-reference-yaml.md) nommé `managed-identities.yaml` dans un répertoire de travail local et collez le contenu suivant. Veillez à remplacer le nom du coffre de clés dans le fichier par le nom de votre coffre de clés

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Cette tâche effectue les actions suivantes :

* Elle vérifie qu’elle peut accéder au secret figurant dans votre coffre de clés. Cette étape sert à des fins de démonstration. Dans un scénario réel, vous pouvez avoir besoin d’une étape de tâche pour obtenir des informations d’identification afin d’accéder à un référentiel privé Docker Hub.
* Elle génère et envoie (push) l’image `mywebsite` dans le registre source.
* Elle se connecte à Azure CLI pour lister les balises d’image `my-website` dans le registre source.

Créez une tâche appelée *msitask* et transmettez-lui l’ID de ressource de l’identité affectée par l’utilisateur que vous avez créée précédemment. Cet exemple de tâche est créé à partir du fichier `managed-identities.yaml` que vous avez enregistré dans votre répertoire de travail local, de sorte que vous devez le déclencher manuellement.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

Dans la sortie de commande, la section `identity` montre qu’une identité de type `UserAssigned` est définie dans la tâche. `principalId` est l’ID du principal de service de l’identité :

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Exécuter manuellement la tâche

Utilisez la commande [az acr task run][az-acr-task-run] pour déclencher manuellement la tâche. Le paramètre `--set` est utilisé pour transmettre le nom du registre source à la tâche :

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

La sortie montre que le secret est résolu, que l’image est générée et envoyée, et que la tâche se connecte à Azure CLI avec l’identité pour lire la balise d’image à partir du registre source :

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les identités managées avec Azure Container Registry Tasks et vous avez appris à :

> [!div class="checklist"]
> * Activer une identité affectée par l’utilisateur ou par le système sur une tâche ACR
> * Accorder à l’identité l’accès à des ressources Azure, telles qu’à d’autres registres de conteneurs Azure
> * Utiliser l’identité managée pour accéder aux ressources à partir d’une tâche  

* En savoir plus sur les [identités managées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
