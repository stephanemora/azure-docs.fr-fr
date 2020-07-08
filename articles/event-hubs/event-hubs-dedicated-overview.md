---
title: Vue d’ensemble d’Event Hubs Dedicated - Azure Event Hubs | Microsoft Docs
description: Cet article fournit une vue d’ensemble d’Azure Event Hubs Dedicated, qui offre des déploiements de concentrateurs d’événements à locataire unique.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 70061b5dc4fe72c9fd2fd60dd8c67da31b1d1e6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322443"
---
# <a name="overview-of-event-hubs-dedicated"></a>Vue d’ensemble d’Event Hubs Dedicated

Les *clusters Event Hubs* offrent des déploiements à locataire unique pour les clients aux besoins de streaming les plus pointus. Cette offre à locataire unique a un SLA de 99,99 % garanti et n'est disponible sur que notre niveau de tarification dédié. Un cluster Event Hubs peut recevoir des millions d’événements par seconde avec une garantie de capacité et de latence inférieure à la seconde. Les espaces de noms et les Event Hubs créées au sein du cluster dédié comprennent toutes les fonctionnalités de l’offre standard et bien davantage, mais sans aucune limite d’entrée. Elle comprend également la fameuse fonctionnalité [Event Hubs Capture](event-hubs-capture-overview.md) sans supplément, ce qui permet de charger automatiquement par lots et de consigner des flux de données dans Stockage Azure ou Azure Data Lake. 

Les clusters sont mis en service et facturés par **unités de capacité (UC)** , une quantité pré-allouée d'UC et de ressources mémoire. Vous pouvez acheter 1, 2, 4, 8, 12, 16 ou 20 unités de capacité pour chaque cluster. Le volume que vous pouvez ingérer et diffuser par CU dépend d'une variété de facteurs, tels que le nombre de producteurs et de consommateurs, la forme de l’entité, le débit en sortie (voir les résultats de référence ci-dessous pour plus de détails). 

> [!NOTE]
> Par défaut, tous les clusters Event Hubs sont compatibles avec Kafka et prennent en charge les points de terminaison Kafka qui peuvent être utilisés par vos applications Kafka existantes. L’activation de Kafka sur votre cluster n'affecte pas vos cas d'utilisation non-Kafka ; il n'y a aucune option ou besoin de désactiver Kafka sur un cluster.

## <a name="why-dedicated"></a>Pourquoi utiliser des clusters dédiés ?

Event Hubs Dedicated offre trois avantages convaincants pour les clients qui ont besoin d’une capacité de niveau entreprise :

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>L’architecture monolocation garantit la capacité pour de meilleures performances

Un cluster dédié garantit une capacité à pleine échelle et peut gérer jusqu'à plusieurs gigaoctets de données en continu avec un stockage durable et une latence inférieure à la seconde pour s'adapter à un pic de trafic. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accès inclusif et exclusif aux fonctionnalités 
L’offre dédiée comprend des fonctionnalités gratuites telles que Capture et fournit un accès exclusif à de futures fonctionnalités comme BYOK (Bring Your Own Key). Le service gère également l'équilibrage de charge, les mises à jour du système d'exploitation, les correctifs de sécurité et le partitionnement pour le client, afin que vous puissiez consacrer moins de temps à la maintenance de l'infrastructure et plus de temps à la création de fonctionnalités côté client.  

#### <a name="cost-savings"></a>Réduction des coûts
À des volumes d'entrée élevés (>100 unités de débit), un cluster coûte nettement moins cher par heure que l'achat d'une quantité comparable d'unités de débit avec l'offre Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quotas et limites de l’offre Event Hubs Dedicated

