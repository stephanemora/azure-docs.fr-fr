---
title: Guide pratique pour utiliser kubectl avec Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Découvrez comment utiliser les commandes kubectl dans un espace de développement sur un cluster Azure Kubernetes Service avec Azure Dev Spaces activé.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438364"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Utiliser kubectl avec un espace Azure Dev Spaces

Vous pouvez accéder au cluster Kubernetes dans un espace Azure Dev Spaces et utiliser les outils Kubernetes existants tels que `kubectl`.

Étant donné que l’exécution de la commande `az aks use-dev-spaces` ajoute automatiquement un contexte de configuration `kubectl`, kubectl doit déjà être connecté à votre cluster Kubernetes Azure Dev Spaces. Exemples :
- Vérifiez le contexte actuel : `kubectl config current-context`
- Répertoriez tous les contextes disponibles : `kubectl config get-contexts`. 
- Changez de contexte : `kubectl config use-context <context-name>`
- Affichez le tableau de bord Kubernetes : exécutez `kubectl proxy`, puis ouvrez votre navigateur à l’adresse émise par cette commande (ajoutez `/ui` à l’URL pour accéder au tableau de bord Kubernetes).
- Répertoriez les services en cours d’exécution dans l’espace Azure Dev Spaces par défaut nommé *default* : `kubectl get services --namespace=default`

