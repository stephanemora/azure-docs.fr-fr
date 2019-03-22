---
title: Configurer la bureau virtuel Windows l’équilibrage de charge (méthode) (version préliminaire) - Azure
description: Comment configurer la méthode d’équilibrage de charge pour un environnement de bureau virtuel Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318343"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurer la méthode d’équilibrage de charge de bureau virtuel Windows

Configuration de la méthode d’équilibrage de charge pour un pool d’hôte vous permet d’ajuster l’environnement de bureau virtuel Windows (version préliminaire) pour répondre aux besoins de vos besoins.

>[!NOTE]
> Cela ne s’applique pas à un pool persistant hôte bureau, car les utilisateurs ont toujours un mappage 1:1 pour un hôte de session au sein du pool de l’hôte.

## <a name="configure-breadth-first-load-balancing"></a>Configurer l’équilibrage de charge de prioritaire

L’équilibrage de charge de prioritaire est la configuration par défaut pour les nouveaux pools hôte non persistant. Équilibrage de charge prioritaire distribue les nouvelles sessions utilisateur sur tous les hôtes de session disponible dans le pool de l’hôte. Lors de la configuration d’équilibrage de charge prioritaire, vous pouvez définir une limite maximale d’une session par l’hôte de session dans le pool de l’hôte.

Tout d’abord, [télécharger et importer le module PowerShell de bureau virtuel Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell si vous n’avez pas déjà.

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
