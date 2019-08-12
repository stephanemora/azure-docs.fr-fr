---
title: Exécuter le service Azure Kubernetes - Analyse de texte
titleSuffix: Azure Cognitive Services
description: Déployez les conteneurs Analyse de texte avec l’image Analyse des sentiments sur Azure Kubernetes Service, puis testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552275"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>Déployer un conteneur Analyse des sentiments sur Azure Kubernetes Service (AKS)

Découvrez comment déployer les conteneurs [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Cognitive Services Azure avec l’image Analyse des sentiments sur Azure Kubernetes Service (AKS). Cette procédure illustre la création d’une ressource Analyse de texte, la création d’une image Analyse des sentiments associée et l’orchestration des deux depuis un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

## <a name="prerequisites"></a>Prérequis

Cette procédure nécessite plusieurs outils qui doivent être installés et exécutés localement. N’utilisez pas Azure Cloud Shell. Vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un éditeur de code, par exemple [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installé.
* The [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installé.
* Une ressource Azure avec le niveau de tarification approprié. Certains niveaux tarifaires ne fonctionnent pas avec ce conteneur :
    * La ressource **Analyse de texte Azure** uniquement avec les niveaux tarifaires F0 ou Standard.
    * La ressource **Azure Cognitive Services** avec un niveau tarifaire S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>Déployer un conteneur Analyse de texte sur un cluster AKS

1. Ouvrir Azure CLI et se connecter à Azure.

    ```azurecli
    az login
    ```

1. Connectez-vous au cluster AKS. Remplacez `your-cluster-name` et `your-resource-group` par les valeurs appropriées.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Une fois cette commande exécutée, elle signale un message similaire au message suivant :

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Si plusieurs abonnements sont disponibles sur votre compte Azure et que la commande `az aks get-credentials` renvoie une erreur, le problème peut être que vous n’utilisez pas l’abonnement approprié. Définissez le contexte de votre session Azure CLI pour utiliser le même abonnement avec lequel vous avez créé les ressources, puis réessayez.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Ouvrez l’éditeur de texte de votre choix. L’exemple suivant utilise Visual Studio Code.

    ```azurecli
    code .
    ```

1. Dans l’éditeur de texte, créez un nouveau fichier nommé _sentiment.yaml_ et collez-y le fichier YAML suivant. Assurez-vous de remplacer les valeurs `billing/value` et `apikey/value` par vos propres informations.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Enregistrez le fichier et fermez l’éditeur de texte.
1. Exécutez la commande Kubernetes `apply` avec le fichier _sentiment.yaml_ comme cible :

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Une fois la configuration de déploiement correctement appliquée par la commande, un message similaire à la sortie suivante s’affiche :

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Vérifiez que le pod a été déployé :

    ```console
    kubectl get pods
    ```

    Vous obtiendrez ainsi l’état d’exécution du pod :

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Vérifiez que le service est disponible et obtenez l’adresse IP.

    ```console
    kubectl get services
    ```

    La sortie de l’état d’exécution du service _sentiment_ dans le pod :

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utiliser davantage de [conteneurs Cognitive Services](../../cognitive-services-container-support.md)
* Utiliser le [service connecté Analytique de texte](../vs-text-connected-service.md)
