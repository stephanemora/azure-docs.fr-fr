---
title: Services Azure prenant en charge les identités managées pour ressources Azure
description: Liste des services qui prennent en charge les identités managées pour ressources Azure et l’authentification Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 10/23/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 25da856d8bd72f4e74718cf32f62bbb0a3eb5b88
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984687"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités managées pour ressources Azure

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Avec une identité gérée, vous pouvez vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. L’intégration des identités managées pour ressources Azure et de l’authentification Azure AD sur Azure est en cours. Vérifiez régulièrement cette page si des mises à jour sont disponibles.

> [!NOTE]
> Identités managées pour les ressources Azure est le nouveau nom du service anciennement nommé Managed Service Identity (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Services Azure qui prennent en charge les identités gérées pour les ressources Azure

Les services Azure prenant en charge les identités managées pour les ressources Azure sont les suivants :

| de diffusion en continu | État affecté par le système | État affecté par l’utilisateur| Configuration | Obtention d’un jeton |
| ------- | ------ | ---- | --------- | ----------- |
| Machines virtuelles Azure | Disponible | VERSION PRÉLIMINAIRE | [Portail Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)<br>[Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | Disponible | VERSION PRÉLIMINAIRE | [Portail Azure](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Interface de ligne de commande Azure](qs-configure-cli-windows-vmss.md)<br>[Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Windows : disponible <br> Linux : prochainement | Non disponible | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interface de ligne de commande Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Disponible | Non disponible | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Interface de ligne de commande Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Logic Apps | Disponible | Non disponible | [Portail Azure](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#deployment-template) |  |
| Azure Data Factory V2 | Disponible | Non disponible | [Portail Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Foundation](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Gestion des API Azure | Disponible | Non disponible | [Modèle Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Azure Container Instances | Linux : préversion<br>Windows : non disponible | Linux : préversion<br>Windows : non disponible | [Interface de ligne de commande Azure](~/articles/container-instances/container-instances-managed-identity.md)<br>[Modèle Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec des services client qui utilisent des identités managées pour ressources Azure.

| de diffusion en continu | ID de ressource | Statut | Date | Attribuer l’accès |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | `https://management.azure.com/` | Disponible | Septembre 2017 | [Portail Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[interface de ligne de commande Azure](howto-assign-access-CLI.md) |
| Azure Key Vault | `https://vault.azure.net` | Disponible | Septembre 2017 | |
| Azure Data Lake | `https://datalake.azure.net/` | Disponible | Septembre 2017 | |
| Azure SQL | `https://database.windows.net/` | Disponible | Octobre 2017 | |
| Hubs d'événements Azure | `https://eventhubs.azure.net` | VERSION PRÉLIMINAIRE | Décembre 2017 | |
| Azure Service Bus | `https://servicebus.azure.net` | VERSION PRÉLIMINAIRE | Décembre 2017 | |
| Stockage Azure | `https://storage.azure.com/` | VERSION PRÉLIMINAIRE | Mai 2018 | |