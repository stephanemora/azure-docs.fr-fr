---
title: Haute disponibilité et récupération d’urgence pour Oracle sur BareMetal
description: En savoir plus sur la haute disponibilité et la récupération d’urgence pour Oracle sur l’infrastructure BareMetal Azure.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589738"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Haute disponibilité et récupération d’urgence pour Oracle sur BareMetal

Dans cet article, nous examinerons les principes de base de la haute disponibilité et de la récupération d’urgence. Nous vous expliquerons ensuite comment vous pouvez obtenir une haute disponibilité et une récupération d’urgence dans un environnement Oracle sur l’infrastructure BareMetal.

## <a name="high-availability-vs-disaster-recovery"></a>Haute disponibilité et récupération d’urgence

La haute disponibilité et la récupération d’urgence offrent une couverture, mais pour différents types de défaillances. Ils utilisent différentes fonctionnalités et options d’Oracle Database.

La haute disponibilité permet à un système de surmonter plusieurs défaillances sans affecter l’expérience utilisateur de l’application. Les caractéristiques courantes d’un système à haute disponibilité sont les suivantes :

- Matériel redondant ne présentant aucun point de défaillance.
- Récupération automatique après des défaillances non critiques, telles que des lecteurs de disque défaillants ou des câbles réseau défectueux.
- La possibilité d’effectuer des modifications matérielles et logicielles sans effet notable sur le fonctionnement.
- Atteint ou dépasse les objectifs pour les objectifs de temps de récupération (RTO) et les objectifs de point de récupération (RPO).

La fonctionnalité la plus courante d’Oracle utilisée pour la haute disponibilité est [Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92).

La récupération d’urgence vous protège contre les défaillances localisées non récupérables qui pourraient nuire à votre stratégie de haute disponibilité principale. Dans l’écosystème Oracle, elle est proposée via la réplication de base de données, également appelée [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités de haute disponibilité pour Oracle :

> [!div class="nextstepaction"]
> [Fonctionnalités de haute disponibilité pour l’infrastructure Oracle sur BareMetal](high-availability-features.md)
