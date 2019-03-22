---
title: Méthodes hôte de l’équilibrage de charge du pool (version préliminaire) - Azure
description: Pool équilibrage de charge méthodes hôtes pour un environnement de bureau virtuel Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0a07406c8bad4ad0bef2949103d1f2c78e7dd8af
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318508"
---
# <a name="host-pool-load-balancing-methods"></a>Héberger des méthodes d’équilibrage de charge de pool

Bureau virtuel Windows (version préliminaire) prend en charge deux méthodes d’équilibrage de charge. Chaque méthode détermine quel hôte de session hébergera une session utilisateur lorsqu’ils se connectent à une ressource dans un pool de l’hôte.

Les méthodes d’équilibrage de charge suivantes sont disponibles dans un bureau virtuel Windows :

- L’équilibrage de charge de prioritaire vous permet distribuer uniformément les sessions utilisateur dans les hôtes de session dans un pool de l’hôte.
- L’équilibrage de charge de la profondeur en premier permet de saturer un hôte de session avec les sessions utilisateur dans un pool de l’hôte. Une fois que la première session atteint son seuil de limite de session, l’équilibreur de charge dirige les nouvelles connexions utilisateur à l’hôte de session suivante dans le pool de l’hôte jusqu'à ce qu’il atteigne sa limite et ainsi de suite.

Chaque pool de l’hôte ne pouvez configurer qu’un seul type d’équilibrage de charge spécifique. Toutefois, l’équilibrage de charge méthodes ont les comportements suivants, peu importe les hébergent pool elles se trouvent dans :

- Si un utilisateur dispose d’une session dans le pool de l’hôte est déjà et se reconnecte à cette session, l’équilibreur de charge avec succès redirigera les à l’hôte de session et leur session existante. Ce comportement s’applique même si la propriété AllowNewConnections de l’hôte de session est définie sur False.
- Si un utilisateur ne dispose déjà d’une session dans le pool de l’hôte, l’équilibreur de charge ne sont pas envisager des hôtes de session dont la propriété AllowNewConnections est définie sur False lors de l’équilibrage de charge.

## <a name="breadth-first-load-balancing-method"></a>Méthode d’équilibrage de charge prioritaire

La méthode d’équilibrage de charge prioritaire vous permet de distribuer les connexions utilisateur afin d’optimiser ce scénario. Cette méthode est idéale pour les organisations qui souhaitent offrir la meilleure expérience pour les utilisateurs qui se connectent à leur environnement de bureau virtuel mis en pool.

La méthode prioritaire interroge d’abord les hôtes de session qui autorise les nouvelles connexions. La méthode sélectionne ensuite l’hôte de session avec le plus petit nombre de sessions. S’il existe un lien, la méthode sélectionne le premier hôte de session dans la requête.

## <a name="depth-first-load-balancing-method"></a>Méthode d’équilibrage de charge de profondeur

La méthode d’équilibrage de charge de profondeur en premier vous permet de saturer un hôte de session à la fois pour optimiser pour ce scénario. Cette méthode est idéale pour les organisations abordables désireuses d’un contrôle plus précis sur le nombre de machines virtuelles qu’ils ont allouée pour un pool de l’hôte.

La méthode de profondeur interroge d’abord les hôtes de session qui autorise de nouvelles connexions et n’ont pas effectué au-delà de cette limite maximale de la session. La méthode sélectionne ensuite l’hôte de session avec un plus grand nombre de sessions. S’il existe un lien, la méthode sélectionne le premier hôte de session dans la requête.