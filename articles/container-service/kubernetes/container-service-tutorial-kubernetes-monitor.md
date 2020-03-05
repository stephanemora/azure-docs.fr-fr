---
title: (DÉPRÉCIÉ) Tutoriel Azure Container Service – Superviser Kubernetes
description: Didacticiel Azure Container Service - Surveiller Kubernetes avec Log Analytics
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84c2438a8c25b1b64f46e12923212812beac687d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273320"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>(DÉPRÉCIÉ) Superviser un cluster Kubernetes avec Log Analytics

> [!TIP]
> Pour obtenir la version mise à jour de ce tutoriel qui utilise Azure Kubernetes Service, consultez [Vue d’ensemble d’Azure Monitor pour conteneurs (préversion)](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

La surveillance de votre cluster Kubernetes et des conteneurs est cruciale, particulièrement lorsque vous gérez un cluster de production à grande échelle avec plusieurs applications.

Vous pouvez tirer parti de plusieurs solutions de surveillance Kubernetes, à partir de Microsoft ou d’autres fournisseurs. Dans ce didacticiel, vous surveillez votre cluster Kubernetes à l’aide de la solution Containers dans [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), la solution de gestion informatique de Microsoft. (La solution Containers est en préversion.)

Dans ce didacticiel (le dernier d’une série de sept), les tâches suivantes sont abordées :

> [!div class="checklist"]
> * Obtenir les paramètres d’espace de travail Log Analytics
> * Configurer les agents Log Analytics sur les nœuds Kubernetes
> * Accéder aux informations de surveillance dans le portail Log Analytics ou le portail Azure

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans des images conteneur, ces images chargées sur Azure Container Registry et un cluster Kubernetes créé.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Obtenir les paramètres de l’espace de travail

Lorsque vous pouvez accéder au [portail Log Analytics](https://mms.microsoft.com), allez dans **Paramètres** > **Sources connectées** > **Serveurs Linux**. Là, vous trouvez l’*ID de l’espace de travail* et une *Clé de l’espace de travail* principal ou secondaire. Prenez en note ces valeurs, elles vous sont nécessaires pour configurer les agents Log Analytics sur le cluster.

## <a name="create-kubernetes-secret"></a>Créer une clé secrète Kubernetes

Stockez les paramètres d’espace de travail Log Analytics dans un secret Kubernetes nommé `omsagent-secret` à l’aide de la commande [kubectl create secret][kubectl-create-secret]. Mettez à jour `WORKSPACE_ID` avec votre ID d’espace de travail Log Analytics et `WORKSPACE_KEY` avec la clé de l’espace de travail.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Configurer les agents Log Analytics

Le fichier manifeste Kubernetes suivant peut être utilisé pour configurer les agents de surveillance des conteneurs sur un cluster Kubernetes. Il crée un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes qui exécute un seul et même pod sur chaque nœud de cluster.

Enregistrez le texte suivant dans un fichier nommé `oms-daemonset.yaml`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

Créer le DaemonSet à l’aide de la commande suivante :

```console
kubectl create -f oms-daemonset.yaml
```

Pour vérifier que le DaemonSet est créé, exécutez :

```console
kubectl get daemonset
```

Le résultat ressemble à ce qui suit :

```output
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Dès lors que les agents sont en cours d’exécution, quelques minutes sont nécessaires à Log Analytics pour ingérer et traiter les données.

## <a name="access-monitoring-data"></a>Accéder aux données de surveillance

Affichez et analysez le conteneur surveillant les données au moyen de la [solution Containers](../../azure-monitor/insights/containers.md), dans le portail Log Analytics ou le portail Azure.

Pour installer la solution Containers à l’aide du [portail Log Analytics](https://mms.microsoft.com), accédez à **Galerie de solutions**. Ensuite, ajoutez **Solution Containers**. Vous pouvez également ajouter la solution Containers à partir d’[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Dans le portail Log Analytics, recherchez une vignette récapitulative **Containers** sur le tableau de bord. Cliquez sur la vignette pour obtenir plus d’informations, notamment sur les événements de conteneur, les erreurs, l’état, l’inventaire des images et l’utilisation du processeur et de la mémoire. Pour obtenir des informations plus précises, cliquez sur une ligne dans une vignette, ou effectuez une [recherche dans les journaux](../../log-analytics/log-analytics-log-searches.md).

![Tableau de bord Containers dans le portail Azure](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

De même, dans le portail Azure, accédez à **Log Analytics** et sélectionnez le nom de votre espace de travail. Pour voir la vignette récapitulative **Containers**, cliquez sur **Solutions** > **Containers**. Pour obtenir plus de détails, cliquez sur la vignette.

Consultez la [documentation Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md) pour obtenir des instructions détaillées sur l’interrogation et l’analyse des données de surveillance.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez surveillé votre cluster Kubernetes avec Log Analytics. Les tâches traitées ont inclus :

> [!div class="checklist"]
> * Obtenir les paramètres d’espace de travail Log Analytics
> * Configurer les agents Log Analytics sur les nœuds Kubernetes
> * Accéder aux informations de surveillance dans le portail Log Analytics ou le portail Azure


Suivez ce lien pour consulter des exemples de scripts prédéfinis pour Container Service.

> [!div class="nextstepaction"]
> [Exemples de scripts Azure Container Service](cli-samples.md)
