---
title: Services Azure prenant en charge les identités managées pour ressources Azure
description: Liste des services qui prennent en charge les identités managées pour ressources Azure et l’authentification Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: ca7ce29adb0b83215b64065ef83ff476025b8e81
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199712"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités managées pour ressources Azure

Les identités managées pour ressources Azure fournissent automatiquement aux services Azure une identité managée dans Azure Active Directory. Avec une identité gérée, vous pouvez vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. L’intégration des identités managées pour ressources Azure et de l’authentification Azure AD sur Azure est en cours. Vérifiez régulièrement cette page si des mises à jour sont disponibles.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités gérées pour les ressources Azure

Les services Azure prenant en charge les identités managées pour les ressources Azure sont les suivants :

### <a name="azure-virtual-machines"></a>Machines virtuelles Azure

|Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE

Reportez-vous à la liste suivante pour configurer l'identité managée des machines virtuelles Azure (dans les régions où elles sont disponibles) :

- [Portail Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)
- [Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Groupes de machines virtuelles identiques Azure

|Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE

Reportez-vous à la liste suivante pour configurer l'identité managée des groupes Azure Virtual Machine Scale Sets (dans les régions où ils sont disponibles) :

- [Portail Azure](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)
- [Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Disponible | Disponible |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | Non disponible | Non disponible | Non disponible

Reportez-vous à la liste suivante pour configurer l'identité managée d'Azure App Service (dans les régions où il est disponible) :

- [Portail Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interface de ligne de commande Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modèle Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Disponible | Disponible |
| Attribuée par l'utilisateur | VERSION PRÉLIMINAIRE | Non disponible | Non disponible | Non disponible

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Functions (dans les régions où il est disponible) :

- [Portail Azure](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Interface de ligne de commande Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Modèle Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Disponible | Disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Logic Apps (dans les régions où il est disponible) :

- [Portail Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Modèle Azure Resource Manager](/azure/app-service/overview-managed-identity#deployment-template)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Data Factory V2 (dans les régions où il est disponible) :

- [Portail Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [Foundation](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Gestion des API Azure

Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Disponible | Disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Non disponible | Non disponible | Non disponible | Non disponible

Reportez-vous à la liste suivante pour configurer l'identité managée du service de gestion des API Azure (dans les régions où il est disponible) :

- [Modèle Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Type d'identité managée |  Toutes mises à la disposition générale<br>Régions Azure à l'échelle internationale | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Attribuée par le système | Linux : VERSION PRÉLIMINAIRE<br>Windows : Non disponible | Non disponible | Non disponible | Non disponible |
| Attribuée par l'utilisateur | Linux : VERSION PRÉLIMINAIRE<br>Windows : Non disponible | Non disponible | Non disponible | Non disponible

Reportez-vous à la liste suivante pour configurer l'identité managée du service Azure Container Instances (dans les régions où il est disponible) :

- [Interface de ligne de commande Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Modèle Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec des services client qui utilisent des identités managées pour ressources Azure.

| de diffusion en continu | ID de ressource | Statut | Attribuer l’accès |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Disponible | [Portail Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Interface de ligne de commande Azure](howto-assign-access-CLI.md) <br>[Modèle Azure Resource Manager](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | Disponible |  
| Azure Data Lake | `https://datalake.azure.net/` | Disponible |
| Azure SQL | `https://database.windows.net/` | Disponible |
| Hubs d'événements Azure | `https://eventhubs.azure.net` | VERSION PRÉLIMINAIRE |
| Azure Service Bus | `https://servicebus.azure.net` | VERSION PRÉLIMINAIRE |
| Stockage Azure | `https://storage.azure.com/` | VERSION PRÉLIMINAIRE |