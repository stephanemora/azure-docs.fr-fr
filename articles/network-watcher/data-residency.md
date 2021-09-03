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
ms.openlocfilehash: 638c466e0d8573ce0b23f31188c5e952a28f74b2
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240459"
---
# <a name="data-residency-for-azure-network-watcher"></a>Résidence des données pour Azure Network Watcher
Avec l’exception du service Moniteur de connexion, Azure Network Watcher ne stocke pas les données client.


## <a name="connection-monitor-data-residency"></a>Résidence des données de Moniteur de connexion
Le service Moniteur de connexion stocke les données client. Ces données sont stockées automatiquement par Network Watcher dans une seule région. Ainsi Moniteur de connexion répond automatiquement aux exigences en matière de résidence des données dans la région, y compris celles spécifiées dans le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Résidence des données
Dans Azure, la fonctionnalité qui permet le stockage de données client dans une seule région n’est actuellement disponible que dans la région Asie Sud-Est (Singapour) de la zone géographique Asie-Pacifique et la région Brésil Sud (État de Sao Paulo) de la zone géographique Brésil. Pour toutes les autres régions, les données client sont stockées dans Zone géographique. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Étapes suivantes

* Lisez une vue d’ensemble de [Network Watcher](./network-watcher-monitoring-overview.md).
