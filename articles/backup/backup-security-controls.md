---
title: Contrôles de sécurité pour la Sauvegarde Azure
description: Check-list des contrôles de sécurité pour l’évaluation de la Sauvegarde Azure
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: dacurwin
ms.openlocfilehash: 6c4fbdf378b1981e28f493e5a4e232ebbc0bbda0
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886198"
---
# <a name="security-controls-for-azure-backup"></a>Contrôles de sécurité pour la Sauvegarde Azure

Cet article décrit les contrôles de sécurité intégrés à la Sauvegarde Azure. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge de l’injection de réseau virtuel| Non |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Le tunneling forcé est pris en charge pour la sauvegarde de machine virtuelle. Le tunneling forcé n’est pas pris en charge pour les charges de travail s’exécutant à l’intérieur de machines virtuelles. |
| Prise en charge du tunneling forcé| Non |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Log Analytics est pris en charge via des journaux de diagnostic. Pour plus d’informations, consultez [Superviser les charges de travail protégées de Sauvegarde Azure à l’aide de Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |
| Journalisation et audit du plan de données| Non | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |
| Authorization| OUI | Des rôles RBAC intégrés et gérés par le client sont utilisés. Pour plus d’informations, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération de Sauvegarde Azure](/azure/backup/backup-rbac-rs-vault). |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Non |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Non | Utilisation du protocole HTTPS. |
| Appels d’API chiffrés| OUI |  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI|  |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [contrôles de sécurité intégrés sur les services Azure](../security/fundamentals/security-controls.md).