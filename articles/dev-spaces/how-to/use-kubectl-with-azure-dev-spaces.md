---
title: Guide pratique pour utiliser kubectl avec Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198739"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Utiliser kubectl avec un espace Azure Dev Spaces

Vous pouvez accéder au cluster Kubernetes dans un espace Azure Dev Spaces et utiliser les outils Kubernetes existants tels que `kubectl`.

Étant donné que l’exécution de `azds resource create` ou de `azds resource select` ajoute automatiquement un contexte de configuration `kubectl`, kubectl doit déjà être connecté à votre cluster Kubernetes Azure Dev Spaces. Exemples :
- Vérifiez le contexte actuel : `kubectl config current-context`
- Répertoriez tous les contextes disponibles : `kubectl config get-contexts`. 
- Changez de contexte : `kubectl config use-context <context-name>`
- Affichez le tableau de bord Kubernetes : exécutez `kubectl proxy`, puis ouvrez votre navigateur à l’adresse émise par cette commande (ajoutez `/ui` à l’URL pour accéder au tableau de bord Kubernetes).
- Répertoriez les services en cours d’exécution dans l’espace Azure Dev Spaces par défaut nommé *default* : `kubectl get services --namespace=default`

