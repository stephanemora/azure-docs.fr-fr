---
title: Équilibrage de charge du pool d’hôtes Windows Virtual Desktop – Azure
description: Méthodes d’équilibrage de charge de pool d’hôtes pour un environnement Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 15d50033a1316601dd8c36bd5748c659f4397d66
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612042"
---
# <a name="host-pool-load-balancing-methods"></a>Méthodes d’équilibrage de charge des pools d’hôtes

>[!IMPORTANT]
>Ce contenu s’applique à la mise à jour Printemps 2020 avec des objets Azure Resource Manager Windows Virtual Desktop. Si vous utilisez la version Automne 2019 de Windows Virtual Desktop sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).
>
> La mise à jour Printemps 2020 de Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service et nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. 
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop prend en charge deux méthodes d’équilibrage de charge. Chaque méthode détermine quel hôte de session hébergera une session utilisateur lors de la connexion à une ressource d’un pool d’hôtes.

Les méthodes d’équilibrage de charge suivantes sont disponibles dans Windows Virtual Desktop :

- L’équilibrage de charge de largeur permet de distribuer uniformément les sessions utilisateur sur les hôtes de session d’un pool d’hôtes.
- L’équilibrage de charge de profondeur permet de saturer un hôte de session avec les sessions utilisateur d’un pool d’hôtes. Dès que la première session a atteint son seuil limite de sessions, l’équilibreur de charge dirige les nouvelles connexions utilisateur vers l’hôte de session suivant du pool d’hôtes jusqu’à ce qu’il atteigne sa limite et ainsi de suite.

Chaque pool d’hôtes ne peut configurer qu’un seul type d’équilibrage de charge qui lui est propre. Toutefois, les deux méthodes d’équilibrage de charge partagent les comportements suivants quel que soit le pool d’hôtes dans lequel elles se trouvent :

- Si un utilisateur dispose déjà d’une session dans le pool d’hôtes et se reconnecte à cette session, l’équilibreur de charge le redirige avec succès vers l’hôte de session avec sa session existante. Ce comportement s’applique même si la propriété AllowNewConnections de l’hôte de session est définie sur False.
- Si un utilisateur ne dispose pas encore de session dans le pool d’hôtes, l’équilibreur de charge ne tient pas compte des hôtes de session dont la propriété AllowNewConnections est définie sur False lors de l’équilibrage de charge.

## <a name="breadth-first-load-balancing-method"></a>Méthode d’équilibrage de charge de largeur

La méthode d’équilibrage de charge de largeur permet de distribuer les connexions utilisateur afin d’optimiser ce scénario. Cette méthode est idéale pour les organisations qui souhaitent offrir la meilleure expérience qui soit aux utilisateurs se connectant à leur environnement de bureau virtuel mis en pool.

La méthode de largeur interroge d’abord les hôtes de session qui autorise les nouvelles connexions. La méthode sélectionne ensuite l’hôte de session ayant le plus petit nombre de sessions. S’il existe un lien, la méthode sélectionne le premier hôte de session de la requête.

## <a name="depth-first-load-balancing-method"></a>Méthode d’équilibrage de charge de profondeur

La méthode d’équilibrage de charge de profondeur permet de saturer un hôte de session à la fois en vue d’une optimisation pour ce scénario. Cette méthode est idéale pour les organisations attentives aux coûts désireuses d’un contrôle plus précis du nombre de machines virtuelles qu’elles ont allouées pour un pool d’hôtes.

La méthode de profondeur commence par interroger les hôtes de session qui autorisent de nouvelles connexions et n’ont pas dépassé leur limite maximale de nombre de sessions. La méthode sélectionne ensuite l’hôte de session ayant le plus grand nombre de sessions. S’il existe un lien, la méthode sélectionne le premier hôte de session de la requête.