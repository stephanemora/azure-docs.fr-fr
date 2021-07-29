---
title: Type d’affectation de bureau personnel Azure Virtual Desktop – Azure
description: Guide pratique pour configurer l’attribution directe ou automatique pour un pool hôte de bureau personnel Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 39e4b23b4f007c790dcfe495661628a53a9293d8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111743888"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurer le type d’affectation d’un pool hôte de bureau personnel

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (classique) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

Vous pouvez configurer le type d’affectation de votre pool hôte de bureau personnel pour adapter votre environnement Azure Virtual Desktop à vos besoins. Cette rubrique explique comment configurer l’affectation automatique ou directe pour les utilisateurs.

>[!NOTE]
> Les instructions données dans cet article s’appliquent uniquement aux pools hôtes de bureau personnel, et non aux pools hôtes mis en pool, car les utilisateurs de ces derniers ne sont pas affectés à des hôtes de session spécifiques.

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez déjà téléchargé et installé le module PowerShell pour Azure Virtual Desktop. Si ce n’est pas le cas, suivez les instructions de l’article [Configurer le module PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Configurer l’affectation automatique

L’affectation automatique est le type d’affectation par défaut des nouveaux pools hôtes de bureau personnel créés dans l’environnement Azure Virtual Desktop. Elle ne nécessite pas d’hôte de session spécifique.

Pour affecter automatiquement des utilisateurs, affectez-les d’abord au pool hôte de bureau personnel pour qu’ils puissent voir le bureau dans leur flux. Quand un utilisateur affecté lance le bureau dans son flux, il demande un hôte de session disponible s’il n’est pas encore connecté au pool hôte, ce qui termine le processus d’affectation.

Pour configurer un pool hôte de façon à affecter automatiquement des utilisateurs aux machines virtuelles, exécutez la cmdlet PowerShell suivante :

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Pour affecter un utilisateur au pool hôte de bureau personnel, exécutez la cmdlet PowerShell suivante :

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Configurer l’affectation directe

Avec l’affectation directe, contrairement à l’affectation automatique, vous devez affecter l’utilisateur au pool hôte de bureau personnel et à un hôte de session spécifique pour qu’il puisse se connecter à son bureau personnel. Si l’utilisateur n’est affecté qu’à un pool hôte sans affectation d’hôte de session, il n’aura pas accès aux ressources.

Pour configurer un pool hôte de sorte que l’affectation directe des utilisateurs aux hôtes de session soit obligatoire, exécutez la cmdlet PowerShell suivante :

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Pour affecter un utilisateur au pool hôte de bureau personnel, exécutez la cmdlet PowerShell suivante :

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Pour affecter un utilisateur à un hôte de session spécifique, exécutez la cmdlet PowerShell suivante :

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

Pour affecter directement un utilisateur à un hôte de session dans le portail Azure :

1. Connectez-vous au portail Azure sur <https://portal.azure.com>.
2. Entrez **Azure Virtual Desktop** dans la barre de recherche.
3. Sous **Services**, sélectionnez **Azure Virtual Desktop**.
4. Dans la page Azure Virtual Desktop, accédez au menu sur le côté gauche de la fenêtre, puis sélectionnez **Pools d’hôtes**.
5. Sélectionnez le nom du pool d’hôtes que vous souhaitez mettre à jour.
6. Ensuite, accédez au menu sur le côté gauche de la fenêtre et sélectionnez **Groupes d’applications**.
7. Sélectionnez le nom du groupe d’applications de bureau que vous souhaitez modifier, puis sélectionnez **Affectations** dans le menu situé à gauche de la fenêtre.
8. Sélectionnez **+ Ajouter**, puis sélectionnez les utilisateurs ou les groupes d’utilisateurs sur lesquels vous souhaitez publier ce groupe d’applications de bureau.
9. Sélectionnez **Affecter une machine virtuelle** dans la barre d’informations pour attribuer un hôte de session à un utilisateur.
10. Sélectionnez l’hôte de session que vous souhaitez attribuer à l’utilisateur, puis sélectionnez **Affecter**.
11. Sélectionnez l’utilisateur auquel vous souhaitez attribuer l’hôte de session dans la liste des utilisateurs disponibles.
12. Quand vous avez terminé, sélectionnez **Sélectionner**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré le type d’affectation de bureau personnel, connectez-vous à un client Azure Virtual Desktop pour le tester dans le cadre d’une session utilisateur. Ces deux procédures suivantes vous indiquent comment vous connecter à une session à l’aide du client de votre choix :

- [Se connecter avec le client Windows Desktop](connect-windows-7-10.md)
- [Se connecter avec le client web](connect-web.md)
- [Se connecter avec le client Android](connect-android.md)
- [Se connecter avec le client iOS](connect-ios.md)
- [Se connecter avec le client macOS](connect-macos.md)
