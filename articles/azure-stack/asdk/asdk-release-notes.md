---
title: Notes de publication du Kit de développement Microsoft Azure Stack | Microsoft Docs
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 33f1ccf3f1c7bc657cc66efe7c5025356c954ad6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187759"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../azure-stack-updates.md#determine-the-current-version).

Restez informé des nouveautés concernant l’ASDK en vous abonnant au [![flux](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11902069"></a>Build 1.1902.0.69

### <a name="new-features"></a>Nouvelles fonctionnalités

- La build 1902 introduit une nouvelle interface utilisateur sur le portail Administrateur Azure Stack pour la création de plans, d’offres, de quotas et de plans complémentaires. Pour plus d’informations, y compris des captures d’écran, consultez [Créer des plans, des offres et des quotas](../azure-stack-create-plan.md).

- Pour obtenir la liste des autres changements et améliorations dans cette version, consultez [cette section](../azure-stack-update-1902.md#improvements) des notes de publication Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Problèmes connus

- Un problème a été identifié dans lequel les paquets supérieure à 1 450 octets vers un équilibreur de charge interne (ILB) sont ignorés. Le problème est dû au fait que le paramètre MTU sur l’hôte est trop faible pour prendre en charge des paquets VXLAN encapsulés qui passent par le rôle, ce qui, à compter de la version 1901, a été déplacé vers l’hôte. Il existe au moins deux scénarios où ce problème peut se manifester :

  - Requêtes SQL pour SQL Always On qui est derrière un équilibreur de charge interne (ILB) et faisant plus 660 octets.
  - Les déploiements Kubernetes échouent si vous essayez d’activer plusieurs maîtres.  

  Le problème se produit lorsque vous avez une communication entre une machine virtuelle et un équilibreur de charge interne sur le même réseau virtuel, mais sur des sous-réseaux différents. Vous pouvez contourner ce problème en exécutant les commandes suivantes dans une invite de commandes avec élévation de privilèges sur l’ordinateur hôte ASDK :

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Version 1.1901.0.95

Consultez les [informations importantes sur la build dans les notes de publication Azure Stack](../azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Changements

Cette version inclut les améliorations suivantes pour Azure Stack :

- Les composants BGP et NAT sont maintenant déployés sur l'hôte physique. Il n'est donc plus nécessaire d'avoir deux adresses IP publiques ou d'entreprise pour pouvoir déployer le kit ASDK, ce qui simplifie également le déploiement.
- Les sauvegardes des systèmes intégrés Azure Stack peuvent maintenant [être validées](asdk-validate-backup.md) à l’aide du script PowerShell **asdk-installer.ps1**.

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../azure-stack-update-1901.md#new-features) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Pour obtenir la liste des problèmes corrigés dans cette version, consultez [cette section](../azure-stack-update-1901.md#fixed-issues) des notes de publication Azure Stack. Pour obtenir la liste des problèmes connus, consultez [cette section](../azure-stack-update-1901.md#known-issues-post-installation).
- Notez que les [correctifs logiciels d’Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) ne sont pas applicables au kit ASDK Azure Stack.

## <a name="build-118110101"></a>Build 1.1811.0.101

### <a name="changes"></a>Changements

Cette version inclut les améliorations suivantes pour Azure Stack :  

- Il existe un ensemble de nouvelles configurations matérielles et logicielles minimales et recommandées pour ASDK. Ces nouvelles spécifications recommandées sont documentées dans [Considérations liées à la planification du déploiement d’Azure Stack](asdk-deploy-considerations.md). La plateforme Azure Stack ayant évolué, elle propose plus de services qui peuvent nécessiter davantage de ressources. L’augmentation des spécifications reflète ces recommandations révisées.

### <a name="new-features"></a>Nouvelles fonctionnalités

Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../azure-stack-update-1811.md#new-features) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

Pour obtenir la liste des problèmes corrigés dans cette version, consultez [cette section](../azure-stack-update-1811.md#fixed-issues) des notes de publication Azure Stack. Pour obtenir la liste des problèmes connus, consultez [cette section](../azure-stack-update-1811.md#known-issues-post-installation).
