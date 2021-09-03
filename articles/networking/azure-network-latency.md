---
title: Statistiques de latence aller-retour réseau Azure | Microsoft Docs
description: En savoir plus sur les statistiques de latence aller-retour entre les régions Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 06/08/2021
ms.author: kumud
ms.openlocfilehash: e86f0e38c71265761e0faddbbf1cc783557cd5df
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112297757"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiques de latence aller-retour réseau Azure

Azure surveille en permanence la latence (vitesse) des principaux domaines de son réseau à l’aide d’outils de surveillance internes, ainsi que de mesures collectées par [ThousandEyes](https://thousandeyes.com), un service de surveillance synthétique tiers.

## <a name="how-are-the-measurements-collected"></a>Comment les mesures sont-elles collectées ?

Les mesures de latence sont collectées à partir des agents ThousandEyes hébergés dans les régions du monde entier du cloud Azure, qui envoient continuellement des probes réseau entre elles, par intervalles de 1 minute. Les statistiques de latence mensuelles sont dérivées de la moyenne des échantillons collectés pour le mois.

## <a name="may-2021-round-trip-latency-figures"></a>Chiffres de la latence aller-retour pour mai 2021

Les durées moyennes et mensuelles d’aller-retour entre les régions Azure pour les 31 derniers jours (qui se terminent le 31 mai 2021) sont indiquées ci-dessous. Les mesures suivantes sont optimisées par [ThousandEyes](https://thousandeyes.com).

[![Statistiques de latence interrégionales Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).
