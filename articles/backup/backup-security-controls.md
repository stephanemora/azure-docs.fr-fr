---
title: Contrôles de sécurité
description: En savoir plus sur les contrôles de sécurité utilisés dans le service Sauvegarde Azure. Ces contrôles permettent au service d’empêcher, de détecter et de répondre aux failles de sécurité.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 7ff3ff5c1b024a228778b0214e67239d3c8ab721
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89418753"
---
# <a name="security-controls-for-azure-backup"></a>Contrôles de sécurité pour la Sauvegarde Azure

Cet article décrit les contrôles de sécurité intégrés à la Sauvegarde Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|--|
| Prise en charge du point de terminaison de service| Non |  |  |
| Prise en charge de l’injection de réseau virtuel| Non |  |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | |  |
| Prise en charge du tunneling forcé pour les machines virtuelles Azure | Oui  |  |  |
| Prise en charge du tunneling forcé pour les applications fonctionnant sur des machines virtuelles Azure| Non  |  |  |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes| Documentation
|---|---|--|--|
| Prise en charge de la supervision Azure (comme Log analytics et App insights)| Oui | Log Analytics est pris en charge via des journaux de ressources. Pour plus d’informations, consultez [Superviser les charges de travail protégées de Sauvegarde Azure à l’aide de Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Journalisation et audit du plan de gestion et de contrôle| Oui | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |  |
| Journalisation et audit du plan de données| Non | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |  |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes| Documentation
|---|---|--|--|
| Authentification| Oui | L’authentification est effectuée via Azure Active Directory. |  |
| Autorisation| Oui | Des rôles Azure intégrés et gérés par le client sont utilisés. Pour plus d’informations, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération de Sauvegarde Azure](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes | Documentation
|---|---|--|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |  |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Non |  |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Non |  |  |
| Chiffrement en transit (comme ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Non | Utilisation du protocole HTTPS. |  |
| Appels d’API chiffrés| Oui |  |  |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes| Documentation
|---|---|--|--|
| Prise en charge de la gestion de la configuration (contrôle de version de la configuration, etc.)| Oui|  |  |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).
