---
title: Résidence des données
description: Résidence des données et informations sur les serveurs avec Azure Arc.
ms.topic: reference
ms.date: 08/05/2021
ms.custom: references_regions
ms.openlocfilehash: 1b32cb56658020f4464522db621155ebe03c5583
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524532"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Serveurs avec Azure Arc : résidence des données

Cet article explique le concept de résidence des données et comment il s’applique aux serveurs avec Azure Arc.

Les serveurs avec Azure Arc sont **[disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** aux **États-Unis, en Europe, au Royaume-Uni, en Australie et en Asie-Pacifique**.

## <a name="data-residency"></a>Résidence des données

Les serveurs avec Azure Arc stockent les paramètres de configuration d’[extension de machine virtuelle Azure](manage-vm-extensions.md) (autrement dit, les valeurs de propriétés). L’extension exige une spécification avant toute tentative d’activation sur la machine connectée. Par exemple, lorsque vous activez l’extension de machine virtuelle Log Analytics, l’**ID d’espace de travail** et la **clé primaire** Log Analytics vous sont demandés.

Les informations de métadonnées relatives à la machine connectée sont également collectées. Plus précisément :

* Nom, type et version du système d’exploitation
* Nom de l'ordinateur
* Nom de domaine complet (FQDN) de l’ordinateur
* Version de l’agent Machine connectée
* Nom de domaine complet (FQDN) Active Directory et DNS
* UUID (ID BIOS)
* Pulsation de l'agent Connected Machine
* Version de l’agent Machine connectée
* Clé publique pour l’identité managée
* État de conformité de la stratégie et détails (si vous utilisez des stratégies de configuration invitées Azure Policy)

Les serveurs avec Arc vous permettent de spécifier la région où vos données seront stockées. Microsoft peut répliquer vers d’autres régions à des fins de résilience des données, mais Microsoft ne réplique pas ou ne déplace pas les données en dehors de la zone géographique. Ces données sont stockées dans la région où la ressource de machine Azure Arc est configurée. Par exemple, si la machine est inscrite auprès d’Arc dans la région USA Est, ces données sont stockées dans la région USA.

> [!NOTE] 
> Pour l’Asie Sud-Est, vos données ne sont pas répliquées en dehors de cette région. 

Pour plus d’informations sur notre prise en charge de la résilience régionale et de la conformité, consultez [Géographie Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la conception de la [résilience Azure](/azure/architecture/reliability/architect).
