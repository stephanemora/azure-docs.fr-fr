---
title: Contrôles de sécurité
description: En savoir plus sur les contrôles de sécurité utilisés dans le service Sauvegarde Azure. Ces contrôles permettent au service d’empêcher, de détecter et de répondre aux failles de sécurité.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172140"
---
# <a name="security-controls-for-azure-backup"></a>Contrôles de sécurité pour la Sauvegarde Azure

Cet article décrit les contrôles de sécurité intégrés à la Sauvegarde Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |  |
| Prise en charge de l’injection de réseau virtuel| Non |  |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Le tunneling forcé est pris en charge pour la sauvegarde de machine virtuelle. Le tunneling forcé n’est pas pris en charge pour les charges de travail s’exécutant à l’intérieur de machines virtuelles. |  |
| Prise en charge du tunneling forcé| Non |  |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| | Documentation
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Log Analytics est pris en charge via des journaux de ressources. Pour plus d’informations, consultez [Superviser les charges de travail protégées de Sauvegarde Azure à l’aide de Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Journalisation et audit du plan de gestion et de contrôle| OUI | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |  |
| Journalisation et audit du plan de données| Non | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| | Documentation
|---|---|--|
| Authentication| OUI | L’authentification est effectuée via Azure Active Directory. |  |
| Authorization| OUI | Des rôles RBAC intégrés et gérés par le client sont utilisés. Pour plus d’informations, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération de Sauvegarde Azure](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Protection des données

| Contrôle de sécurité | Oui/Non | Notes | | Documentation
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | OUI | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non |  |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Non |  |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Non | Utilisation du protocole HTTPS. |  |
| Appels d’API chiffrés| OUI |  |  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| | Documentation
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI|  |  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
