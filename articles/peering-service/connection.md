---
title: Connexion Azure Peering Service
description: Découvrez la connexion Microsoft Azure Peering Service
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: 9463a0967b9cfc25f189dd3efac80e26428866fa
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202656"
---
# <a name="peering-service-connection"></a>Connexion Peering Service

Une connexion fait généralement référence à un ensemble d’informations logiques identifiant un Peering Service. Vous la définissez en spécifiant les attributs suivants :

- Nom logique
- Partenaire de connectivité
- Emplacement du service principal du partenaire de connectivité
- Emplacement du service de sauvegarde du partenaire de connectivité
- Emplacement physique du client
- Préfixes d’adresses IP

Le client peut établir une ou plusieurs connexions en fonction des besoins. Une connexion est également utilisée en tant qu’unité de collecte de télémétrie. Par exemple, pour choisir des alertes de télémétrie, le client doit définir la connexion à superviser.

> [!Note]
> Lorsque vous vous inscrivez à Peering Service, nous analysons votre télémétrie Windows et Microsoft 365 afin de vous fournir des mesures de latence pour vos préfixes sélectionnés. Actuellement, la télémétrie est prise en charge uniquement pour les préfixes de taille /24 ou supérieure.
>Pour plus d’informations sur la télémétrie des connexions, reportez-vous à [Télémétrie des connexions Peering Service](connection-telemetry.md).
>

## <a name="how-to-create-a-peering-service-connection"></a>Comment créer une connexion Peering Service ?

**Scénario** : supposons qu’une filiale est éparpillée dans différents emplacements géographiques, comme illustré dans la figure ci-dessous. Ici, le client doit fournir un nom logique, le nom du fournisseur de services, l’emplacement physique du client et des préfixes IP (appartenant au client ou alloués par le fournisseur de services) associés à une connexion unique.  Les emplacements de service principal et de sauvegarde avec un partenaire aident à définir l’emplacement de service préféré du client. Ce processus doit être répété pour créer une connexion Peering Service pour d’autres emplacements.

![Connexions géo-redondantes](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> La filtration de niveau d’état est prise en compte pour l’emplacement physique du client lorsque la connexion est géo-localisée aux États-Unis.
>

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir le processus pas à pas d’inscription d’une connexion Peering Service, consultez [Créer une connexion Peering Service à l’aide du portail Azure](azure-portal.md).

Pour en savoir plus sur la télémétrie des connexions Peering Service, consultez [Télémétrie des connexions Peering Service](connection-telemetry.md).

Pour accéder à la télémétrie, consultez [Accéder à la télémétrie des connexions](measure-connection-telemetry.md).
