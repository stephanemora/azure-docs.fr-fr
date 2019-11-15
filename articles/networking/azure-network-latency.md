---
title: Statistiques de latence aller-retour réseau Azure | Microsoft Docs
description: En savoir plus sur les statistiques de latence aller-retour entre les régions Azure.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586870"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiques de latence aller-retour réseau Azure

Azure surveille en permanence la latence (vitesse) des principaux domaines de son réseau à l’aide d’outils de surveillance internes, ainsi que de mesures collectées par [ThousandEyes](https://thousandeyes.com), un service de surveillance synthétique tiers.

## <a name="how-are-the-measurements-collected"></a>Comment les mesures sont-elles collectées ?

Les mesures de latence sont collectées à partir des agents ThousandEyes hébergés dans les régions du cloud Azure, qui envoient continuellement des sondes réseau entre elles, par intervalles de 1 minute. Les statistiques de latence mensuelles sont dérivées de la moyenne des échantillons collectés pour le mois.

## <a name="october-2019-latency-figures"></a>Chiffres de latence d’octobre 2019

Pour les 31 jours qui se terminent le 31 octobre 2019, les temps de latence aller-retour mensuels minimum et maximum dans les régions agrégées sont les suivants :

- **5 ms** à **72 ms** pour les aller-retours au sein des régions d’**Amérique du Nord**.
- **3 ms** à **28 ms** pour les aller-retours au sein des régions d’**Europe**.
- **4 ms** à **134 ms** pour les aller-retours au sein des régions d’**Asie**.

Les mesures de latence inter-régionales suivantes sont optimisées par [ThousandEyes](https://thousandeyes.com). L’unité de mesure dans le tableau ci-dessous est exprimée en millisecondes (ms).

![Statistiques de latence inter-régionales Azure](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).