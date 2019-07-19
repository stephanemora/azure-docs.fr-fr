---
title: Attributs de sécurité communs pour Stockage Azure
description: Liste de contrôle des attributs de sécurité communs pour l’évaluation de Stockage Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 922273e3805004f6af068ea748c16f5675810144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001462"
---
# <a name="security-attributes-for-azure-storage"></a>Attributs de sécurité pour Stockage Azure

Cet article décrit les attributs de sécurité intégrés à Stockage Azure. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| OUI |  |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Chiffrement dans le réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| OUI | Prend en charge les mécanismes HTTPS/TLS standards.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| OUI | Consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Chiffrement au niveau des colonnes (Azure Data Services)| N/A |  |
| Appels d’API chiffrés| OUI |  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| N/A |  |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | |
| Prise en charge du tunneling forcé| N/A |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Indicateurs de performance Azure Monitor disponible à présent, début de la préversion des journaux d’activité |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| OUI | Azure Active Directory, clé partagée, jeton d’accès partagé. |
| Authorization| OUI | Autorisation de support via le contrôle d’accès en fonction du rôle (RBAC), les listes de contrôle d'accès POSIX et les jetons SAS |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion et de contrôle | OUI | Journal d’activité Azure Resource Manager |
| Journalisation et audit du plan de données| OUI | Journaux de diagnostic de service et début de la préversion de la journalisation Azure Monitor  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| OUI | Prise en charge du contrôle de version du fournisseur de ressources via les API Azure Resource Manager |