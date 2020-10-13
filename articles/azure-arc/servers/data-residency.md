---
title: Résidence des données
description: Résidence des données et informations sur les serveurs avec Azure Arc.
ms.topic: reference
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 73acb07b8caa3c1368c936463f04969c29d37985
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327763"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Serveurs avec Azure Arc : Résidence des données

Cet article explique le concept de résidence des données et comment il s’applique aux serveurs avec Azure Arc.

Les serveurs Azure Arc sont **[disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** dans les régions **États-Unis, Europe, Royaume-Uni ou Asie-Pacifique**.

## <a name="data-residency"></a>Résidence des données

Les serveurs avec Azure Arc stockent les paramètres de configuration d’[extension de machine virtuelle Azure](manage-vm-extensions.md) (autrement dit, les valeurs de propriétés) qui doivent être spécifiés pour l’extension avant toute tentative d’activation sur la machine connectée. Par exemple, lorsque vous activez l’extension de machine virtuelle Log Analytics, l’**ID d’espace de travail** et la **clé primaire** Log Analytics vous sont demandés.

Les informations de métadonnées relatives à la machine connectée sont également collectées. Plus précisément :

* Nom et version du système d’exploitation
* Nom de l'ordinateur
* Nom de domaine complet (FQDN) de l’ordinateur
* Version de l’agent Machine connectée

Les serveurs avec Azure Arc vous permettent de spécifier la région où vos données seront stockées. Microsoft peut répliquer vers d’autres régions à des fins de résilience des données, mais Microsoft ne réplique pas ou ne déplace pas les données en dehors de la zone géographique. Ces données sont stockées dans la région où la ressource de machine Azure Arc est configurée. Par exemple, si la machine est inscrite auprès d’Arc dans la région USA Est, ces données sont stockées dans la région USA.

Pour plus d’informations sur notre prise en charge de la résilience régionale et de la conformité, consultez [Géographie Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la conception de la [résilience Azure](/azure/architecture/reliability/architect).