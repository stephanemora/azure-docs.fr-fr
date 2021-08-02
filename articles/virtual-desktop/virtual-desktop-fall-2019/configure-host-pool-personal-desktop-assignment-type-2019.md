---
title: Type d’affectation de bureau personnel Azure Virtual Desktop (classique) – Azure
description: Guide pratique pour configurer le type d’affectation d’un pool d’hôtes de bureau personnel Azure Virtual Desktop (classique).
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 20123cb66bfd5fdd2b77c1a7c1afbae28a93f45e
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750117"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-azure-virtual-desktop-classic"></a>Configuration du type d’affectation d’un pool d’hôtes de bureau personnel pour Azure Virtual Desktop (classique)

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop (classique), qui ne prend pas en charge les objets Azure Virtual Desktop pour Azure Resource Manager. Si vous essayez de gérer des objets Azure Virtual Desktop pour Azure Resource Manager, consultez [cet article](../configure-host-pool-personal-desktop-assignment-type.md).

Vous pouvez configurer le type d’affectation de votre pool d’hôtes de bureau personnel pour adapter votre environnement Azure Virtual Desktop à vos besoins. Cette rubrique explique comment configurer l’affectation automatique ou directe pour les utilisateurs.

>[!NOTE]
> Les instructions données dans cet article s’appliquent uniquement aux pools hôtes de bureau personnel, et non aux pools hôtes mis en pool, car les utilisateurs de ces derniers ne sont pas affectés à des hôtes de session spécifiques.

## <a name="configure-automatic-assignment"></a>Configurer l’affectation automatique

L’affectation automatique est le type d’affectation par défaut des pools d’hôtes de bureau personnel créés dans l’environnement Azure Virtual Desktop. Elle ne nécessite pas d’hôte de session spécifique.

Pour affecter automatiquement des utilisateurs, affectez-les d’abord au pool hôte de bureau personnel pour qu’ils puissent voir le bureau dans leur flux. Quand un utilisateur affecté lance le bureau dans son flux, il demande un hôte de session disponible s’il n’est pas encore connecté au pool hôte, ce qui termine le processus d’affectation.

Avant de commencer, [téléchargez et importez le module PowerShell Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/) si vous ne l’avez pas déjà fait.

> [!NOTE]
> Avant de suivre ces instructions, assurez-vous d’avoir installé la version 1.0.1534.2001 ou une version ultérieure du module PowerShell Azure Virtual Desktop.

Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pour configurer un pool hôte de façon à affecter automatiquement des utilisateurs aux machines virtuelles, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Pour affecter un utilisateur au pool hôte de bureau personnel, exécutez la cmdlet PowerShell suivante :

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Configurer l’affectation directe

Avec l’affectation directe, contrairement à l’affectation automatique, vous devez affecter l’utilisateur au pool hôte de bureau personnel et à un hôte de session spécifique pour qu’il puisse se connecter à son bureau personnel. Si l’utilisateur n’est affecté qu’à un pool hôte sans affectation d’hôte de session, il n’aura pas accès aux ressources.

Pour configurer un pool hôte de sorte que l’affectation directe des utilisateurs aux hôtes de session soit obligatoire, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Pour affecter un utilisateur au pool hôte de bureau personnel, exécutez la cmdlet PowerShell suivante :

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Pour affecter un utilisateur à un hôte de session spécifique, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Supprimer une affectation d’utilisateurs

Vous pouvez supprimer une affectation d’utilisateurs, parce qu’un utilisateur n’a plus besoin du bureau personnel, il a quitté l’entreprise ou vous voulez réutiliser le bureau pour quelqu’un d’autre.

Actuellement, la seule façon de supprimer l’affectation d’utilisateurs d’un bureau personnel consiste à supprimer entièrement l’hôte de session. Pour supprimer l’hôte de session, exécutez cette applet de commande :

```powershell
Remove-RdsSessionHost
```

Si vous avez besoin de rajouter l’hôte de session dans le pool d’hôtes de bureau personnel, désinstallez Azure Virtual Desktop sur cet ordinateur, puis suivez la procédure décrite dans [Création d’un pool d’hôtes avec PowerShell](create-host-pools-powershell-2019.md) pour réinscrire l’hôte de session.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré le type d’affectation de bureau personnel, vous pouvez vous connecter à un client Azure Virtual Desktop pour le tester dans le cadre d’une session utilisateur. Ces deux procédures suivantes vous indiquent comment vous connecter à une session à l’aide du client de votre choix :

- [Se connecter avec le client Windows Desktop](connect-windows-7-10-2019.md)
- [Se connecter avec le client web](connect-web-2019.md)
