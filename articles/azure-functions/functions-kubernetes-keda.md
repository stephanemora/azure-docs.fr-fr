---
title: Azure Functions sur Kubernetes avec KEDA
description: Apprenez à exécuter Azure Functions dans Kubernetes dans le cloud ou localement à l’aide de KEDA, une mise à l’échelle automatique pilotée par événement basée sur Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 9978bd567b1b07e8dd0e22e1f02834626281a5dd
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920675"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions sur Kubernetes avec KEDA

Le runtime Azure Functions fournit une flexibilité d’hébergement où et comme vous le souhaitez.  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling, ou mise à l’échelle automatique pilotée par événement basée sur Kubernetes) fonctionne parfaitement avec le runtime et les outils Azure Functions pour fournir une échelle pilotée par événement dans Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Fonctionnement des fonctions basées sur Kubernetes

Le service Azure Functions se compose de deux composants clés : un runtime et un contrôleur d’échelle.  Le runtime Functions s’exécute et exécute votre code.  Le runtime inclut une logique sur le déclenchement, la consignation et la gestion des exécutions de fonction.  Le runtime Azure Functions peut s’exécuter *n’importe où*.  L’autre composant est un contrôleur d’échelle.  Le contrôleur d’échelle supervise le taux d’événements qui ciblent votre fonction et met proactivement à l’échelle le nombre d’instances exécutant votre application.  Pour en savoir plus, voir [Mise à l’échelle et hébergement d’Azure Functions](functions-scale.md).

Functions basé sur Kubernetes fournit le runtime Functions dans un [conteneur Docker](functions-create-function-linux-custom-image.md) avec mise à l’échelle piloté par événement via KEDA.  KEDA peut descendre en puissance jusqu’à 0 instance (lorsqu’aucun événement ne se produit) et jusqu’à *n* instances. Pour cela, il expose des métriques personnalisées de l’autoscaler Kubernetes (autoscaler de pods élastique).  L’utilisation de conteneurs Functions avec KEDA permet de répliquer des fonctionnalités de fonction serverless dans n’importe quel cluster Kubernetes.  Ces fonctions peuvent également être déployées à l’aide de la fonctionnalité de [nœuds virtuels Azure Kubernetes Service (AKS)](../aks/virtual-nodes-cli.md) pour l’infrastructure serverless.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestion de KEDA et des fonctions dans Kubernetes

Pour exécuter Functions sur votre cluster Kubernetes, vous devez installer le composant KEDA. Vous pouvez installer ce composant à l’aide d’[Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installation avec Azure Functions Core Tools

Par défaut, Core Tools installe les composants KEDA et Osiris, qui prennent en charge la mise à l’échelle pilotée par événement et HTTP, respectivement.  L’installation utilise `kubectl` s’exécutant dans le contexte actuel.

Installez KEDA dans votre cluster en exécutant la commande d’installation suivante :

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Déploiement d’une application de fonction sur Kubernetes

Vous pouvez déployer n’importe quelle application de fonction sur un cluster Kubernetes en exécutant KEDA.  Dans la mesure où vos fonctions s’exécutent dans un conteneur Docker, votre projet nécessite un `Dockerfile`.  S’il n’en a pas, vous pouvez ajouter un fichier Dockerfile en exécutant la commande suivante à la racine de votre projet Functions :

```cli
func init --docker-only
```

Pour créer une image et déployer vos fonctions sur Kubernetes, exécutez la commande suivante :

> [!NOTE]
> Core Tools tirera parti de l’interface de commande de Docker pour créer et publier l’image. Veillez à ce que Docker soit déjà installé et connecté à votre compte avec `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Remplacez `<name-of-function-deployment>` par le nom de votre application de fonction.

Cette opération crée une ressource Kubernetes `Deployment`, une ressource `ScaledObject` et `Secrets`, qui inclut des variables d’environnement importées de votre fichier `local.settings.json`.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Déployer une application de fonction à partir d’un registre privé

Le flux ci-dessus fonctionne également pour les registres privés.  Si vous extrayez votre image de conteneur à partir d’un registre privé, incluez l’indicateur `--pull-secret` qui référence le secret Kubernetes contenant les informations d’identification de registre privé lors de l’exécution de `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Suppression d’une application de fonction de Kubernetes

Après le déploiement, vous pouvez supprimer une fonction en supprimant le `Deployment` associé, `ScaledObject`, un `Secrets` créé.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Désinstallation de KEDA de Kubernetes

Vous pouvez exécuter la commande Core Tools suivante pour supprimer KEDA d’un cluster Kubernetes :

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Déclencheurs pris en charge dans KEDA

KEDA prend en charge les déclencheurs Azure Function suivants :

* [Files d’attente Stockage Azure](functions-bindings-storage-queue.md)
* [Files d’attente Azure Service Bus](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [File d’attente RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Prise en charge de déclencheur HTTP

Vous pouvez utiliser Azure Functions qui exposent les déclencheurs HTTP, mais KEDA ne les gère pas directement.  Azure Functions Core Tools installe un projet connexe, Osiris, qui permet la mise à l’échelle des points de terminaison HTTP de 0 à 1.  La mise à l’échelle de 1 à *n* repose sur les stratégies de mise à l’échelle Kubernetes traditionnelles.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Créer une fonction avec une image personnalisée](functions-create-function-linux-custom-image.md)
* [Coder et tester Azure Functions localement](functions-develop-local.md)
* [Fonctionnement du plan Consommation Azure Function](functions-scale.md)