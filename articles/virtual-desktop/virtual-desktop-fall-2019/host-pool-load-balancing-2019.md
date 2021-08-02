---
title: Équilibrage de charge du pool d’hôtes Azure Virtual Desktop (classique) – Azure
description: Méthodes d’équilibrage de charge du pool d’hôtes d’un environnement Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: abd52941efc06b79a21e490e4e94f39a6e1d34be
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745040"
---
# <a name="host-pool-load-balancing-methods-in-azure-virtual-desktop-classic"></a>Méthodes d’équilibrage de charge du pool d’hôtes dans Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../host-pool-load-balancing.md).

Azure Virtual Desktop prend en charge deux méthodes d’équilibrage de charge. Chaque méthode détermine quel hôte de session hébergera une session utilisateur lors de la connexion à une ressource d’un pool d’hôtes.

Les méthodes d’équilibrage de charge disponibles dans Azure Virtual Desktop sont les suivantes :

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