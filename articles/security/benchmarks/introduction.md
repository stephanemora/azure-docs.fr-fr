---
title: Introduction au benchmark de sécurité Azure
description: Introduction au benchmark de sécurité
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945336"
---
# <a name="azure-security-benchmark-introduction"></a>Introduction aux benchmarks de sécurité Azure

Vous avez peut-être plusieurs années, voire plusieurs dizaines d’années d’expérience dans l’informatique locale. Vous savez comment sécuriser ces déploiements, mais le cloud est différent. Comment savoir si vos déploiements cloud sont sécurisés ? Quelles sont les différences entre les pratiques de sécurité pour les systèmes locaux et les déploiements cloud ?

Il existe une vaste collection de livres blancs, de meilleures pratiques, d’architectures de référence, d’aide sur le web, d’outils open source, de solutions commerciales, de flux de renseignements, etc. qui peuvent aider à sécuriser le cloud. Quelle option faut-il utiliser ? Que peut-on faire pour bénéficier d’un niveau de sécurité acceptable dans le cloud ? 

L’une des meilleures façons de sécuriser des déploiements cloud est de se concentrer sur les recommandations du benchmark de sécurité dans le cloud. Quel que soit le service à sécuriser, ces recommandations commencent par une compréhension fondamentale du risque en matière de cybersécurité et de sa gestion. Vous pouvez ensuite appliquer ces connaissances en adoptant les recommandations de sécurité du benchmark de votre fournisseur de services cloud pour sélectionner des paramètres de configuration de sécurité spécifiques dans votre environnement. 

Le benchmark de sécurité Azure comprend un ensemble de recommandations de sécurité à fort impact qui peuvent vous permettre de sécuriser la plupart des services que vous utilisez dans Azure. Vous pouvez considérer ces recommandations comme « générales » ou « organisationnelles », car elles s’appliquent à la plupart des services Azure. Les recommandations du benchmark de sécurité Azure sont ensuite personnalisées pour chaque service Azure ; cette aide personnalisée est contenue dans les articles sur les recommandations des services. 

La documentation du benchmark de sécurité Azure indique les contrôles de sécurité et les recommandations de service.

- **Contrôles de sécurité** : les recommandations du benchmark de sécurité Azure sont classées par contrôle de sécurité. Les contrôles de sécurité représentent des exigences de sécurité de haut niveau indépendantes du fournisseur, comme la sécurité réseau et la protection des données. Chacun comporte un ensemble de recommandations de sécurité et d’instructions qui aident à les implémenter. 
- **Recommandations de service** : lorsqu’elles sont disponibles, les recommandations du benchmark pour les services Azure comprennent les recommandations du benchmark de sécurité Azure ajustées spécialement à ce service. 

Les termes « contrôle », « benchmark » et « base de référence » sont souvent utilisés dans la documentation du benchmark de sécurité Azure ; il est important de comprendre comment Azure les utilise. 

| Terme | Description | Exemple |
|--|--|--|
| Control | Un **contrôle** est une description de haut niveau d’une fonctionnalité ou d’une activité à traiter, qui n’est pas propre à une technologie ou à une implémentation. | La protection des données constitue l’un des contrôles de sécurité. Ce contrôle contient des actions spécifiques qui doivent être réglées pour garantir la protection des données. |
| Référence | Un **benchmark** contient des recommandations de sécurité pour une technologie spécifique, comme Azure. Les recommandations sont classées en fonction du contrôle auquel elles appartiennent. | Le benchmark de sécurité Azure comprend les recommandations de sécurité propres à la plateforme Azure.  |
| Ligne de base | Une **base de référence** correspond aux exigences de sécurité d’une organisation, basées sur les recommandations du benchmark. Chaque organisation décide des recommandations du benchmark à inclure dans sa base de référence. | La société Contoso crée sa base de référence de sécurité en choisissant d’exiger des recommandations spécifiques dans le benchmark de sécurité Azure. |

Nous attendons vos commentaires sur le benchmark de sécurité Azure ! Nous vous invitons à les formuler dans la zone de commentaires ci-dessous. Si vous préférez partager votre avis de manière plus privée avec l’équipe de benchmark de sécurité Azure, vous pouvez remplir le formulaire à l’adresse https://aka.ms/AzSecBenchmark. 
