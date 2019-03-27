---
title: Services Azure prenant en charge les identités managées pour ressources Azure
description: Liste des services qui prennent en charge les identités managées pour ressources Azure et l’authentification Azure AD
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd0612f865b82537e914ce6b6e062038a570c98
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449110"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités managées pour ressources Azure

Les identités managées pour ressources Azure fournissent automatiquement aux services Azure une identité managée dans Azure Active Directory. Avec une identité gérée, vous pouvez vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. L’intégration des identités managées pour ressources Azure et de l’authentification Azure AD sur Azure est en cours. Vérifiez régulièrement cette page si des mises à jour sont disponibles.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités gérées pour les ressources Azure

Les services Azure prenant en charge les identités managées pour les ressources Azure sont les suivants :

### <a name="azure-virtual-machines"></a>Machines virtuelles Azure

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | 
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE |

Reportez-vous à la liste suivante pour configurer l'identité managée des machines virtuelles Azure (dans les régions où elles sont disponibles) :

- [Portail Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)
- [Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Groupes de machines virtuelles identiques Azure

|Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE |

Reportez-vous à la liste suivante pour configurer l'identité managée des groupes Azure Virtual Machine Scale Sets (dans les régions où ils sont disponibles) :

- [Portail Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)
- [Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Disponible | Disponible |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée d'Azure App Service (dans les régions où il est disponible) :

- [Portail Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interface de ligne de commande Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modèle Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | VERSION PRÉLIMINAIRE | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour utiliser une identité gérée avec [Azure plans](../../governance/blueprints/overview.md):

- [Portail Azure - affectation de plan](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API REST - affectation de plan](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Type d'identité managée |Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Disponible | Disponible |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Functions (dans les régions où il est disponible) :

- [Portail Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interface de ligne de commande Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modèle Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | Non disponible | VERSION PRÉLIMINAIRE |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Logic Apps (dans les régions où il est disponible) :

- [Portail Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modèle Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Data Factory V2 (dans les régions où il est disponible) :

- [Portail Azure](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Kit SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Gestion des API Azure

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service de gestion des API Azure (dans les régions où il est disponible) :

- [Modèle Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Type d'identité managée | Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Attribuée par le système | Linux : VERSION PRÉLIMINAIRE<br>Windows : Non disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Linux : VERSION PRÉLIMINAIRE<br>Windows : Non disponible | Non disponible | Non disponible | Non disponible |

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Container Instances (dans les régions où il est disponible) :

- [Interface de ligne de commande Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modèle Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec des services client qui utilisent des identités managées pour ressources Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Reportez-vous à la liste suivante pour configurer l’accès à Azure Resource Manager :

- [Attribuer l’accès via le portail Azure](howto-assign-access-portal.md)
- [Attribuer l’accès via Powershell](howto-assign-access-powershell.md)
- [Attribuer l’accès via l’interface CLI Azure](howto-assign-access-CLI.md)
- [Attribuer l’accès via le modèle Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Disponible |
| Azure Government | `https://management.usgovcloudapi.net/` | Disponible |
| Azure Germany | `https://management.microsoftazure.de/` | Disponible |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Disponible |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Disponible |
| Azure Government | `https://vault.usgovcloudapi.net` | Disponible |
| Azure Germany |  `https://vault.microsoftazure.de` | Disponible |
| Azure China 21Vianet | `https://vault.azure.cn` | Disponible |

## <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Disponible |
| Azure Government |  | Non disponible |
| Azure Germany |   | Non disponible |
| Azure China 21Vianet |  | Non disponible |

## <a name="azure-sql"></a>Azure SQL 

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Disponible |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponible |
| Azure Germany | `https://database.cloudapi.de/` | Disponible |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponible |

## <a name="azure-event-hubs"></a>Hubs d'événements Azure

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | VERSION PRÉLIMINAIRE |
| Azure Government |  | Non disponible |
| Azure Germany |   | Non disponible |
| Azure China 21Vianet |  | Non disponible |

## <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | VERSION PRÉLIMINAIRE |
| Azure Government |  | Non disponible |
| Azure Germany |   | Non disponible |
| Azure China 21Vianet |  | Non disponible |

## <a name="azure-storage"></a>Stockage Azure

| Cloud | ID de ressource | Statut |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` | VERSION PRÉLIMINAIRE |
| Azure Government |  | Non disponible |
| Azure Germany |   | Non disponible |
| Azure China 21Vianet |  | Non disponible |
