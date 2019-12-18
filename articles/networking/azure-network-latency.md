---
title: Statistiques de latence aller-retour réseau Azure | Microsoft Docs
description: En savoir plus sur les statistiques de latence aller-retour entre les régions Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893081"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiques de latence aller-retour réseau Azure

Azure surveille en permanence la latence (vitesse) des principaux domaines de son réseau à l’aide d’outils de surveillance internes, ainsi que de mesures collectées par [ThousandEyes](https://thousandeyes.com), un service de surveillance synthétique tiers.

## <a name="how-are-the-measurements-collected"></a>Comment les mesures sont-elles collectées ?

Les mesures de latence sont collectées à partir des agents ThousandEyes hébergés dans les régions du monde entier du cloud Azure, qui envoient continuellement des probes réseau entre elles, par intervalles de 1 minute. Les statistiques de latence mensuelles sont dérivées de la moyenne des échantillons collectés pour le mois.

## <a name="november-2019-latency-figures"></a>Chiffres de latence de novembre 2019

**Mise à jour de novembre :** 3 régions ajoutées.

* Norvège Est
* Norvège Ouest
* Australie

Les durées de boucles moyennes et mensuelles entre les régions Azure pour les 30 derniers jours (qui se terminent le 30 novembre 2019) sont indiquées ci-dessous. Les mesures suivantes sont optimisées par [ThousandEyes](https://thousandeyes.com).

![Statistiques de latence inter-régionales Azure](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).
