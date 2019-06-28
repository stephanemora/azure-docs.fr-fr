---
title: Configurer la méthode d’équilibrage de charge de la préversion de Windows Virtual Desktop - Azure
description: Comment configurer la méthode d’équilibrage de charge pour un environnement Windows Virtual Desktop ?
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328882"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Configurer la méthode d’équilibrage de charge de la préversion de Windows Virtual Desktop

Configurer la méthode d’équilibrage de charge d'un pool d’hôtes vous permet d’ajuster l’environnement de la préversion de Windows Virtual Desktop pour mieux répondre à vos besoins.

>[!NOTE]
> Cela ne s’applique pas à un pool d'hôtes de bureau persistant, car les utilisateurs disposent toujours d'un mappage 1:1 vers un hôte de session au sein du pool d’hôtes.

## <a name="configure-breadth-first-load-balancing"></a>Configurer l’équilibrage de charge de largeur

L’équilibrage de charge de largeur correspond à la configuration par défaut des nouveaux pools d'hôtes non persistants. L’équilibrage de charge de largeur répartit les nouvelles sessions utilisateur entre tous les hôtes de session du pool. Lorsque vous configurez l'équilibrage de charge de largeur, vous pouvez définir une limite maximale de sessions par hôte de session du pool.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

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
