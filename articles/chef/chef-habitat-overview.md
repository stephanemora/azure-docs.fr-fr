---
title: Utiliser Habitat pour déployer rapidement votre application dans Azure
description: Découvrez comment déployer votre application de manière cohérente sur des machines virtuelles et des conteneurs Azure
keywords: azure, chef, devops, machines virtuelles, présentation, automate, habitat
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158063"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Utiliser Habitat pour déployer rapidement votre application dans Azure
[Habitat](https://www.habitat.sh/) est un système d’empaquetage et d’exécution d’application qui regroupe l’application et son automatisation en tant qu’unité de déploiement. Cela optimise la portabilité de l’application, qui peut ainsi être déployée sur des conteneurs, des machines virtuelles, des systèmes nus ou PaaS, sans réécriture ou réempaquetage.

Cet article décrit les principaux avantages que présente l’utilisation d’Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Moderniser et déplacer des applications existantes
Libérez les applications existantes des anciens systèmes d’exploitation et des intergiciels (middlewares) en les réempaquetant avec Habitat. L’artefact qui en résulte est portable, et peut facilement changer de plateforme et basculer vers une infrastructure plus récente telle que des machines virtuelles ou des conteneurs en cours d’exécution dans le cloud.

## <a name="accelerate-container-adoption"></a>Accélérer l’adoption des conteneurs
Habitat résout le déploiement continu d’applications complexes et orientées microservices en représentant avec précision les dépendances du runtime. Allez au-delà du simple déploiement bleu/vert des composants individuels, et concevez des architectures de comportement de déploiement sophistiquées sans avoir à générer de flux d’orchestration complexe.

## <a name="run-any-application-anywhere"></a>Exécuter n’importe quelle application dans n’importe quel environnement
Avec Habitat, les applications peuvent s’exécuter dans n’importe quel environnement d’exécution, sans nécessiter de modifications. Cela inclut tous les types d’environnements, du système nu aux machines virtuelles, en passant par les conteneurs (tels que Docker), les systèmes de gestion de cluster (comme Mesosphere ou Kubernetes) et les systèmes PaaS (comme Pivotal Cloud Foundry).

## <a name="integrate-into-the-chef-devops-workflow"></a>Intégration au flux de travail Chef DevOps
Le projet Habitat fait partie d’un projet open source de Chef Software, avec une forte communauté de support. Habitat s’appuie sur l’expérience de Chef concernant l’automatisation d’infrastructure afin d’ajouter des fonctionnalités d’automatisation inédites aux applications. Chef propose une prise en charge commerciale pour Habitat et une intégration fluide entre Habitat et Chef Automate afin d’automatiser entièrement le cycle de publication des applications, du développement jusqu’au déploiement.

## <a name="next-steps"></a>Étapes suivantes

* [Essayez Habitat](https://www.habitat.sh/learn/)
