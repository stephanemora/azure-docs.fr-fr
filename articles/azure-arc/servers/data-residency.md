---
title: Résidence des données
description: Résidence des données et informations sur les serveurs avec Azure Arc.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559505"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Serveurs avec Azure Arc : Résidence des données

Cet article explique le concept de résidence des données et comment il s’applique aux serveurs avec Azure Arc.

Azure Arc enabled servers est **[disponible](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** dans les régions **États-Unis, Europe, Royaume-Uni, Australie et Asie-Pacifique**.

## <a name="data-residency"></a>Résidence des données

Les serveurs avec Azure Arc stockent les paramètres de configuration d’[extension de machine virtuelle Azure](manage-vm-extensions.md) (autrement dit, les valeurs de propriétés) qui doivent être spécifiés pour l’extension avant toute tentative d’activation sur la machine connectée. Par exemple, lorsque vous activez l’extension de machine virtuelle Log Analytics, l’**ID d’espace de travail** et la **clé primaire** Log Analytics vous sont demandés.

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

Les serveurs avec Azure Arc vous permettent de spécifier la région où vos données seront stockées. Microsoft peut répliquer vers d’autres régions à des fins de résilience des données, mais Microsoft ne réplique pas ou ne déplace pas les données en dehors de la zone géographique. Ces données sont stockées dans la région où la ressource de machine Azure Arc est configurée. Par exemple, si la machine est inscrite auprès d’Arc dans la région USA Est, ces données sont stockées dans la région USA.

Pour plus d’informations sur notre prise en charge de la résilience régionale et de la conformité, consultez [Géographie Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la conception de la [résilience Azure](/azure/architecture/reliability/architect).
