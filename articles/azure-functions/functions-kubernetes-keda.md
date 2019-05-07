---
title: Azure Functions sur Kubernetes avec KEDA
description: Comprendre comment exécuter Azure Functions dans Kubernetes dans le cloud ou localement à l’aide de KEDA, à l’échelle automatique basée sur les événements basées sur Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077619"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions sur Kubernetes avec KEDA

Le runtime Azure Functions fournit une flexibilité d’hébergement où et comment vous souhaitez.  [KEDA](https://github.com/kedacore/kore) (basées sur Kubernetes événement piloté par mise à l’échelle) paires en toute transparence avec le runtime d’Azure Functions et des outils pour fournir une échelle basée sur les événements dans Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Fonctions de travail basées sur les Kubernetes

Le service Azure Functions se compose de deux composants clés : un runtime et un contrôleur de mise à l’échelle.  Le runtime Functions s’exécute et exécute votre code.  Le runtime inclut une logique sur comment déclencher, ouvrez une session et gérer les exécutions de la fonction.  L’autre composant est un contrôleur de mise à l’échelle.  Le contrôleur de mise à l’échelle supervise le taux d’événements qui ciblent votre fonction et proactive met à l’échelle le nombre d’instances exécutant votre application.  Pour plus d’informations, consultez [échelle et hébergement Azure Functions](functions-scale.md).

Fonctions basées sur Kubernetes fournit au runtime de fonctions dans un [conteneur Docker](functions-create-function-linux-custom-image.md) pilotée par événements mise à l’échelle via KEDA.  KEDA peut mettre à l’échelle vers le bas sur des instances 0 (quand aucun événement ne se produit) et jusqu'à *n* instances. Cela en exposant des mesures personnalisées pour l’autoscaler Kubernetes (Horizontal Pod Autoscaler).  À l’aide de conteneurs de fonctions avec KEDA rend possible répliquer les fonctionnalités de fonction sans serveur dans n’importe quel cluster Kubernetes.  Ces fonctions peuvent également être déployées à l’aide de [nœuds virtuels Azure Kubernetes service (AKS)](../aks/virtual-nodes-cli.md) fonctionnalité pour l’infrastructure sans serveur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestion des KEDA et des fonctions dans Kubernetes

Pour exécuter des fonctions sur votre cluster Kubernetes, vous devez installer le composant KEDA. Vous pouvez installer ce composant à l’aide [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installation avec les fonctions Azure Core Tools

Par défaut, Core Tools installe des composants KEDA et Osiris, qui prennent en charge pilotée par événements et la mise à l’échelle de HTTP, respectivement.  L’installation utilise `kubectl` en cours d’exécution dans le contexte actuel.

Installez KEDA dans votre cluster en exécutant la commande d’installation suivantes :

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Déploiement d’une application de fonction vers Kubernetes

Vous pouvez déployer n’importe quelle application de fonction sur un cluster Kubernetes KEDA en cours d’exécution.  Dans la mesure où vos fonctions s’exécutent dans un conteneur Docker, votre projet nécessite un `Dockerfile`.  Si elle n’en possède pas, vous pouvez ajouter un fichier Dockerfile en exécutant la commande suivante à la racine de votre projet de fonctions :

```cli
func init --docker-only
```

Pour créer une image et déployer vos fonctions sur Kubernetes, exécutez la commande suivante :

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Remplacez `<name-of-function-deployment>` par le nom de votre application de fonction.

Cette opération crée un Kubernetes `Deployment` ressource, un `ScaledObject` ressource, et `Secrets`, qui inclut des variables d’environnement importés à partir de votre `local.settings.json` fichier.

## <a name="removing-a-function-app-from-kubernetes"></a>Suppression d’une application de fonction à partir de Kubernetes

Une fois le déploiement vous pouvez supprimer une fonction en supprimant associé `Deployment`, `ScaledObject`, un `Secrets` créé.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Désinstallation KEDA sur Kubernetes

Vous pouvez exécuter la commande d’outils core suivante pour supprimer KEDA à partir d’un cluster Kubernetes :

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Prise en charge des déclencheurs dans KEDA

KEDA est actuellement en version bêta avec prise en charge pour les déclencheurs de fonction Azure suivantes :

* [Files d’attente de stockage Azure](functions-bindings-storage-queue.md)
* [Files d’attente de Bus de Service Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Créer une fonction à l’aide d’une image personnalisée](functions-create-function-linux-custom-image.md)
* [Coder et tester Azure Functions localement](functions-develop-local.md)
* [Fonctionnement du plan de consommation Azure (fonction)](functions-scale.md)