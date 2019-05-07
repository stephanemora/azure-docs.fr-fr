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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138658"
---
# <a name="overview-of-event-hubs-dedicated"></a>Vue d’ensemble d’Event Hubs Dedicated

Clusters Event Hubs Azure offrent des déploiements à locataire unique pour les clients avec les plus exigeantes en matière de diffusion en continu. Cette offre de locataire unique a un contrat SLA de 99,99 % garanti. Il est disponible uniquement sur le niveau tarifaire de dédié.

Un cluster de concentrateurs d’événements peuvent être entrés des millions d’événements par seconde avec la garantie de la capacité et la latence pour les sous-secondes. Espaces de noms et à event hubs créées au sein du cluster dédié incluent toutes les fonctionnalités de l’offre Standard et bien plus encore, mais sans aucune limitation de l’entrée. Il inclut également le [Event Hubs Capture](event-hubs-capture-overview.md) fonctionnalité sans coût supplémentaire. Vous pouvez l’utiliser pour automatiquement batch et le journal des flux de données pour le stockage Azure ou Azure Data Lake.

Des clusters dédiés sont mis en service et facturées en unités de capacité (CUs). Unités de capacité sont une quantité préallouée de ressources processeur et mémoire. Vous pouvez acheter 1, 2, 4, 8, 12, 16 ou 20 unités de capacité pour chaque cluster. La quantité que vous pouvez recevoir et flux par unité de capacité dépend de facteurs tels que le nombre de producteurs et consommateurs, la forme de la charge utile et le taux de sortie.

Pour plus d’informations, consultez le tableau avec les résultats de test d’évaluation.

## <a name="why-use-event-hubs-dedicated"></a>Pourquoi utiliser Event Hubs Dedicated ?

Event Hubs Dedicated offre trois avantages pour les clients qui ont besoin de capacité au niveau de l’entreprise.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Architecture mutualisée unique garantit la capacité pour de meilleures performances

Un cluster dédié garantit la capacité à grande échelle complète. Peuvent être entrés jusqu'à gigaoctets de données de diffusion en continu avec une latence de stockage et pour les sous-secondes durable pour faire face aux pics de trafic.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accès aux fonctionnalités inclusif et exclusif 
L’offre dédié inclut des fonctionnalités telles que la Capture sans coût supplémentaire. Il offre également un accès exclusif à des fonctionnalités à venir comme BYOK. Le service gère également l’équilibrage de charge, du système d’exploitation mises à jour, correctifs de sécurité et de partitionnement. Avec ces fonctionnalités, vous pouvez consacrer moins de temps sur la maintenance de l’infrastructure et de plus de temps sur la création de fonctionnalités côté client.

#### <a name="cost-savings"></a>Réduction des coûts
Les volumes en entrée élevé, un cluster coûts beaucoup moins heure qu’if vous achetez une quantité comparable d’unités de débit (tu) dans l’offre Standard. Un volume élevé est > 100 aux unités de débit.


## <a name="event-hubs-standard-vs-dedicated"></a>Vs Standard des concentrateurs d’événements. Dédié

Le tableau suivant compare les niveaux de service Event Hubs disponibles. L’offre Event Hubs Dedicated est facturé à un tarif mensuel fixe par rapport au prix de l’utilisation pour la plupart des fonctionnalités de la norme. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité d’échelle de l’entreprise pour les clients avec des charges de travail exigeantes.

| Fonctionnalité | standard | Dédié |
| --- |:---:|:---:|
| Événements d’entrée | Paiement par million d’événements | Inclus |
| Unité de débit (1 Mo/s en entrée, 2 Mo/s en sortie) | Paiement par heure | Inclus |
| Taille des messages | 1 Mo | 1 Mo |
| Partitions | 40 par espace de noms | 2 000 par unité de capacité |
| Groupes de consommateurs | 20 par event hub | 1 000 par event hub |
| Bande passante maximale | 20 aux unités de débit (jusqu'à 40 aux unités de débit) | 20 unités de capacité |
| Connexions réparties | 1 000 inclus | 100 000 inclus |
| Rétention des messages | Un jour inclus | Jusqu'à sept jours inclus |
| Capture | Paiement par heure | Inclus |

## <a name="what-can-i-achieve-with-a-cluster"></a>Ce que puis-je faire avec un cluster ?

Pour un cluster Event Hubs, combien vous pouvez ingérer et diffuser en continu dépend de votre producteurs, vos consommateurs, la vitesse à laquelle vous ingérer et traiter et bien plus encore.

Le tableau suivant montre les résultats de test d’évaluation qui ont été obtenus lors du test.

| Forme de la charge utile | Récepteurs | Bande passante en entrée| Messages en entrée | Bande passante en sortie | Messages en sortie | Nombre total d’unités de débit | Unités de débit par unité de capacité |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lots de 100x1 Ko | 2 | 400 Mo/s | 400 000 messages par seconde | 800 Mo/s | 800 000 messages par seconde | 400 unités de débit | 100 unités de débit | 
| Lots de 10x10 Ko | 2 | 666 Mo/s | 66,600 messages/s | 1,33 Go/s | 133,000 messages/s | 666 unités de débit | 166 unités de débit |
| Lots de 6x32 Ko | 1 | 1,05 Go/s | 34 000 messages par seconde | 1,05 Go/s | 34 000 messages par seconde | Débit de 1 000 | 250 unités de débit |

Dans le test, les critères suivants ont été utilisés :

- Un cluster dédié couche Event Hubs avec quatre unités de capacité a été utilisé.
- Le hub d’événements utilisé pour l’ingestion possédait 200 partitions.
- Les données qui a été ingérées a été reçues par les deux applications de destinataire. Il a reçu des données à partir de toutes les partitions.

## <a name="use-event-hubs-dedicated"></a>Utiliser Event Hubs Dedicated

Pour utiliser Event Hubs Dedicated, [contactez le support de facturation](https://ms.portal.azure.com/#create/Microsoft.Support) ou votre représentant Microsoft. Vous pouvez faire évoluer votre capacité à la hausse ou à la baisse au cours du mois pour répondre à vos besoins en ajoutant ou en supprimant des unités de capacité. L’équipe de produit Event Hubs vous permet d’obtenir le déploiement flexible qui vous convient.

## <a name="next-steps"></a>Étapes suivantes

Contactez votre représentant commercial Microsoft ou le Support Microsoft pour obtenir des informations supplémentaires sur la capacité Event Hubs Dedicated. Pour en savoir plus sur les niveaux tarifaires Event Hubs, utilisez les liens suivants :

- [Tarification d’Event Hubs Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Vous pouvez également contacter votre représentant commercial Microsoft ou le Support Microsoft pour obtenir des informations supplémentaires sur la capacité Event Hubs Dedicated.
- L’article [FAQ sur les hubs d’événements](event-hubs-faq.md) traite des informations de tarification et répond à certaines questions fréquemment posées sur Event Hubs.
