---
title: Niveaux de performance du service de stockage d'objets blob de blocs Azure - Stockage Azure
description: Niveaux de performance du service Stockage Blob Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ac483a338b7d71142b89b13e41fc048346ac037f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803918"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Niveaux de performance du service de stockage d'objets blob de blocs Azure

À mesure que les entreprises déploient des applications cloud natives sensibles aux performances, il est impératif de disposer d'options de stockage de données économiques offrant différents niveaux de performance.

Le service de stockage d'objets blob de blocs Azure offre deux niveaux de performance différents :

- Premium : optimisé pour des taux de transaction élevés et une latence de stockage constante à un chiffre
- Standard : optimisé pour une capacité et un débit élevés

Les considérations suivantes s'appliquent aux différents niveaux de performance :

- Le niveau de performance Standard est disponible dans toutes les [régions Azure](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Le niveau de performance Premium est uniquement disponible dans [certaines régions](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- Le niveau de performance Premium offre une tarification optimisée pour les applications aux taux de transaction élevés afin de [réduire le coût total de stockage](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) de ces charges de travail.
- Le niveau de performance Premium requiert l'utilisation de comptes de stockage d'objets blob de blocs, qui prennent en charge les objets blob de blocs et les objets blob d'ajouts.
- Le niveau de performance Standard est disponible avec les comptes de stockage v1 universel, v2 universel et blob.
- Les niveaux de performance Standard et Premium prennent tous les deux en charge les [objets blob de blocs à haut débit](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Les objets blob de blocs à haut débit sont disponibles pour le niveau de performance Premium à plus de 256 Kio. Les objets blob de blocs à haut débit sont disponibles pour le niveau de performance Standard à plus de 4 Mio (Put Block).
- Pour le moment, le niveau de performance Premium est uniquement disponible avec le stockage localement redondant (LRS).

## <a name="premium-performance"></a>Niveau de performance Premium

Le stockage d'objets blob de blocs du niveau de performance Premium permet d'accéder aux données à l'aide de matériel hautes performances. Les données sont stockées sur des disques SSD optimisés pour une latence faible. Les disques SSD offrent un débit plus élevé que les disques durs traditionnels.

Le stockage d’objets blob de blocs de performances Premium est adapté aux charges de travail qui nécessitent des temps de réponse rapides et cohérents. Il convient tout particulièrement aux charges de travail qui exécutent une multitude de petites transactions.

## <a name="standard-performance"></a>Niveau de performance Standard

Le niveau de performance Standard prend en charge différents [niveaux d'accès](storage-blob-storage-tiers.md) pour stocker les données de la manière la plus économique. Il est optimisé pour une capacité et un débit élevés sur les jeux de données volumineux.

## <a name="blob-lifecycle-management"></a>Gestion de cycle de vie des objets blob

La gestion de cycle de vie du service Stockage Blob propose une stratégie élaborée basée sur des règles :

- Premium : les données expirent à la fin de leur cycle de vie
- Standard : les données passent au meilleur niveau d'accès et expirent à la fin de leur cycle de vie

Pour plus d'informations, consultez [Gérer le cycle de vie du service Stockage Blob Azure](storage-lifecycle-management-concepts.md).

Les données stockées dans un compte de stockage d'objets blob de blocs Premium ne peuvent pas être déplacées entre les niveaux chaud, froid et archive. Toutefois, vous pouvez copier des objets blob d'un compte de stockage d'objets blob de blocs vers le niveau d'accès chaud d'un *autre* compte. Utilisez l'API [Put Block From URL](/rest/api/storageservices/put-block-from-url) ou [AzCopy v10](../common/storage-use-azcopy-v10.md) pour copier des données vers un autre compte. L'API **Put Block From URL** copie les données sur le serveur de manière synchrone. L'appel ne se termine qu'une fois toutes les données déplacées de l'emplacement du serveur d'origine vers l'emplacement de destination.

## <a name="next-steps"></a>Étapes suivantes

Évaluer les niveaux chaud, froid et archive dans les comptes de stockage d’objets blob GPv2

- [Vérifier la disponibilité de niveau chaud, froid et archive par région](https://azure.microsoft.com/regions/#services)
- [Gérer le cycle de vie du stockage Blob Azure](storage-lifecycle-management-concepts.md)
- [Découvrir comment réalimenter les données d’objets blob à partir du niveau Archive](storage-blob-rehydration.md)
- [Évaluer l’utilisation des comptes de stockage actuels en activant les métriques Azure Storage](../common/storage-enable-and-view-metrics.md)
- [Vérifier la tarification du niveau chaud, froid et archive dans les comptes de stockage d’objets blob et GPv2 par région](https://azure.microsoft.com/pricing/details/storage/)
- [Vérifier la tarification des transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/)