L’offre Event Hubs Dedicated est facturée à un tarif mensuel fixe, avec un minimum de 4 heures d’utilisation. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise et les limites pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | standard | Dédié |
| --- |:---:|:---:|
| Bande passante | 20 unités de débit (jusqu'à 40 unités de débit) | 20 CUS |
| Espaces de noms |  1 | 50 par unité de capacité |
| Event Hubs |  10 par espace de noms | 1 000 par espace de noms |
| Événements d’entrée | Paiement par million d’événements | Inclus |
| Taille des messages | 1 million d’octets | 1 million d’octets |
| Partitions | 32 par hub d’événements | 1 024 par hub d’événements |
| Groupes de consommateurs | 20 par hub d’événements | Aucune limite par unité de capacité, 1 000 par hub d’événements |
| Connexions réparties | 1 000 inclus, 5 000 maximum | 100 000 inclus et maximum |
| Rétention des messages | 7 jours, 84 Go inclus par unité de débit | 90 jours, 10 To inclus per unité de capacité |
| Capture | Paiement par heure | Inclus |

## <a name="how-to-onboard"></a>Intégration : mode d’emploi

L’expérience en libre-service permettant de [créer un cluster Event Hubs](event-hubs-dedicated-cluster-create-portal.md) via le [Portail Azure](https://aka.ms/eventhubsclusterquickstart) est désormais en préversion. Si vous avez des questions ou si vous avez besoin d'aide pour l'intégration à Event Hubs Dedicated, veuillez contacter l'[équipe Event Hubs](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>FAQ

#### <a name="what-can-i-achieve-with-a-cluster"></a>Que puis-je faire avec un cluster ?

Pour un cluster Event Hubs, la quantité que vous pouvez ingérer et diffuser dépend de divers facteurs tels que vos producteurs, les consommateurs, la vitesse à laquelle vous ingérez et transformez, et bien plus encore. 

Le tableau suivant présente les résultats de référence que nous avons obtenus au cours de nos tests :

| Forme de la charge utile | Récepteurs | Bande passante en entrée| Messages en entrée | Bande passante en sortie | Messages en sortie | Nombre total d’unités de débit | Unités de débit par unité de capacité |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lots de 100x1 Ko | 2 | 400 Mo/s | 400 000 messages par seconde | 800 Mo/s | 800 000 messages par seconde | 400 unités de débit | 100 unités de débit | 
| Lots de 10x10 Ko | 2 | 666 Mo/s | 66 600 messages par seconde | 1,33 Go/s | 133 000 messages par seconde | 666 unités de débit | 166 unités de débit |
| Lots de 6x32 Ko | 1 | 1,05 Go/s | 34 000 messages par seconde | 1,05 Go/s | 34 000 messages par seconde | 1 000 unités de débit | 250 unités de débit |

Lors des tests, les critères suivants ont été utilisés :

- Un cluster Event Hubs dédié avec quatre unités de capacité (UC) a été utilisé. 
- Le hub d’événements utilisé pour l’ingestion possédait 200 partitions. 
- Les données ingérées ont été reçues par deux applications réceptrices provenant de toutes les partitions.

#### <a name="can-i-scale-updown-my-cluster"></a>Puis-je augmenter/réduire (scale-up/scale-down) la taille de mon cluster ?

Une fois créés, vos clusters sont facturés pour un minimum de 4 heures d’utilisation. Dans la préversion de l'expérience en libre-service, vous pouvez soumettre une [demande de support](https://ms.portal.azure.com/#create/Microsoft.Support) à l'équipe Event Hubs sous *Technical > Quota > Request to Scale Up or Scale Down Dedicated Cluster* pour augmenter ou réduire la taille de votre cluster. La demande de réduction de la taille de votre cluster peut prendre jusqu’à 7 jours. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Comment la géo-reprise fonctionnera-t-elle avec mon cluster ?

Vous pouvez former une géopaire en combinant un espace de noms sous un cluster dédié avec un autre espace de noms sous un cluster dédié. Nous déconseillons l’association d'un espace de noms dédié avec un espace de noms dans notre offre Standard, car la limite de débit sera incompatible, ce qui entraînerait des erreurs. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Puis-je migrer mes espaces de noms Standard vers un cluster dédié ?
Nous n’offrons actuellement aucun processus de migration automatisé pour la migration des données de vos hubs d'événements d'un espace de noms Standard vers un espace dédié. 

## <a name="next-steps"></a>Étapes suivantes

Contactez votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur Event Hubs Dedicated. Vous pouvez également créer un cluster ou en apprendre plus sur les niveaux tarifaires Event Hubs en consultant les liens suivants :

- [Créer un cluster Event Hubs via le portail Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Tarification d’Event Hubs Dedicated](https://azure.microsoft.com/pricing/details/event-hubs/). Vous pouvez également contacter votre représentant commercial Microsoft ou le support technique Microsoft pour obtenir des informations supplémentaires sur la capacité Event Hubs Dedicated.
- L’article [FAQ sur les hubs d’événements](event-hubs-faq.md) traite des informations de tarification et répond à certaines questions fréquemment posées sur Event Hubs.
