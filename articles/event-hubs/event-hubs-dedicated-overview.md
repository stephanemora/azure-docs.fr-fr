---
title: Vue d’ensemble d’Event Hubs Dedicated - Azure Event Hubs | Microsoft Docs
description: Cet article fournit une vue d’ensemble de dédié Azure Event Hubs, qui offre des déploiements à locataire unique des concentrateurs d’événements.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4f721dc4fda5bef002c794d79dfd2f054f9eaf38
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511180"
---
# <a name="overview-of-event-hubs-dedicated"></a>Vue d’ensemble d’Event Hubs Dedicated

*Clusters Event Hubs* offre des déploiements à locataire unique pour les clients avec les plus exigeantes en matière de diffusion en continu. Cette offre de locataire unique a un contrat SLA de 99,99 % garanti et est uniquement disponible sur notre dédié de niveau tarifaire. Un cluster de concentrateurs d’événements peuvent être entrés des millions d’événements par seconde avec la garantie de la capacité et la latence de la seconde. Espaces de noms et à event hubs créées au sein du cluster dédié incluent toutes les fonctionnalités de l’offre Standard et bien plus encore, mais sans aucune limitation de l’entrée. Il inclut également le fameux [Event Hubs Capture](event-hubs-capture-overview.md) fonctionnalité sans coût supplémentaire, ce qui vous permet automatiquement batch et le journal des flux de données pour le stockage Azure ou Azure Data Lake. 

Les clusters sont configurés et facturés par **unités de capacité (CUs)**, une quantité préallouer de ressources processeur et mémoire. Vous pouvez acheter 1, 2, 4, 8, 12, 16 ou 20 unités de capacité pour chaque cluster. Combien vous pouvez ingérer et diffuser en continu par unité de capacité dépend de divers facteurs, tels que le nombre de producteurs et consommateurs, forme de la charge utile, sortie de taux (voir les résultats du benchmark ci-dessous pour plus d’informations). 

> [!NOTE]
> Tous les clusters de concentrateurs d’événements sont activés par défaut Kafka et prendre en charge des points de terminaison Kafka qui peuvent être utilisés par votre Kafka en fonction des applications. Avoir Kafka activé sur votre cluster n’affecte pas votre cas d’usage de non-Kafka ; Il n’existe aucune option ou la nécessité de désactiver Kafka sur un cluster.

## <a name="why-dedicated"></a>Pourquoi dédié ?

Dedicated Event Hubs offre trois avantages incontournables pour les clients qui ont besoin de capacité au niveau de l’entreprise :

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Architecture mutualisée unique garantit la capacité pour de meilleures performances

Un cluster dédié garantit la capacité à grande échelle complète, et pouvez entrée jusqu'à gigaoctets de données de diffusion en continu avec une latence de stockage et la seconde une durabilité complète pour prendre en charge ses très élevées en rafales le trafic. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accès aux fonctionnalités inclusif et exclusif 
L’offre dédié inclut des fonctionnalités telles que les capturer à aucun coût supplémentaire, mais aussi un accès exclusif à des fonctionnalités à venir comme BYOK. Le service gère également l’équilibrage de charge, du système d’exploitation mises à jour, correctifs de sécurité et de partitionnement pour le client, afin que vous pouvez passer moins de temps sur la maintenance de l’infrastructure et de plus de temps sur la création de fonctionnalités côté client.  

#### <a name="cost-savings"></a>Réduction des coûts
Si les volumes en entrée élevé (> 100 aux unités de débit), un cluster les coûts de manière significative moins par heure plutôt que d’acheter une quantité comparable d’unités de débit dans l’offre Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated Quotas et limites

