---
title: À propos des maillages de services
description: Obtenez une vue d’ensemble des maillages de services, des scénarios pris en charge, des critères de sélection et des étapes suivantes à explorer.
author: pgibson
ms.topic: article
ms.date: 07/29/2021
ms.author: pgibson
ms.openlocfilehash: f1821bd9af9c09da1c5fb3ae80145dba3cc38b09
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434301"
---
# <a name="about-service-meshes"></a>À propos des maillages de services

Un maillage de services offre des fonctionnalités axées sur les aspects suivants : gestion du trafic, résilience, stratégie, sécurité, identité forte et observabilité des charges de travail. Votre application est dissociée de ces fonctionnalités opérationnelles, lesquelles sont déplacées par le maillage de services hors de la couche Application, au niveau de la couche d’infrastructure.

## <a name="scenarios"></a>Scénarios

Voici quelques-uns des scénarios que vous pouvez mettre en œuvre avec vos charges de travail quand vous utilisez un maillage de services :

- **Chiffrer tout le trafic dans le cluster** : activez le protocole TLS mutuel entre services spécifiés dans le cluster. Ce scénario peut être étendu à l’entrée et à la sortie du périmètre de réseau. Il fournit une option sécurisée par défaut ne nécessitant aucune modification du code et de l’infrastructure de l’application.

- **Lancements de versions canary et par phases** : spécifiez les conditions de routage d’un sous-ensemble de trafic vers un ensemble de nouveaux services dans le cluster. En cas de test réussi d’une version canary, supprimez le routage conditionnel et augmentez progressivement le pourcentage du trafic vers le nouveau service. Au final, tout le trafic doit être dirigé vers le nouveau service.

- **Gestion et manipulation du trafic** : créez une stratégie sur un service qui limite le débit du trafic vers une version d’un service à partir d’une origine spécifique. Ou créez une stratégie qui applique une stratégie de nouvelle tentative aux classes de défaillances entre services spécifiés. Mettez en miroir le trafic en direct vers les nouvelles versions des services pendant une migration ou pour déboguer des problèmes. Injectez des erreurs entre les services dans un environnement de test pour tester la résilience.

- **Observabilité** : obtenez des insights sur le mode de connexion de vos services au trafic qui transite entre eux. Obtenez des métriques, des journaux et des traces pour l’ensemble du trafic dans le cluster et pour les entrées/sorties. Ajoutez des fonctionnalités de traçage distribué à vos applications.

## <a name="selection-criteria"></a>Critères de sélection

Avant de sélectionner un maillage de services, veillez à bien comprendre vos exigences et les raisons pour lesquelles vous souhaitez en installer un. Posez les questions suivantes.

- **Un contrôleur d’entrée est-il suffisant pour mes besoins ?** Parfois, la mise en place d’une fonctionnalité telle qu’un test a/b ou une répartition du trafic à l’entrée est suffisante pour prendre en charge le scénario requis. N’augmentez pas le niveau de complexité de votre environnement si cela n’en vaut pas la peine.

- **Mes charges de travail et mon environnement peuvent-ils tolérer les surcharges supplémentaires ?** Tous les composants supplémentaires requis pour prendre en charge le maillage de services nécessitent davantage de ressources, notamment en termes de processeur et de mémoire. De plus, tous les proxys et les vérifications de stratégie associées ajoutent de la latence à votre trafic. Si vous avez des charges de travail très sensibles à la latence ou êtes dans l’impossibilité de fournir des ressources supplémentaires pour répondre aux besoins des composants du maillage de services, reconsidérez votre décision.

- **Cela ajoute-t-il une complexité supplémentaire inutile ?** Si vous installez un maillage de services dans le but d’obtenir une fonctionnalité non critique pour l’entreprise ou les équipes opérationnelles, déterminez si l’accroissement de la complexité résultant des tâches d’installation, de maintenance et de configuration en vaut la peine.

- **Est-il possible de suivre une approche incrémentielle ?** Vous pouvez adopter certains maillages de services proposant un grand nombre de fonctionnalités de manière plus incrémentielle. Dans ce cas, vous installez uniquement les composants nécessaires à votre réussite. Une fois que vous avez gagné en confiance, explorez d’autres fonctionnalités à mesure que de nouveaux besoins émergent. Résistez à l’envie d’installer *tout* dès le début.

## <a name="next-steps"></a>Étapes suivantes

À l’étape suivante, explorez Open Service Mesh (OSM) sur Azure Kubernetes Service (AKS) :

> [!div class="nextstepaction"]
> [En savoir plus sur OMS...][osm-about]

Vous pouvez également explorer les maillages de services suivants sur Azure Kubernetes Service (AKS) via la documentation complète du projet disponible pour chacun d’entre eux :

- [Istio][istio]
- [Linkerd][linkerd]
- [Consul Connect][consul]

Si vous souhaitez en savoir plus sur le paysage de maillage de service, l’ensemble plus vaste de maillages de services disponibles, les outils et la conformité, explorez les éléments suivants :

- [Paysage de maillage de service de la Couche 5][service-mesh-landscape]

Vous pouvez également explorer les différents efforts de normalisation de maillage de service :

- [SMI (Service Mesh Interface)][smi]
- [Fédération de maillage de service][smf]
- [Performances du maillage de service (SMP)][smp]


<!-- LINKS - external -->
[istio]: https://istio.io/latest/docs/setup/install/
[linkerd]: https://linkerd.io/getting-started/
[consul]: https://learn.hashicorp.com/tutorials/consul/service-mesh-deploy
[service-mesh-landscape]: https://layer5.io/service-mesh-landscape
[smi]: https://smi-spec.io/
[smf]: https://github.com/vmware/hamlet
[smp]: https://github.com/service-mesh-performance/service-mesh-performance

<!-- LINKS - internal -->
[osm-about]: ./open-service-mesh-about.md
