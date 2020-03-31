---
title: Configurer l’équilibrage de charge de Windows Virtual Desktop – Azure
description: Comment configurer la méthode d’équilibrage de charge pour un environnement Windows Virtual Desktop ?
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128309"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurer la méthode d’équilibrage de charge de Windows Virtual Desktop

La configuration de la méthode d’équilibrage de charge pour un pool d’hôtes vous permet d’ajuster l’environnement Windows Virtual Desktop afin de mieux répondre à vos besoins.

>[!NOTE]
> Cela ne s’applique pas à un pool d'hôtes de bureau persistant, car les utilisateurs disposent toujours d'un mappage 1:1 vers un hôte de session au sein du pool d’hôtes.

## <a name="configure-breadth-first-load-balancing"></a>Configurer l’équilibrage de charge de largeur

L’équilibrage de charge de largeur correspond à la configuration par défaut des nouveaux pools d'hôtes non persistants. L’équilibrage de charge de largeur répartit les nouvelles sessions utilisateur entre tous les hôtes de session du pool. Lorsque vous configurez l'équilibrage de charge de largeur, vous pouvez définir une limite maximale de sessions par hôte de session du pool.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/) à utiliser dans votre session PowerShell. Exécutez ensuite l’applet de commande suivante pour vous connecter à votre compte :

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pour configurer un pool d’hôtes à des fins d'équilibrage de charge de largeur sans ajuster la limite maximale de sessions, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Pour configurer un pool d’hôtes à des fins d'équilibrage de charge de largeur et utiliser une nouvelle limite maximale de sessions, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurer l’équilibrage de charge de profondeur

L’équilibrage de charge de profondeur répartit les nouvelles sessions utilisateur vers l'hôte de session disponible doté du plus grand nombre de connexions, sans avoir atteint sa limite maximale de sessions. Lorsque vous configurez l'équilibrage de charge de profondeur, vous **devez** définir une limite maximale de sessions par hôte de session du pool.

Pour configurer un pool d’hôtes à des fins d'équilibrage de charge de profondeur, exécutez la cmdlet PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
