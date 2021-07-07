---
title: 'Démarrage rapide : Déployer un cluster AKS à l’aide de PowerShell'
description: Découvrez comment créer rapidement un cluster Kubernetes, déployer une application et superviser les performances dans AKS (Azure Kubernetes Service) à l’aide de PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0f96ecd00b72f1853fe25b497283ea48ebdf55d7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747794"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Démarrage rapide : Déployer un cluster Azure Kubernetes Service à l’aide de PowerShell

AKS (Azure Kubernetes Service) est un service Kubernetes managé qui vous permet de déployer et de gérer rapidement des clusters. Dans ce guide de démarrage rapide, vous allez :
* Déployer un cluster AKS à l’aide de PowerShell.
* Exécutez une application à plusieurs conteneurs avec un serveur web frontal et une instance Redis dans le cluster.
* Supervisez l’intégrité du cluster et des pods qui exécutent votre application.

Pour en savoir plus sur la création d’un pool de nœuds Windows Server, consultez [Créer un cluster AKS qui prend en charge les conteneurs Windows Server][windows-container-powershell].

![Application de vote déployée dans Azure Kubernetes Service](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Ce guide de démarrage rapide suppose une compréhension élémentaire des concepts liés à Kubernetes. Pour plus d’informations, consultez [Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)][kubernetes-concepts].

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Si vous exécutez PowerShell localement, installez le module Az PowerShell et de vous connecter à votre compte Azure avec l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Pour en savoir plus sur l’installation du module Az PowerShell, consultez [Installer Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous avez plusieurs abonnements Azure, sélectionnez l’ID d’abonnement approprié dans lequel les ressources doivent être facturées avec la cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) est un groupe logique dans lequel des ressources Azure sont déployées et gérées. Lorsque vous créez un groupe de ressources, il vous est demandé de spécifier un emplacement. Cet emplacement est :
* L’emplacement de stockage des métadonnées de votre groupe de ressources.
* Là où vos ressources s’exécutent dans Azure si vous ne spécifiez pas une autre région lors de la création des ressources.

L’exemple suivant crée un groupe de ressources nommé **myResourceGroup** dans la région **USA Est**.

Créez un groupe de ressources avec la cmdlet [New-AzResourceGroup][new-azresourcegroup].

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Sortie pour un groupe de ressources créé avec succès :

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Créer un cluster AKS

1. Utilisez l’utilitaire de ligne de commande `ssh-keygen` pour générer une paire de clés SSH.
    * Pour plus d’informations, consultez [Étapes rapides : Création et utilisation d’une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Créez un cluster AKS en utilisant l’applet de commande [New-AzAksCluster][new-azakscluster]. Azure Monitor pour les conteneurs est activé par défaut.

    L’exemple suivant crée un cluster à un nœud nommé **myAKSCluster**.

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Au bout de quelques minutes, la commande se termine et retourne des informations sur le cluster.

> [!NOTE]
> Lors de la création d’un cluster AKS, un deuxième groupe de ressources est automatiquement créé pour stocker les ressources AKS. Pour plus d’informations, consultez [Pourquoi deux groupes de ressources sont-ils créés avec AKS ?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Se connecter au cluster

Pour gérer un cluster Kubernetes, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé.

1. Pour installer `kubectl` localement avec la cmdlet `Install-AzAksKubectl` :

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Configurez `kubectl` afin de vous connecter à votre cluster Kubernetes avec la cmdlet [Import-AzAksCredential][import-azakscredential]. La cmdlet suivante télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get][kubectl-get]. Cette commande renvoie la liste des nœuds de cluster.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    La sortie montre le nœud unique créé au cours des étapes précédentes. Assurez-vous que l’état du nœud est *Prêt* :

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
    ```

## <a name="run-the-application"></a>Exécution de l'application

Un [fichier manifeste Kubernetes][kubernetes-deployment] définit un état souhaité d’un cluster, notamment les images conteneur à exécuter.

Dans ce guide de démarrage rapide, un manifeste est utilisé afin de créer tous les objets nécessaires pour l’exécution de l’[application Azure Vote][azure-vote-app]. Ce manifeste comprend deux [déploiements Kubernetes][kubernetes-deployment] :
* Exemples d’applications Python pour Azure Vote.
* Une instance Redis.

Deux [services Kubernetes][kubernetes-service] sont également créés :
* Un service interne pour l’instance Redis.
* Un service externe pour accéder à l’application Azure vote à partir d’Internet.

1. Créez un fichier appelé `azure-vote.yaml`.
    * Si vous utilisez Azure Cloud Shell, vous pouvez créer ce fichier à l’aide de `vi` ou `nano` comme si vous travailliez sur un système virtuel ou physique
1. Copiez-y la définition YAML suivante :

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Déployez l’application à l’aide de la commande [kubectl apply][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    La sortie affiche les déploiements et services créés avec succès :

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Test de l’application

Quand l’application s’exécute, un service Kubernetes expose le front-end de l’application sur Internet. L’exécution de ce processus peut prendre plusieurs minutes.

Surveillez la progression avec la commande [kubectl get service][kubectl-get] et l’argument `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

La sortie **EXTERNAL-IP** pour le service `azure-vote-front` affiche initialement *En attente*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quand l’adresse **EXTERNAL-IP** passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`. L’exemple de sortie suivant montre une adresse IP publique valide affectée au service :

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pour voir l’application Azure Vote en action, ouvrez un navigateur web en utilisant l’adresse IP externe de votre service.

![Application de vote déployée dans Azure Kubernetes Service](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Affichez les métriques d’intégrité des nœuds de cluster et des blocs capturées par Azure Monitor pour les conteneurs du portail Azure.

## <a name="delete-the-cluster"></a>Supprimer le cluster

Pour éviter les frais Azure, vous devez nettoyer les ressources non nécessaires. Utilisez la cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] pour supprimer le groupe de ressources, le service conteneur et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Lorsque vous supprimez le cluster, le principal de service Azure Active Directory utilisé par le cluster AKS n’est pas supprimé. Pour obtenir des instructions sur la façon de supprimer le principal de service, consultez [Considérations et suppression du principal de service AKS][sp-delete].
>
> Si vous avez utilisé une identité managée, l’identité est managée par la plateforme et n’a pas besoin d’être supprimée.

## <a name="get-the-code"></a>Obtenir le code

Dans ce guide de démarrage rapide, des images conteneur existant au préalable ont été utilisées pour créer un déploiement Kubernetes. Le code de l’application associé, Dockerfile, et le fichier manifeste Kubernetes sont [disponibles sur GitHub][azure-vote-app].

## <a name="next-steps"></a>Étapes suivantes

Dans ce Démarrage rapide, vous avez déployé un cluster Kubernetes dans lequel vous avez ensuite déployé une application de plusieurs conteneurs. [Accédez au tableau de bord web Kubernetes][kubernetes-dashboard] pour votre cluster AKS.

Pour en savoir plus sur AKS et parcourir le code complet de l’exemple de déploiement, passez au tutoriel sur le cluster Kubernetes.

> [!div class="nextstepaction"]
> [Didacticiel AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
