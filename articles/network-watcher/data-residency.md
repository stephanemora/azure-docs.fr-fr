---
title: Résidence des données pour Azure Network Watcher | Microsoft Docs
description: Comprendre la résidence des données pour le service Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117833"
---
# <a name="data-residency-for-azure-network-watcher"></a>Résidence des données pour Azure Network Watcher
Azure Network Watcher ne stocke pas les données client à l’exception du service Moniteur de connexion (préversion).


## <a name="connection-monitor-preview-data-residency"></a>Résidence des données de Moniteur de connexion (préversion)
Le service *Moniteur de connexion (en préversion)* stocke les données client. Ces données sont stockées automatiquement par Network Watcher dans une seule région. Ainsi *Moniteur de connexion (préversion)* répond automatiquement aux exigences en matière de résidence des données dans la région, y compris celles spécifiées dans le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Résidence des données à Singapour

Dans Azure, la fonctionnalité permettant le stockage de données client dans une seule région n’est actuellement disponible que dans la région Asie Sud-Est (Singapour) de la zone géographique Asie-Pacifique. Pour toutes les autres régions, les données client sont stockées dans Zone géographique. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Réplication préalable** Les clients ont la possibilité de stocker les adresses IP des utilisateurs finaux avec leur instance Network Watcher afin que Network Watcher puisse surveiller l’accessibilité, la latence et les changements de topologie du réseau liés aux adresses IP des utilisateurs finaux. Ces adresses IP d’utilisateur final peuvent être classées en tant que données client. Depuis le 15 juillet 2020, Network Watcher stockait ces données dans une seule région. (Les données client ne sont plus répliquées dans HK.) Ces données ne sont plus répliquées dans HK. Ces données client sont chiffrées au repos.
> 
> Si ces données client étaient accessibles par un tiers, cela permettrait au tiers de connaître l’adresse IP, mais n’accorderait pas à l’utilisateur tiers l’accès à la machine ou au périphérique associé à l’adresse IP. Network Watcher estime que aucun tiers n’a accédé à ces données client dans HK.

## <a name="next-steps"></a>Étapes suivantes

* Lisez une vue d’ensemble de [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
