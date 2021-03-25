---
title: Résidence des données pour Azure Network Watcher | Microsoft Docs
description: Cet article vous aidera à comprendre la résidence des données pour le service Azure Network Watcher.
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
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b5aa8167031c3b871c6a6a4d84159c3c284bf241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018426"
---
# <a name="data-residency-for-azure-network-watcher"></a>Résidence des données pour Azure Network Watcher
Azure Network Watcher ne stocke pas les données client à l’exception du service Moniteur de connexion (préversion).


## <a name="connection-monitor-preview-data-residency"></a>Résidence des données de Moniteur de connexion (préversion)
Le service Moniteur de connexion (en préversion) stocke les données client. Ces données sont stockées automatiquement par Network Watcher dans une seule région. Ainsi Moniteur de connexion (préversion) répond automatiquement aux exigences en matière de résidence des données dans la région, y compris celles spécifiées dans le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Résidence des données
Dans Azure, la fonctionnalité qui permet le stockage de données client dans une seule région n’est actuellement disponible que dans la région Asie Sud-Est (Singapour) de la zone géographique Asie-Pacifique et la région Brésil Sud (État de Sao Paulo) de la zone géographique Brésil. Pour toutes les autres régions, les données client sont stockées dans Zone géographique. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Étapes suivantes

* Lisez une vue d’ensemble de [Network Watcher](./network-watcher-monitoring-overview.md).