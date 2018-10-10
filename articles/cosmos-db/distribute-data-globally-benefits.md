---
title: Principaux avantages de la distribution mondiale d’Azure Cosmos DB
description: Découvrez les principaux avantages de la fonction multimaître dans Azure Cosmos DB que procure la géo-réplication, ainsi que des utilisations concrètes.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968342"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Distribuer des données mondialement avec Azure Cosmos DB

Cet article décrit les principaux avantages de la distribution mondiale de données et certains scénarios en temps réel où celle-ci est nécessaire.

## <a name="key-benefits"></a>Principaux avantages

Voici les principaux avantages disponibles pour les comptes qui tirent parti des fonctionnalités de distribution mondiale des données d’Azure Cosmos DB :

* **Latence à un chiffre** : Azure Cosmos DB offre une latence en lecture et écriture inférieure à 10 ms au 99e centile, garantie par des [Contrats de niveau de service soutenus financièrement](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Disponibilité « cinq neuf »**  : Azure Cosmos DB offre une disponibilité en lecture et écriture de 99,999 %, garantie par des [Contrats de niveau de service soutenus financièrement](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Résolution des conflits flexible** : pour les clients qui tirent parti des fonctionnalités multimaître, Azure Cosmos DB fournit trois modes de gestion des conflits pour garantir l’intégrité et la cohérence des données au niveau mondial.

* **Cohérence ajustable** : Azure Cosmos DB prend en charge 5 [niveaux de cohérence](consistency-levels.md) différents tournés vers la distribution mondiale pour les comptes dotés de la fonction multimaître (seul le niveau de cohérence forte n’est pas pris en charge).

* **Tolérance de panne implicite**  : avec la réplication des données dans plusieurs régions, les applications peuvent bénéficier d’une tolérance de panne élevée contre les pannes régionales.

## <a name="use-cases"></a>Cas d'utilisation

Voici un tout petit échantillon des scénarios qui peuvent tirer parti des fonctionnalités de distribution mondiale d’Azure Cosmos DB.

* **Applications de réseaux sociaux** : les applications de réseaux sociaux nécessitent une latence faible, une haute disponibilité et la scalabilité pour offrir une expérience exceptionnelle aux utilisateurs du monde entier.

* **IoT**  : les déploiements de périphérie géodistribués doivent souvent effectuer le suivi de données de série chronologique à partir de plusieurs emplacements. Chaque appareil peut être hébergé dans sa région la plus proche. Quand des appareils changent d’emplacement, ils peuvent être rapatriés pour écrire dans la région disponible la plus proche.

* **E-commerce**  : l’e-commerce nécessite une très faible latence et une haute disponibilité. La géo-distribution des données avec lectures et écritures rapproche au maximum les données des utilisateurs, améliorant ainsi la réactivité des applications. La disponibilité pour les lectures et écritures dans plusieurs régions fournit une plus grande disponibilité.

* **Détection des fraudes/anomalies**  : souvent, les applications qui supervisent l’activité de l’utilisateur ou du compte sont distribuées à l’échelle mondiale et doivent effectuer le suivi de plusieurs événements simultanément pour mettre à jour les scores afin de maîtriser les paramètres de risque.

* **Contrôle**  : le comptage et la régulation de l’utilisation (tels que les appels d’API, le nombre de transactions/seconde et les minutes utilisées) peuvent être implémentés à l’échelle mondiale en toute simplicité à l’aide de l’architecture multimaître Azure Cosmos DB. La résolution de conflit intégrée garantit à la fois la précision des chiffres et la régulation en temps réel.

## <a name="next-steps"></a>Étapes suivantes  

Dans cet article, vous avez découvert les principaux avantages et des cas d’usage des fonctionnalités de distribution mondiale des données d’Azure Cosmos DB. Vous pouvez maintenant consulter les ressources suivantes :

* [Comment Azure Cosmos DB permet une distribution mondiale clé en main](distribute-data-globally-turnkey.md)

* [Guide pratique pour configurer la réplication de base de données mondiale dans Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Guide pratique pour activer le multimaître pour les comptes Azure Cosmos DB](enable-multi-master.md)

* [Fonctionnement du basculement manuel et automatique dans Azure Cosmos DB](regional-failover.md)

* [Présentation de la résolution des conflits dans Azure Cosmos DB](multi-master-conflict-resolution.md)

* [Utilisation du multimaître avec des bases de données NoSQL open source](multi-master-oss-nosql.md)
