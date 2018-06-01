---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec les conteneurs et les microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198756"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
L’environnement Azure Dev Spaces simplifie le développement sur Kubernetes. En outre, Azure Dev Spaces enrichit vos conteneurs Azure Kubernetes de fonctionnalités de développement complètes, telles que le débogage, et vous permet de développer de manière itérative des conteneurs dans le cloud à l’aide d’outils courants, comme Visual Studio Code, Visual Studio ou la ligne de commande. Azure Dev Spaces se révèle particulièrement utile dans le cadre du développement en équipe, dans lequel l’isolation des différentes branches de code dans un espace qui leur est propre constitue une phase cruciale du cycle de vie de développement.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Simplification du développement Kubernetes avec Azure Dev Spaces 

Cette approche comporte plusieurs avantages :

* Bénéficiez d’un environnement de développement à infrastructure allégée parfaitement représentatif de la production, avec un accès complet aux ressources cloud.
* Déboguez des conteneurs Node.js et .NET Core directement dans Kubernetes avec VS Code ou Visual Studio. Vous pouvez développer dans tous les autres langages à l’aide de l’interface de ligne de commande.
* Partagez une instance Kubernetes avec l’ensemble de votre équipe de développement afin de réduire les coûts et de minimiser les tâches de configuration d’ordinateurs locaux pour les nouveaux membres de l’équipe.
* Développez votre code de manière isolée et procédez à des tests de bout en bout avec d’autres composants sans avoir à répliquer ni simuler de dépendances.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)