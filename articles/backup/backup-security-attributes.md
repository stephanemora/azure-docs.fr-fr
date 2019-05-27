---
title: Attributs de sécurité courants pour la sauvegarde Azure
description: Liste de contrôle des attributs de sécurité communs pour l'évaluation de Sauvegarde Azure
services: backup
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 86b8ab96a94a6ffc44c304d8a0a689301560a989
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002797"
---
# <a name="security-attributes-for-azure-backup"></a>Attributs de sécurité pour la sauvegarde Azure

Cet article décrit les attributs de sécurité intégrées à Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Non | Utilisation du protocole HTTPS. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Non |  |
| Chiffrement au niveau colonne (Services de données Azure)| Non |  |
| Appels d’API chiffrés| Oui |  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non |  |
| Prise en charge l’injection de réseau virtuel| Non |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | Le tunneling forcé est pris en charge pour la sauvegarde de machine virtuelle. Le tunneling forcé n’est pas pris en charge pour les charges de travail s’exécutant à l’intérieur de machines virtuelles. |
| Prise en charge de tunneling de forcé| Non |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Log Analytics est pris en charge via des journaux de diagnostic. Consultez [surveiller la sauvegarde Azure protégé des charges de travail à l’aide d’Analytique de journal](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) pour plus d’informations. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | L’authentification est effectuée via Azure Active Directory. |
| Authorization| Oui | Des rôles RBAC intégrés et gérés par le client sont utilisés. Consultez [utiliser le contrôle d’accès pour gérer les points de récupération de sauvegarde Azure](/azure/backup/backup-rbac-rs-vault) pour plus d’informations. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion| Oui | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |
| Audit et consignation de plan de données| Non | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui|  |