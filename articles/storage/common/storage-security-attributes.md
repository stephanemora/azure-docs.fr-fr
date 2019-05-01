---
title: Attributs de sécurité courants pour le stockage Azure
description: Une liste de vérification des attributs de sécurité courants pour l’évaluation de stockage Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7868b52fee991d4b9323fa0b7969aeca4dc83cdb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711957"
---
# <a name="common-security-attributes-for-azure-storage"></a>Attributs de sécurité courants pour le stockage Azure

La sécurité fait partie intégrante d'un service Azure. Cet article décrit les attributs de sécurité courantes intégrées à Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos :<ul><li>Chiffrement côté serveur</li><li>Chiffrement côté serveur avec des clés gérées par le client</li><li>Autres fonctionnalités de chiffrement (côté client, Always Encrypted, etc.)</ul>| Oui |  |
| Chiffrement en transit :<ul><li>Chiffrement Express Route</li><li>Dans le chiffrement du réseau virtuel</li><li>Chiffrement de réseau virtuel à réseau virtuel</ul>| Oui | Prend en charge les mécanismes HTTPS/TLS standard.  Les utilisateurs peuvent également chiffrer les données avant leur transmission au service. |
| Gestion de clé de chiffrement (clé CMK, BYOK, etc.).| Oui | Consultez [Storage Service Encryption à l’aide de clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Chiffrement au niveau colonne (Services de données Azure)| N/A |  |
| Appels d’API chiffrés| Oui |  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge l’injection de réseau virtuel| N/A |  |
| Prise en charge des pare-feu et l’isolement réseau| Oui | |
| Prise en charge de tunneling de forcé| N/A |  |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Mesures Azure Monitor disponible consigne désormais, version préliminaire de départ |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentification| Oui | Azure Active Directory, clé partagée, le jeton d’accès partagé. |
| Authorization| Oui | Autorisation de prise en charge par le biais de RBAC, les ACL POSIX et les jetons SAS |


## <a name="audit-trail"></a>Piste d’audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Audit et consignation de plan de contrôle et de gestion | Oui | Journal d’activité Azure Resource Manager |
| Audit et consignation de plan de données| Oui | Journaux de Diagnostic de service et l’aperçu de départ journalisation d’Azure Monitor  |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Oui | Prend en charge la gestion des versions du fournisseur de ressources via les API Azure Resource Manager |