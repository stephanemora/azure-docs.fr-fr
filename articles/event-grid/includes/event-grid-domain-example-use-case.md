---
title: Fichier Include
description: inclure fichier
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413523"
---
Il est plus simple de partir d’un exemple pour expliquer les domaines d’événements. Supposez que votre société, Contoso Construction Machinery, produit des tracteurs, du matériel d’excavation et autres machineries lourdes. Dans le cadre de la gestion de votre entreprise, vous envoyez des informations en temps réel à vos clients sur la maintenance des machines, l’état des systèmes et les mises à jour de vos contrats. Tout ceci est envoyé vers différents points de terminaison, dont votre application, des points de terminaison côté client ou toute autre infrastructure que des clients ont mise en place.

Les domaines d’événements vous permettent de modéliser Contoso Construction Machinery en une unique entité source d’événements. Chacun de vos clients est représenté en tant que rubrique au sein du domaine. L’authentification et l’autorisation sont gérées à l’aide d’Azure Active Directory. Chacun de vos clients peut s’abonner à sa rubrique et recevoir ses événements. L’accès géré via le domaine d’événements garantit qu’ils n’ont accès qu’à leur rubrique.

Il vous donne également un point de terminaison unique, dans lequel vous pouvez publier l’ensemble des événements du client. Event Grid veille à ce que chaque rubrique prenne uniquement en compte les événements qui concernent son abonné.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Exemple Contoso Construction" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::