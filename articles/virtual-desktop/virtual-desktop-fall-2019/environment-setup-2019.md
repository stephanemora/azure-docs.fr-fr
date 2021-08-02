---
title: Environnement Azure Virtual Desktop (classique) – Azure
description: Éléments de base d’un environnement Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9062565f5c13d7b278b57068df79c1bb4de66424
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745058"
---
# <a name="azure-virtual-desktop-classic-environment"></a>Environnement Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../environment-setup.md).

Azure Virtual Desktop est un service qui offre aux utilisateurs un accès facile et sécurisé à leurs postes de travail virtualisés et à leurs applications distantes RemoteApps. Cette rubrique fournit des informations supplémentaires sur la structure générale de l’environnement Azure Virtual Desktop.

## <a name="tenants"></a>Locataires

Le locataire Azure Virtual Desktop est l’interface principale pour gérer votre environnement Azure Virtual Desktop. Chaque locataire Azure Virtual Desktop doit être associé à l’instance Azure Active Directory contenant les utilisateurs qui se connecteront à l’environnement. À partir du locataire Azure Virtual Desktop, vous pouvez commencer à créer des pools d’hôtes pour exécuter des charges de travail de vos utilisateurs.

## <a name="host-pools"></a>Pools d’hôtes

Un pool d’hôtes est une collection de machines virtuelles Azure qui s’inscrivent à Azure Virtual Desktop en tant qu’hôtes de session quand vous exécutez l’agent Azure Virtual Desktop. Pour que l’expérience utilisateur soit cohérente, toutes les machines virtuelles hôtes de session dans un pool hôte doivent provenir de la même image.

Il existe deux types de pools hôtes :

- Personnel, où chaque hôte de session est affecté à des utilisateurs individuels.
- Mis en pool, où les hôtes de session peuvent accepter des connexions de tout utilisateur autorisé à un groupe d’applications au sein du pool hôte.

Vous pouvez définir des propriétés supplémentaires sur le pool d’hôtes pour modifier son comportement d’équilibrage de charge, pour déterminer le nombre de sessions pour chaque hôte de session et les opérations que l’utilisateur peut exécuter sur les hôtes de session dans le pool d’hôtes quand il est connecté à ses sessions Azure Virtual Desktop. Vous contrôlez les ressources publiées pour les utilisateurs au travers de groupes d’applications.

## <a name="app-groups"></a>Groupes d’applications

Un groupe d’applications est un regroupement logique d’applications installées sur des hôtes de session dans le pool hôte. Il existe deux types de groupes d’applications :

- RemoteApp, où les utilisateurs accèdent aux applications distantes que vous sélectionnez individuellement et publiez sur le groupe d’applications.
- Bureau, où les utilisateurs accèdent à l’intégralité du bureau.

Par défaut, un groupe de l’application Bureau est créé automatiquement chaque fois que vous créez un pool hôte. Vous pouvez supprimer ce groupe d’application à tout moment. En revanche, vous ne pouvez pas créer d’autre groupe d’applications de bureau dans le pool hôte tant que celui-ci contient déjà groupe d’applications de bureau. Pour publier des applications distantes, vous devez créer un groupe d’applications RemoteApp. Vous pouvez créer plusieurs groupes d’applications RemoteApp en fonction des différents scénarios de travail. Les différents groupes d’applications RemoteApp peuvent également contenir des applications distantes se chevauchant.

Pour publier des ressources pour des utilisateurs, vous devez affecter ceux-ci à des groupes d’applications. Lors de l’affectation d’utilisateurs à des groupes d’applications, tenez compte des considérations suivantes :

- Un utilisateur ne peut pas être affecté à la fois à un groupe d’applications Bureau et à un groupe d’applications RemoteApp au sein du même pool hôte.
- Un utilisateur peut être affecté à plusieurs groupes d’applications au sein du même pool hôte, dont les flux sont cumulés.

## <a name="tenant-groups"></a>Groupes de locataires

Dans Azure Virtual Desktop, c’est au niveau du locataire Azure Virtual Desktop qu’ont lieu la majeure partie des opérations d’installation et de configuration. Le locataire Azure Virtual Desktop contient les pools d’hôtes, les groupes d’applications et les affectations d’utilisateurs de groupe d’applications. Toutefois, il peut y avoir des cas où vous devez gérer plusieurs locataires Azure Virtual Desktop à la fois, en particulier si vous êtes fournisseur de services cloud (CSP) ou partenaire d’hébergement. Dans ces cas, vous pouvez utiliser un groupe de locataires Azure Virtual Desktop personnalisé pour y placer les locataires Azure Virtual Desktop de chaque client et gérer les accès de manière centralisée. Toutefois, si vous ne gérez qu’un seul locataire Azure Virtual Desktop, le concept de groupe de locataires ne s’applique pas et vous pouvez continuer à exécuter et gérer votre locataire existant dans le groupe de locataires par défaut.

## <a name="end-users"></a>Utilisateurs finaux

Une fois que vous avez attribué des utilisateurs à leurs groupes d’applications, ces utilisateurs peuvent se connecter à un déploiement Azure Virtual Desktop avec n’importe lequel des clients Azure Virtual Desktop.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur l’accès délégué et sur la manière d’attribuer des rôles aux utilisateurs en consultant [Accès délégué dans Azure Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Pour apprendre à configurer votre locataire Azure Virtual Desktop, consultez [Créer un locataire dans Azure Virtual Desktop](tenant-setup-azure-active-directory.md).

Pour savoir comment vous connecter à Azure Virtual Desktop, consultez l’un des articles suivants :

- [Se connecter à partir de Windows 10 ou Windows 7](connect-windows-7-10-2019.md)
- [Se connecter à partir d’un navigateur web](connect-web-2019.md)
