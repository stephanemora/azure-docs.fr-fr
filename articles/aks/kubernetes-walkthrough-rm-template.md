---
title: 'Démarrage rapide : créer un cluster AKS (Azure Kubernetes Service)'
description: Découvrez comment créer rapidement un cluster Kubernetes à l’aide d’un modèle Azure Resource Manager et déployer une application dans AKS (Azure Kubernetes Service)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: ea66651818f92dec05de4830726c999d75e5196b
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108279709"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Démarrage rapide : Déployer un cluster AKS (Azure Kubernetes Service) à l’aide d’un modèle ARM

AKS (Azure Kubernetes Service) est un service Kubernetes managé qui vous permet de déployer et de gérer rapidement des clusters. Dans ce guide de démarrage rapide, vous allez :
* Déployer un cluster AKS à l’aide d’un modèle Azure Resource Manager. 
* Exécutez une application à plusieurs conteneurs avec un serveur web frontal et une instance Redis dans le cluster. 

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ce guide de démarrage rapide suppose une compréhension élémentaire des concepts liés à Kubernetes. Pour plus d’informations, consultez [Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)][kubernetes-concepts].

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.kubernetes%2Faks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.0.61 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Pour créer un cluster AKS à l’aide d’un modèle Resource Manager, vous fournissez une clé publique SSH. Si vous avez besoin de cette ressource, consultez la section suivante ; sinon, passez à la section [Vérifier le modèle](#review-the-template).

### <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH

Pour accéder aux nœuds AKS, vous vous connectez à l’aide d’une paire de clés SSH (publique et privée), que vous générez à l’aide de la commande `ssh-keygen`. Par défaut, ces fichiers sont créés dans le répertoire *~/.ssh*. L’exécution de la commande `ssh-keygen` remplace toutes les paires de clés SSH portant le même nom existant déjà dans l’emplacement donné.

1. Accédez à [https://shell.azure.com](https://shell.azure.com) pour ouvrir Cloud Shell dans votre navigateur.

1. Exécutez la commande `ssh-keygen`. L’exemple suivant crée une paire de clés SSH à l’aide du chiffrement RSA avec une longueur de 2048 :

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Pour plus d’informations sur la création de clés SSH, consultez [Créer et gérer les clés SSH pour l’authentification dans Azure][ssh-keys].

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/aks/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.kubernetes/aks/azuredeploy.json":::

Pour plus d’exemples AKS, consultez le site [Modèles de démarrage rapide AKS][aks-quickstart-templates].

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez le bouton suivant pour vous connecter à Azure et ouvrir un modèle.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.kubernetes%2Faks%2Fazuredeploy.json)

2. Sélectionnez ou entrez les valeurs suivantes.

    Pour ce guide de démarrage rapide, conservez les valeurs par défaut pour *Taille du disque du système d’exploitation en Go*, *Nombre d’agents*, *Taille de machine virtuelle de l’agent*, *Type de système d’exploitation* et *Version de Kubernetes*. Fournissez vos propres valeurs pour les paramètres de modèle suivants :

    * **Abonnement**: Sélectionnez un abonnement Azure.
    * **Groupe de ressources** : Sélectionnez **Créer nouveau**. Entrez un nom unique pour le groupe de ressources, tel que *myResourceGroup*, puis choisissez **OK**.
    * **Emplacement** : sélectionnez un emplacement, comme **USA Est**.
    * **Nom de cluster** : entrez un nom unique pour le cluster AKS, tel que *myAKSCluster*.
    * **Préfixe DNS** : entrez un préfixe DNS unique pour votre cluster, tel que *myakscluster*.
    * **Nom de l’utilisateur administrateur Linux** : entrez un nom d’utilisateur pour se connecter à l’aide de SSH, tel qu’*azureuser*.
    * **Clé publique RSA SSH** : copiez et collez la partie *publique* de votre paire de clés SSH (par défaut, le contenu de *~/.ssh/id_rsa.pub*).

    ![Modèle Resource Manager pour créer un cluster Azure Kubernetes Service dans le portail](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Sélectionnez **Vérifier + créer**.

La création du cluster AKS ne prend que quelques minutes. Attendez que le cluster soit correctement déployé pour passer à l’étape suivante.

## <a name="validate-the-deployment"></a>Valider le déploiement

### <a name="connect-to-the-cluster"></a>Se connecter au cluster

Pour gérer un cluster Kubernetes, utilisez [kubectl][kubectl], le client de ligne de commande Kubernetes. Si vous utilisez Azure Cloud Shell, `kubectl` est déjà installé. 

1. Installez `kubectl` en local avec la commande [az aks install-cli][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Configurez `kubectl` afin de vous connecter à votre cluster Kubernetes avec la commande [az aks get-credentials][az-aks-get-credentials]. Cette commande télécharge les informations d’identification et configure l’interface CLI Kubernetes pour les utiliser.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Pour vérifier la connexion à votre cluster, exécutez la commande [kubectl get][kubectl-get]. Cette commande renvoie la liste des nœuds de cluster.

    ```console
    kubectl get nodes
    ```

    La sortie montre les nœuds créés au cours des étapes précédentes. Vérifiez que l’état de tous les nœuds est *Ready* :

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Exécution de l'application

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

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    La sortie affiche les déploiements et services créés avec succès :

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

### <a name="test-the-application"></a>Test de l’application

Quand l’application s’exécute, un service Kubernetes expose le front-end de l’application sur Internet. L’exécution de ce processus peut prendre plusieurs minutes.

Surveillez la progression avec la commande [kubectl get service][kubectl-get] et l’argument `--watch`.

```console
kubectl get service azure-vote-front --watch
```

La sortie **EXTERNAL-IP** pour le service `azure-vote-front` affiche initialement *En attente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quand l’adresse **EXTERNAL-IP** passe de l’état *pending* à une adresse IP publique réelle, utilisez `CTRL-C` pour arrêter le processus de surveillance `kubectl`. L’exemple de sortie suivant montre une adresse IP publique valide affectée au service :

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pour voir l’application Azure Vote en action, ouvrez un navigateur web en utilisant l’adresse IP externe de votre service.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour éviter les frais Azure, vous devez nettoyer les ressources non nécessaires. Utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, le service conteneur ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
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
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
