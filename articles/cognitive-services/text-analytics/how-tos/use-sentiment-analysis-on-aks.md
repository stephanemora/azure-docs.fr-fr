---
title: Exécuter Azure Kubernetes Services
titleSuffix: Text Analytics - Azure Cognitive Services
description: Déployez les conteneurs Analyse de texte avec l’image Analyse des sentiments sur Azure Kubernetes Service, puis testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a419ed3b9c0d2c4db9c552642dc5c662786f6730
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561253"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Déployer un conteneur Analyse des sentiments sur Azure Kubernetes Service (AKS)

Découvrez comment déployer les conteneurs [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Cognitive Services avec l’image Analyse des sentiments sur Azure Kubernetes Service (AKS). Cette procédure illustre la création d’une ressource Analyse de texte, la création d’une image Analyse des sentiments associée et la possibilité de procéder à l’orchestration des deux depuis un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

## <a name="prerequisites"></a>Prérequis

Cette procédure nécessite plusieurs outils qui doivent être installés et exécutés localement. N’utilisez pas Azure Cloud Shell.

* Utilisez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Éditeur de texte, par exemple : [Visual Studio Code](https://code.visualstudio.com/download).
* Installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Installez l’[interface de ligne de commande Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Une ressource Azure avec le niveau de tarification approprié. Certains niveaux tarifaires ne fonctionnent pas avec ce conteneur :
    * La ressource **Analytique de texte** uniquement avec les niveaux tarifaires F0 ou Standard.
    * La ressource **Cognitive Services** avec un niveau tarifaire S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Déployer un conteneur Analyse de texte sur un cluster AKS

1. Ouvrez l’interface Azure CLI et se connecter à Azure

    ```azurecli
    az login
    ```

1. Connectez-vous au cluster AKS (remplacez `your-cluster-name` et `your-resource-group` par les valeurs appropriées)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Une fois cette commande exécutée, elle signale un message similaire au message suivant :

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Si plusieurs abonnements sont disponibles sur votre compte Azure et que la commande `az aks get-credentials` renvoie une erreur, le problème peut être que vous n’utilisez pas l’abonnement approprié. Définissez simplement le contexte de votre session Azure CLI pour utiliser le même abonnement avec lequel vous avez créé les ressources, puis réessayez.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Ouvrez l’éditeur de texte de votre choix (cet exemple utilise __Visual Studio Code__) :

    ```azurecli
    code .
    ```

1. Dans l’éditeur de texte, créez un nouveau fichier nommé _sentiment.yaml_ et collez-y le fichier YAML suivant. Assurez-vous de remplacer les valeurs `billing/value` et `apikey/value` par les vôtres.

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
1. Exécutez la commande `apply` Kubernetes avec le fichier _sentiment.yaml_ comme cible :

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Une fois la configuration de déploiement correctement appliquée par la commande, un message similaire à la sortie suivante s’affiche :

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Vérifiez que le POD a été déployé :

    ```console
    kubectl get pods
    ```

    Vous obtiendrez ainsi l’état d’exécution du POD :

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Vérifiez que le service est disponible et obtenez l’adresse IP :

    ```console
    kubectl get services
    ```

    Vous obtiendrez ainsi l’état d’exécution du service _sentiment_ dans le POD :

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Étapes suivantes

* Utiliser davantage de [conteneurs Cognitive Services](../../cognitive-services-container-support.md)
* Utiliser le [service connecté Analyse de texte](../vs-text-connected-service.md)
