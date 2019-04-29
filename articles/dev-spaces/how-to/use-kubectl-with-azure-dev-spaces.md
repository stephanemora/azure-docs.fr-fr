---
title: Comment utiliser kubectl avec des espaces de développement Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686322"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Utiliser kubectl avec un espace Azure Dev Spaces

Vous pouvez accéder au cluster Kubernetes dans un espace Azure Dev Spaces et utiliser les outils Kubernetes existants tels que `kubectl`.

Étant donné que l’exécution de la commande `az aks use-dev-spaces` ajoute automatiquement un contexte de configuration `kubectl`, kubectl doit déjà être connecté à votre cluster Kubernetes Azure Dev Spaces. Exemples :
- Vérifiez le contexte actuel : `kubectl config current-context`
- Répertoriez tous les contextes disponibles : `kubectl config get-contexts`. 
- Changez de contexte : `kubectl config use-context <context-name>`
- Affichez le tableau de bord Kubernetes : exécutez `kubectl proxy`, puis ouvrez votre navigateur à l’adresse émise par cette commande (ajoutez `/ui` à l’URL pour accéder au tableau de bord Kubernetes).
- Répertoriez les services en cours d’exécution dans l’espace Azure Dev Spaces par défaut nommé *default* : `kubectl get services --namespace=default`

