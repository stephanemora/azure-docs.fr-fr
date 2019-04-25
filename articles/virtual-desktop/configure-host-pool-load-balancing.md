---
title: Configurer la méthode d’équilibrage de charge Windows Virtual Desktop Preview - Azure
description: Comment configurer la méthode d’équilibrage de charge pour un environnement de bureau virtuel Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328882"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Configurer la méthode d’équilibrage de charge de Windows Virtual Desktop Preview

Configuration de la méthode d’équilibrage de charge pour un pool d’hôte vous permet d’ajuster l’environnement Windows virtuel Desktop Preview pour répondre aux besoins de vos besoins.

>[!NOTE]
> Cela ne s’applique pas à un pool persistant hôte bureau, car les utilisateurs ont toujours un mappage 1:1 pour un hôte de session au sein du pool de l’hôte.

## <a name="configure-breadth-first-load-balancing"></a>Configurer l’équilibrage de charge de prioritaire

L’équilibrage de charge de prioritaire est la configuration par défaut pour les nouveaux pools hôte non persistant. L’équilibrage de charge de prioritaire distribue les nouvelles sessions utilisateur sur tous les hôtes de session disponible dans le pool de l’hôte. Lors de la configuration d’équilibrage de charge prioritaire, vous pouvez définir une limite maximale d’une session par l’hôte de session dans le pool de l’hôte.

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

Pour configurer un pool d’hôte pour effectuer prioritaire équilibreur de charge sans ajustement de la limite maximale d’une session, exécutez l’applet de commande PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Pour configurer un pool d’hôte pour effectuer l’équilibrage de charge de prioritaire et à utiliser une nouvelle maximale limite de session, exécutez l’applet de commande PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurer l’équilibrage de charge de profondeur

L’équilibrage de charge de profondeur distribue les nouvelles sessions utilisateur vers un hôte de session disponible avec le plus grand nombre de connexions, mais n’a pas atteint son seuil de la limite maximale de la session. Lors de la configuration d’équilibrage de charge de profondeur, vous **doit** définir une limite maximale d’une session par l’hôte de session dans le pool de l’hôte.

Pour configurer un pool d’hôte pour effectuer l’équilibrage de charge de profondeur, exécutez l’applet de commande PowerShell suivante :

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
