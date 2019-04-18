---
title: Attributs de sécurité courants pour la sauvegarde Azure
description: Liste de contrôle des attributs de sécurité communs pour l'évaluation de Sauvegarde Azure
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679677"
---
# <a name="common-security-attributes-for-azure-backup"></a>Attributs de sécurité courants pour la sauvegarde Azure

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui | Utilisation d’un chiffrement du service de stockage pour les comptes de stockage. |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Non  | Utilisation du protocole HTTPS. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non  |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Non  |  |
| Appels d’API chiffrés| Oui |  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Non  |  |
| Prise en charge de l’injection de réseau virtuel| Non  |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| Oui | Le tunneling forcé est pris en charge pour la sauvegarde de machine virtuelle. Le tunneling forcé n’est pas pris en charge pour les charges de travail s’exécutant à l’intérieur de machines virtuelles. |
| Prise en charge du tunneling forcé | Non  |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Log Analytics est pris en charge via des journaux de diagnostic. Consultez [surveiller la sauvegarde Azure protégé des charges de travail à l’aide d’Analytique de journal](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) pour plus d’informations. |

## <a name="iam-support"></a>Prise en charge d’IAM

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Gestion des accès – Authentification| Oui | L’authentification est effectuée via Azure Active Directory. |
| Gestion des accès – Autorisation| Oui | Des rôles RBAC intégrés et gérés par le client sont utilisés. Consultez [utiliser le contrôle d’accès pour gérer les points de récupération de sauvegarde Azure](/azure/backup/backup-rbac-rs-vault) pour plus d’informations. |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Toutes les actions déclenchées par le client à partir du portail Azure sont consignées dans des journaux d’activité. |
| Journalisation et audit du plan de données| Non  | Le plan de données de Sauvegarde Azure n’est pas accessible directement.  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui|  |