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
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708016"
---
# <a name="overview-of-event-hubs-dedicated"></a>Vue d’ensemble d’Event Hubs Dedicated

*Clusters Event Hubs* offre des déploiements à locataire unique pour les clients avec les plus exigeantes en matière de diffusion en continu. Cette offre de locataire unique a un contrat SLA de 99,99 % garanti et est uniquement disponible sur notre dédié de niveau tarifaire. Un cluster de concentrateurs d’événements peuvent être entrés des millions d’événements par seconde avec la garantie de la capacité et la latence de la seconde. Espaces de noms et à event hubs créées au sein du cluster dédié incluent toutes les fonctionnalités de l’offre Standard et bien plus encore, mais sans aucune limitation de l’entrée. Il inclut également le fameux [Event Hubs Capture](event-hubs-capture-overview.md) fonctionnalité sans coût supplémentaire, ce qui vous permet automatiquement batch et le journal des flux de données pour le stockage Azure ou Azure Data Lake. 

Des clusters dédiés sont mis en service et facturés par **unités de capacité (CUs)**, une quantité préallouer de ressources processeur et mémoire. Vous pouvez acheter des unités de capacité 1, 2, 4, 8, 12, 16 ou 20 pour chaque cluster. Combien vous pouvez ingérer et diffuser en continu par unité de capacité dépend de divers facteurs, tels que le nombre de producteurs et consommateurs, forme de la charge utile, sortie de taux (voir les résultats du benchmark ci-dessous pour plus d’informations). 

## <a name="why-dedicated"></a>Pourquoi dédié ?

Dedicated Event Hubs offre trois avantages incontournables pour les clients qui ont besoin de capacité au niveau de l’entreprise :

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Architecture mutualisée unique garantit la capacité pour de meilleures performances

Un cluster dédié garantit la capacité à grande échelle complète, et pouvez entrée jusqu'à gigaoctets de données de diffusion en continu avec une latence de stockage et la seconde une durabilité complète pour prendre en charge ses très élevées en rafales le trafic. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accès aux fonctionnalités inclusif et exclusif 
L’offre dédié inclut des fonctionnalités telles que les capturer à aucun coût supplémentaire, mais aussi un accès exclusif à des fonctionnalités à venir comme BYOK. Le service gère également l’équilibrage de charge, du système d’exploitation mises à jour, correctifs de sécurité et de partitionnement pour le client, afin que vous pouvez passer moins de temps sur la maintenance de l’infrastructure et de plus de temps sur la création de fonctionnalités côté client.  

#### <a name="cost-savings"></a>Réduction des coûts
Si les volumes en entrée élevé (> 100 aux unités de débit), un cluster les coûts de manière significative moins par heure plutôt que d’acheter une quantité comparable d’unités de débit dans l’offre Standard.


## <a name="event-hubs-standard-vs-dedicated"></a>Vs Standard des concentrateurs d’événements. Dédié

Le tableau suivant compare les niveaux de service Event Hubs disponibles. L’offre Event Hubs Dedicated est facturé à un tarif mensuel fixe par rapport au prix de l’utilisation pour la plupart des fonctionnalités de la norme. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | standard | Dédié |
| --- |:---:|:---:|
| Événements d’entrée | Paiement par million d’événements | Inclus |
| Unité de débit (1 Mo/s en entrée, 2 Mo/s en sortie) | Paiement par heure | Inclus |
| Taille des messages | 1 Mo | 1 Mo |
| Partitions | 40 par espace de noms | 2000 par unité de capacité |
| Groupes de consommateurs | 20 par Event Hub | 1000 par Event Hub |
| Bande passante Bande passante | 20 aux unités de débit (jusqu'à 40 aux unités de débit)    | 20 unités de capacité |
| Connexions réparties | 1 000 inclus | 100 K inclus |
| Rétention des messages | 1 jour inclus | Jusqu’à 7 jours inclus |
| Capture | Paiement par heure | Inclus |

## <a name="what-can-i-achieve-with-a-cluster"></a>Ce que puis-je faire avec un cluster ?

Pour un cluster Event Hubs, combien vous pouvez ingérer et diffuser en continu dépend de divers facteurs tels que votre producteurs, consommateurs, la vitesse à laquelle vous êtes ingestion et traitement et bien plus encore. 

Le tableau suivant présente les résultats de référence que nous avons obtenus au cours de nos tests :

| Forme de la charge utile | Récepteurs | Bande passante en entrée| Messages en entrée | Bande passante en sortie | Messages en sortie | Nombre total d’unités de débit | Unités de débit par unité de capacité |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lots de 100x1 Ko | 2 | 400 Mo/s | 400 000 msg/s | 800 Mo/s | 800 000 msg/s | 400 unités de débit | 100 unités de débit | 
| Lots de 10x10 Ko | 2 | 666 Mo/s | 66 600 msg/s | 1,33 Go/s | 133 000 msg/s | 666 unités de débit | 166 unités de débit |
| Lots de 6x32 Ko | 1 | 1,05 Go/s | 34 000 msg/s | 1,05 Go/s | 34 000 msg/s | 1 000 unités de débit | 250 unités de débit |

Lors des tests, les critères suivants ont été utilisés :

- Un cluster de concentrateurs d’événements de niveau dédié avec quatre unités de capacité (CUs) a été utilisé. 
- Le hub d’événements utilisé pour l’ingestion possédait 200 partitions. 
- Les données ingérées ont été reçues par deux applications réceptrices provenant de toutes les partitions.

## <a name="how-to-onboard"></a>Intégration : mode d’emploi

Pour commencer à utiliser cette référence SKU, [contactez le support de facturation](https://ms.portal.azure.com/#create/Microsoft.Support) ou votre représentant Microsoft. Vous pouvez faire évoluer votre capacité à la hausse ou à la baisse au cours du mois pour répondre à vos besoins en ajoutant ou en supprimant des unités de capacité. Le plan Dedicated est unique dans la mesure où vous bénéficiez d’une intégration plus pratique de la part de l’équipe produit Event Hubs pour obtenir le déploiement flexible qui vous convient. 

## <a name="next-steps"></a>Étapes suivantes

Contactez votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité des Event Hubs Dedicated. Vous pouvez également en apprendre plus sur les niveaux tarifaires Event Hubs en consultant les liens suivants :

- [Tarification d’Event Hubs Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Vous pouvez également contacter votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité Event Hubs Dedicated.
- L’article [FAQ sur les hubs d’événements](event-hubs-faq.md) traite des informations de tarification et répond à certaines questions fréquemment posées sur Event Hubs.
