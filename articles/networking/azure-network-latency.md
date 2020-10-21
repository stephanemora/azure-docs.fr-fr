---
title: Statistiques de latence aller-retour réseau Azure | Microsoft Docs
description: En savoir plus sur les statistiques de latence aller-retour entre les régions Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 72168a56bfb4e08c7f44c84c773d9f6599cfa607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848878"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiques de latence aller-retour réseau Azure

Azure surveille en permanence la latence (vitesse) des principaux domaines de son réseau à l’aide d’outils de surveillance internes, ainsi que de mesures collectées par [ThousandEyes](https://thousandeyes.com), un service de surveillance synthétique tiers.

## <a name="how-are-the-measurements-collected"></a>Comment les mesures sont-elles collectées ?

Les mesures de latence sont collectées à partir des agents ThousandEyes hébergés dans les régions du monde entier du cloud Azure, qui envoient continuellement des probes réseau entre elles, par intervalles de 1 minute. Les statistiques de latence mensuelles sont dérivées de la moyenne des échantillons collectés pour le mois.

## <a name="september-2020-round-trip-latency-figures"></a>Chiffres de la latence aller-retour pour septembre 2020

Les durées de boucles moyennes et mensuelles entre les régions Azure pour les 30 derniers jours (qui se terminent le 30 septembre 2020) sont indiquées ci-dessous. Les mesures suivantes sont optimisées par [ThousandEyes](https://thousandeyes.com).

[![Statistiques de latence interrégionales Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).