L’offre Event Hubs Dedicated est facturé à un tarif mensuel fixe, avec un minimum de 4 heures d’utilisation. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise et les limites pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | standard | Dédié |
| --- |:---:|:---:|
| Bande passante | 20 aux unités de débit (jusqu'à 40 aux unités de débit) | 20 unités de capacité |
| Espaces de noms |  1 | 50 par unité de capacité |
| Event Hubs |  10 | Aucune limite sur event hubs/rubriques |
| Événements d'entrée | Paiement par million d’événements | Inclus |
| Taille des messages | 1 million d’octets | 1 million d’octets |
| Partitions | 40 par espace de noms | 2000 par unité de capacité |
| Groupes de consommateurs | 20 par Event Hub | Aucune limite par unité de capacité, 1000 par event hub |
| Connexions négociées | 1 000 inclus | 100 K inclus |
| Rétention des messages | 7 derniers jours, 84 Go inclus par ma | 90 jours, 10 To inclus par unité de capacité |
| Capturer | Paiement par heure | Inclus |

## <a name="how-to-onboard"></a>Intégration : mode d’emploi

À l’intégration d’Event Hubs Dedicated, veuillez contacter le [équipe de concentrateurs d’événements](mailto:askeventhubs@microsoft.com). Le plan Dedicated est unique dans la mesure où vous bénéficiez d’une intégration plus pratique de la part de l’équipe produit Event Hubs pour obtenir le déploiement flexible qui vous convient. 

## <a name="faqs"></a>FAQ

#### <a name="what-can-i-achieve-with-a-cluster"></a>Ce que puis-je faire avec un cluster ?

Pour un cluster Event Hubs, combien vous pouvez ingérer et diffuser en continu dépend de divers facteurs tels que votre producteurs, consommateurs, la vitesse à laquelle vous êtes ingestion et traitement et bien plus encore. 

Le tableau suivant présente les résultats de référence que nous avons obtenus au cours de nos tests :

| Forme de la charge utile | Récepteurs | Bande passante en entrée| Messages en entrée | Bande passante en sortie | Messages en sortie | Nombre total d’unités de débit | Unités de débit par unité de capacité |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lots de 100x1 Ko | 2 | 400 Mo/s | 400 k messages par seconde | 800 Mo/s | 800 k messages par seconde | 400 unités de débit | 100 unités de débit | 
| Lots de 10x10 Ko | 2 | 666 Mo/s | 66,6 k messages par seconde | 1,33 Go/s | 133 k messages par seconde | 666 unités de débit | 166 unités de débit |
| Lots de 6x32 Ko | 1 | 1,05 Go/s | les messages de 34 Ko / s | 1,05 Go/s | 34 Ko messages par seconde | 1 000 unités de débit | 250 unités de débit |

Lors des tests, les critères suivants ont été utilisés :

- Un cluster de concentrateurs d’événements de niveau dédié avec quatre unités de capacité (CUs) a été utilisé. 
- Le hub d’événements utilisé pour l’ingestion possédait 200 partitions. 
- Les données ingérées ont été reçues par deux applications réceptrices provenant de toutes les partitions.

#### <a name="can-i-scale-down-my-cluster"></a>Puis-je Descendre mon cluster ?

Après la création de clusters sont facturés pour un minimum de 4 heures d’utilisation. Dans la version préliminaire de l’expérience de libre-service, vous pouvez soumettre un [demande de support](https://ms.portal.azure.com/#create/Microsoft.Support) à l’équipe Event Hubs sous *technique > Quota > demande de mise à l’échelle ou de Cluster à l’échelle vers le bas dédié*. Il peut prendre jusqu'à 7 jours pour terminer la demande à l’échelle votre cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Géo-reprise d’activité fonctionnement avec mon cluster ?

Vous pouvez géopaire un espace de noms sous un cluster dédié de couche avec un autre espace de noms sous un cluster dédié de couche. Nous ne pas encourager jumelage niveau dédié d’un espace de noms avec un espace de noms dans notre Standard offre, étant donné que la limite de débit seront incompatibles qui entraîneront des erreurs. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Puis-je migrer Mes espaces de noms Standard pour appartiennent à un cluster dédié couche ?
Nous ne gérons pas actuellement d’un processus de migration automatisée pour la migration de vos données de hubs d’événements à partir d’un espace de noms Standard à un dédié à un. Pour migrer vers un cluster dédié de couche, nous vous recommandons de drainage de n’importe quel gauche des messages dans vos hubs d’événements de niveau Standard et en remplaçant les points de terminaison de connexion par celle de votre espace de noms dédié.

## <a name="next-steps"></a>Étapes suivantes

Contactez votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité des Event Hubs Dedicated. Vous pouvez également en apprendre plus sur les niveaux tarifaires Event Hubs en consultant les liens suivants :

- [Tarification d’Event Hubs Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Vous pouvez également contacter votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité Event Hubs Dedicated.
- L’article [FAQ sur les hubs d’événements](event-hubs-faq.md) traite des informations de tarification et répond à certaines questions fréquemment posées sur Event Hubs.
