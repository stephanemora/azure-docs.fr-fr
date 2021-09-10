---
title: Services de données avec Azure Arc – Versions de mise en production
description: Journal des versions par date de publication pour les services de données avec Azure Arc.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/06/2021
ms.topic: conceptual
ms.openlocfilehash: 3e636fe5ff6477f78f09d940b70099b8cbf41fb9
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532960"
---
# <a name="version-log"></a>Journal des versions

Le tableau suivant décrit les différentes versions au fur et à mesure de leur évolution :

|Date|Nom de la version.|Étiquette d’images conteneur|Préfixes et versions CRD|Version d’API ARM|Version d’extension Azure CLI `arcdata`|Version de l’extension du chart Helm Kubernetes avec Arc|Extension Arc Data pour Azure Data Studio|
|---|---|---|---|---|---|---|---|
|30 juillet 2021|SQL Managed Instance avec Arc Usage général et SQL Server avec Arc Disponibilité générale|v1.0.0_2021-07-30|`datacontrollers` : v1beta1, v1 <br/>`exporttasks.tasks` : v1beta1, v1 <br/>`monitors` : v1beta1, v1 <br/>`sqlmanagedinstances.sql` : v1beta1, v1 <br/>`postgresqls` : v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql` : v1beta1 <br/>`dags.sql` : v1beta1 <br/>|2021-08-01 (stable)|1.0|1.0.16701001, train de mise en production : stable|0.9.5|
|3 août 2021|Mise à jour de l’extension Azure Arc pour Azure Data Studio pour s’aligner sur la disponibilité générale du 30 juillet|Aucun changement|Aucun changement|Aucun changement|Aucun changement|Aucun changement|0.9.6|

Pour obtenir la CRD complète, ajoutez `.arcdata.microsoft.com` au préfixe. 
