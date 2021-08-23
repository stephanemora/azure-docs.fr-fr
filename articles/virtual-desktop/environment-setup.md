---
title: Environnement Azure Virtual Desktop – Azure
description: Découvrez les éléments de base d’un environnement Azure Virtual Desktop, comme les pools d’hôtes et les groupes d’applications.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b6162657520f91168f46c43c1d6d7f5cbfbe6d38
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031732"
---
# <a name="azure-virtual-desktop-environment"></a>Environnement Azure Virtual Desktop

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/environment-setup-2019.md).

Azure Virtual Desktop est un service qui offre aux utilisateurs un accès facile et sécurisé à leurs postes de travail virtualisés et à leurs applications distantes RemoteApps. Cette rubrique fournit des informations supplémentaires sur la structure générale de l’environnement Azure Virtual Desktop.

## <a name="host-pools"></a>Pools hôtes

Un pool hôte est une collection de machines virtuelles Azure qui s’inscrivent auprès d’Azure Virtual Desktop en tant qu’hôtes de session quand vous exécutez l’agent Azure Virtual Desktop. Pour que l’expérience utilisateur soit cohérente, toutes les machines virtuelles hôtes de session dans un pool hôte doivent provenir de la même image.

Il existe deux types de pools hôtes :

- Personnel, où chaque hôte de session est affecté à des utilisateurs individuels.
- Mis en pool, où les hôtes de session peuvent accepter des connexions de tout utilisateur autorisé à un groupe d’applications au sein du pool hôte.

Vous pouvez définir des propriétés supplémentaires sur le pool hôte pour modifier son comportement d’équilibrage de charge et pour déterminer le nombre de sessions pour chaque hôte de session ainsi que les opérations que l’utilisateur peut exécuter sur les hôtes de session dans le pool hôte quand il est connecté à leurs sessions Azure Virtual Desktop. Vous contrôlez les ressources publiées pour les utilisateurs au travers de groupes d’applications.

## <a name="app-groups"></a>Groupes d’applications

Un groupe d’applications est un regroupement logique d’applications installées sur des hôtes de session dans le pool hôte. Il existe deux types de groupes d’applications :

- RemoteApp, où les utilisateurs accèdent aux applications distantes que vous sélectionnez individuellement et publiez sur le groupe d’applications.
- Bureau, où les utilisateurs accèdent à l’intégralité du bureau.

Par défaut, un groupe de l’application Bureau est créé automatiquement chaque fois que vous créez un pool hôte. Vous pouvez supprimer ce groupe d’application à tout moment. En revanche, vous ne pouvez pas créer d’autre groupe d’applications de bureau dans le pool hôte tant que celui-ci contient déjà groupe d’applications de bureau. Pour publier des applications distantes, vous devez créer un groupe d’applications RemoteApp. Vous pouvez créer plusieurs groupes d’applications RemoteApp en fonction des différents scénarios de travail. Les différents groupes d’applications RemoteApp peuvent également contenir des applications distantes se chevauchant.

Pour publier des ressources pour des utilisateurs, vous devez affecter ceux-ci à des groupes d’applications. Lors de l’affectation d’utilisateurs à des groupes d’applications, tenez compte des considérations suivantes :

- Un utilisateur peut être affecté à la fois à un groupe d’applications de bureau et à un groupe d’applications RemoteApp au sein du même pool d’hôtes. Toutefois, les utilisateurs ne peuvent lancer qu’un seul type de groupe d’applications par session. Les utilisateurs ne peuvent pas lancer les deux types de groupes d’applications simultanément au cours d’une même session.
- Un utilisateur peut être affecté à plusieurs groupes d’applications au sein du même pool hôte, dont les flux sont cumulés.

## <a name="workspaces"></a>Workspaces

Un espace de travail est un regroupement logique de groupes d’applications dans Azure Virtual Desktop. Chaque groupe d’applications Azure Virtual Desktop doit être associé à un espace de travail pour que les utilisateurs puissent voir les applications et les bureaux distants qui y sont publiés.

## <a name="end-users"></a>Utilisateurs finaux

Une fois que vous avez attribué des utilisateurs à leurs groupes d’applications, ces utilisateurs peuvent se connecter à un déploiement Azure Virtual Desktop avec n’importe lequel des clients Azure Virtual Desktop.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur l’accès délégué et sur la manière d’attribuer des rôles aux utilisateurs en consultant [Accès délégué dans Azure Virtual Desktop](delegated-access-virtual-desktop.md).

Pour découvrir comment configurer votre pool d’hôtes Azure Virtual Desktop, consultez [Créer un pool d’hôtes avec le portail Azure](create-host-pools-azure-marketplace.md).

Pour découvrir comment vous connecter à Azure Virtual Desktop, consultez les articles suivants :

- [Se connecter avec Windows 10 ou Windows 7](./user-documentation/connect-windows-7-10.md)
- [Se connecter avec un navigateur web](./user-documentation/connect-web.md)
- [Se connecter avec le client Android](./user-documentation/connect-android.md)
- [Se connecter avec le client macOS](./user-documentation/connect-macos.md)
- [Se connecter avec le client iOS](./user-documentation/connect-ios.md)