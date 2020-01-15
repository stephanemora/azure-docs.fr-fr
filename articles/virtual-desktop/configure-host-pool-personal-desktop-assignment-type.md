---
title: Type d’affectation de bureau personnel Windows Virtual Desktop – Azure
description: Guide pratique pour configurer le type d’affectation pour un pool hôte de bureau personnel Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: 0db1ffe46165d91148b6980abbf89d03340e646e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477047"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Configurer le type d’affectation d’un pool hôte de bureau personnel

Vous pouvez configurer le type d’affectation de votre pool hôte de bureau personnel pour adapter votre environnement Windows Virtual Desktop à vos besoins. Cette rubrique explique comment configurer l’affectation automatique ou directe pour les utilisateurs.

>[!NOTE]
> Les instructions données dans cet article s’appliquent uniquement aux pools hôtes de bureau personnel, et non aux pools hôtes mis en pool, car les utilisateurs de ces derniers ne sont pas affectés à des hôtes de session spécifiques.

## <a name="configure-automatic-assignment"></a>Configurer l’affectation automatique

L’affectation automatique est le type d’affectation par défaut des nouveaux pools hôtes de bureau personnel créés dans l’environnement Windows Virtual Desktop. Elle ne nécessite pas d’hôte de session spécifique.

Pour affecter automatiquement des utilisateurs, affectez-les d’abord au pool hôte de bureau personnel pour qu’ils puissent voir le bureau dans leur flux. Quand un utilisateur affecté lance le bureau dans son flux, il demande un hôte de session disponible s’il n’est pas encore connecté au pool hôte, ce qui termine le processus d’affectation.

Avant de commencer, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview). 

> [!NOTE]
> Avant de suivre ces instructions, vérifiez que vous avez installé le module PowerShell Windows Virtual Desktop 1.0.1534.2001 (ou une version ultérieure).

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

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré le type d’affectation de bureau personnel, connectez-vous à un client Windows Virtual Desktop pour le tester dans le cadre d’une session utilisateur. Les deux guides pratiques suivants indiquent comment se connecter à une session à l’aide du client choisi :

- [Se connecter avec le client Windows Desktop](connect-windows-7-and-10.md)
- [Se connecter avec le client web](connect-web.md)
