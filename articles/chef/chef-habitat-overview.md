---
title: Utiliser Habitat pour déployer rapidement votre application dans Azure
description: Découvrez comment déployer votre application de manière cohérente sur des machines virtuelles et des conteneurs Azure
keywords: azure, chef, devops, machines virtuelles, présentation, automate, habitat
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267007"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Utiliser Habitat pour déployer rapidement votre application dans Azure
[Habitat](https://www.habitat.sh/) est un tout nouveau type de projet open source, qui offre une approche entièrement nouvelle de la gestion des applications. Avec Habitat, l’application et son automatisation constituent l’unité de déploiement. Lorsque les applications sont wrappées dans un « habitat » léger, l’environnement d’exécution, qu’il s’agisse d’un conteneur, d’un système nu ou d’un système PaaS, n’est plus au centre du processus et ne contraint donc plus l’application. 

Cet article décrit les avantages que présente l’utilisation d’Habitat.

## <a name="support-for-the-modern-application"></a>Prise en charge des applications modernes
Les packages Habitat comprennent tout ce dont une application a besoin pour s’exécuter tout au long de son cycle de vie. Les principaux composants d’Habitat sont les suivants :
- Format du package : les applications d’un package Habitat sont atomiques, immuables et auditables.
- Le superviseur Habitat exécute les packages d’applications en connaissant les relations homologues, la stratégie de mise à niveau et les stratégies de sécurité des packages. Le superviseur Habitat configure et gère l’application, quel que soit l’environnement.
- L’écosystème Habitat fournit également un service de génération qui prend un plan de génération Habitat, crée le package Habitat, puis le publie dans un dépôt.

## <a name="run-any-application-anywhere"></a>Exécuter n’importe quelle application dans n’importe quel environnement
Avec Habitat, les applications peuvent s’exécuter dans n’importe quel environnement d’exécution, sans nécessiter de modifications. Cela inclut tous les types d’environnements, du système nu aux machines virtuelles, en passant par les conteneurs (tels que Docker), les systèmes de gestion de cluster (comme Mesosphere ou Kubernetes) et les systèmes PaaS (comme Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Porter facilement les applications héritées
Lorsque les applications héritées sont wrappées dans un package Habitat, elles sont indépendantes de l’environnement pour lequel elles ont été conçues à l’origine. Les packages peuvent être rapidement déplacés vers des environnements plus modernes, tels que le cloud ou des conteneurs. En outre, étant donné que les packages Habitat ont une interface standard, tournée vers l’extérieur, les applications héritées deviennent beaucoup plus faciles à gérer.

## <a name="improve-the-container-experience"></a>Améliorer l’utilisation des conteneurs
Habitat simplifie la gestion des conteneurs dans les environnements de production. En automatisant la configuration des applications dans un conteneur, Habitat facilite la tâche des développeurs qui doivent déplacer des applications basées sur un conteneur entre un environnement de développement et un environnement de production.

## <a name="integrate-into-the-chef-devops-workflow"></a>Intégration au flux de travail Chef DevOps
Le projet Habitat est financé par Chef. Habitat s’appuie sur l’expérience de Chef concernant l’automatisation d’infrastructure afin d’ajouter des fonctionnalités d’automatisation inédites aux applications. Chef proposera une prise en charge commerciale pour Habitat et garantira une intégration transparente entre Habitat et Chef Delivery afin d’automatiser entièrement le cycle de publication des applications, du développement jusqu’au déploiement.

## <a name="next-steps"></a>Étapes suivantes
* [Créer une machine virtuelle Windows dans Azure avec Chef](/azure/virtual-machines/windows/chef-automation)