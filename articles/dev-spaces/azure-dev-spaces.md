---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec les conteneurs et les microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: douge
ms.openlocfilehash: 93440b8a1c9fd1b386931e5998c70133071a079e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823042"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces offre aux équipes une expérience de développement Kubernetes rapide et itérative. Avec une configuration minimale de machine de développement, vous pouvez exécuter et déboguer de manière itérative les conteneurs directement dans Azure Kubernetes Service (AKS). Développez sur Windows, Mac ou Linux en utilisant des outils populaires comme Visual Studio, Visual Studio Code ou la ligne de command.

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Simplification du développement Kubernetes avec Azure Dev Spaces 

Azure Dev Spaces permet aux équipes de développement d’être plus productifs sur Kubernetes comme suit :
- Réduit la configuration minimale de machine de développement pour chaque membre de l’équipe et fonctionne directement dans AKS, un cluster Kubernetes géré dans Azure.
- Effectue une itération et débogue le code rapidement directement dans Kubernetes à l’aide de Visual Studio 2017 ou de Visual Studio Code.
- Génère une configuration Docker et Kubernetes actifs en tant que configuration sous forme de code que vous pouvez utiliser du développement jusqu’à la production. 
- Partagez un cluster Kubernetes avec votre équipe et travaillez ensemble en collaboration. Développez votre code de manière isolée et procédez à des tests de bout en bout avec d’autres composants sans avoir à répliquer ni simuler de dépendances.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>Voir aussi

[Azure Kubernetes Service](/azure/aks)