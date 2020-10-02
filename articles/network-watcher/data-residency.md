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
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706832"
---
# <a name="data-residency-for-azure-network-watcher"></a>Résidence des données pour Azure Network Watcher
Azure Network Watcher ne stocke pas les données client à l’exception du service Moniteur de connexion (préversion).


## <a name="connection-monitor-preview-data-residency"></a>Résidence des données de Moniteur de connexion (préversion)
Le service Moniteur de connexion (en préversion) stocke les données client. Ces données sont stockées automatiquement par Network Watcher dans une seule région. Ainsi Moniteur de connexion (préversion) répond automatiquement aux exigences en matière de résidence des données dans la région, y compris celles spécifiées dans le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Résidence des données à Singapour

Dans Azure, la fonctionnalité permettant le stockage de données client dans une seule région n’est actuellement disponible que dans la région Asie Sud-Est (Singapour) de la zone géographique Asie-Pacifique. Pour toutes les autres régions, les données client sont stockées dans Zone géographique. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Étapes suivantes

* Lisez une vue d’ensemble de [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
