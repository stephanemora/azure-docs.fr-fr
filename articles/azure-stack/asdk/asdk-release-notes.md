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
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 485d88a4765d7cedcb171a5b325fe5f366fff1f9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004750"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../azure-stack-updates.md#determine-the-current-version).

Restez informé des nouveautés concernant l’ASDK en vous abonnant au [![flux](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Version 1.1901.0.95

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

## <a name="build-11809090"></a>Build 1.1809.0.90

### <a name="new-features"></a>Nouvelles fonctionnalités

Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../azure-stack-update-1809.md#new-features) des notes de publication Azure Stack.

### <a name="fixed-issues"></a>Problèmes résolus

Pour obtenir la liste des problèmes corrigés dans cette version, consultez [cette section](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Problèmes connus

Pour obtenir la liste des problèmes connus dans cette version, consultez [cette section](../azure-stack-update-1809.md#known-issues-post-installation).