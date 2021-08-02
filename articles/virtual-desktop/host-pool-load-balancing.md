---
title: Équilibrage de charge du pool d’hôtes Azure Virtual Desktop – Azure
description: Découvrez les méthodes d’équilibrage de charge de pool d’hôtes pour un environnement Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 54cbd03283814fd21a95dfe7578173f3481c4cd8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757604"
---
# <a name="host-pool-load-balancing-methods"></a>Méthodes d’équilibrage de charge des pools d’hôtes

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Azure Virtual Desktop prend en charge deux méthodes d’équilibrage de charge. Chaque méthode détermine quel hôte de session hébergera une session utilisateur lors de la connexion à une ressource d’un pool d’hôtes.

Les méthodes d’équilibrage de charge suivantes sont disponibles dans Azure Virtual Desktop :

- L’équilibrage de charge de largeur permet de distribuer uniformément les sessions utilisateur sur les hôtes de session d’un pool d’hôtes.
- L’équilibrage de charge de profondeur permet de saturer un hôte de session avec les sessions utilisateur d’un pool d’hôtes. Dès que la première session a atteint son seuil limite de sessions, l’équilibreur de charge dirige les nouvelles connexions utilisateur vers l’hôte de session suivant du pool d’hôtes jusqu’à ce qu’il atteigne sa limite et ainsi de suite.

Chaque pool d’hôtes ne peut configurer qu’un seul type d’équilibrage de charge qui lui est propre. Toutefois, les deux méthodes d’équilibrage de charge partagent les comportements suivants quel que soit le pool d’hôtes dans lequel elles se trouvent :

- Si un utilisateur dispose déjà d’une session dans le pool d’hôtes et se reconnecte à cette session, l’équilibreur de charge le redirige avec succès vers l’hôte de session avec sa session existante. Ce comportement s’applique même si la propriété AllowNewConnections de l’hôte de session est définie sur False.
- Si un utilisateur ne dispose pas encore de session dans le pool d’hôtes, l’équilibreur de charge ne tient pas compte des hôtes de session dont la propriété AllowNewConnections est définie sur False lors de l’équilibrage de charge.

## <a name="breadth-first-load-balancing-method"></a>Méthode d’équilibrage de charge de largeur

La méthode d’équilibrage de charge de largeur permet de distribuer les connexions utilisateur afin d’optimiser ce scénario. Cette méthode est idéale pour les organisations qui souhaitent offrir la meilleure expérience qui soit aux utilisateurs se connectant à leur environnement de bureau virtuel mis en pool.

La méthode de largeur interroge d’abord les hôtes de session qui autorise les nouvelles connexions. La méthode sélectionne ensuite un hôte de session de façon aléatoire parmi la moitié des hôtes de session avec le moins de sessions. Par exemple, s’il existe neuf machines avec 11, 12, 13, 14, 15, 16, 17, 18 et 19 sessions, lorsque vous créez une session, celle-ci n’est pas automatiquement affectée à la première machine. Au lieu de cela, elle peut être affectée à l’une quelconque des cinq premières machines avec le moins de sessions (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Méthode d’équilibrage de charge de profondeur

La méthode d’équilibrage de charge de profondeur permet de saturer un hôte de session à la fois en vue d’une optimisation pour ce scénario. Cette méthode est idéale pour les organisations attentives aux coûts désireuses d’un contrôle plus précis du nombre de machines virtuelles qu’elles ont allouées pour un pool d’hôtes.

La méthode de profondeur commence par interroger les hôtes de session qui autorisent de nouvelles connexions et n’ont pas dépassé leur limite maximale de nombre de sessions. La méthode sélectionne ensuite l’hôte de session ayant le plus grand nombre de sessions. S’il existe un lien, la méthode sélectionne le premier hôte de session de la requête.

>[!IMPORTANT]
>L’algorithme d’équilibrage de charge en profondeur d’abord distribue les sessions aux hôtes de session en fonction de la limite maximale de ces derniers. Ce paramètre est obligatoire avec l’algorithme d’équilibrage de charge en profondeur d’abord. Pour une expérience utilisateur optimale, veillez à définir le paramètre de limite maximale des hôtes de session sur le nombre le plus adapté à votre environnement.