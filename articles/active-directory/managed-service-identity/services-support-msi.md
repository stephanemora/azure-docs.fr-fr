---
title: Services Azure qui prennent en charge l’identité du service administré
description: Liste des services qui prennent en charge l’identité du service administré et l’authentification Azure AD
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 9e49e7cdb9157fea2ae29d015bd84d391c73e71b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140673"
---
# <a name="services-that-support-managed-service-identity"></a>Services qui prennent en charge l’identité du service administré 

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Avec une identité gérée, vous pouvez vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. L’intégration de Managed Service Identity et de l’authentification Azure AD sur Azure est en cours. Vérifiez régulièrement cette page si des mises à jour sont disponibles.

## <a name="azure-services-that-support-managed-service-identity"></a>Services Azure qui prennent en charge l’identité du service administré

Les services Azure suivants prennent en charge l’identité du service administré.

| de diffusion en continu | Attribué par le système | Attribué par l’utilisateur| Configuration | Obtention d’un jeton |
| ------- | ------ | ---- | --------- | ----------- |
| Machines virtuelles Azure | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | [Portail Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[interface de ligne de commande Azure](qs-configure-cli-windows-vm.md)<br>[Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | VERSION PRÉLIMINAIRE | VERSION PRÉLIMINAIRE | [Portail Azure](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[interface de ligne de commande Azure](qs-configure-cli-windows-vmss.md)<br>[Modèles Microsoft Azure Resource Manager](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Disponible | Non disponible | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[interface de ligne de commande Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure Functions | Disponible | Non disponible | [Portail Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[interface de ligne de commande Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Modèle Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | VERSION PRÉLIMINAIRE | Non disponible | [Portail Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Foundation](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Gestion des API Azure | VERSION PRÉLIMINAIRE | Non disponible | [Modèle Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Services Azure qui prennent en charge l’authentification Azure AD

Les services suivants prennent en charge l’authentification Azure AD et ont été testés avec les services clients qui utilisent l’identité du service administré.

| de diffusion en continu | ID de ressource | Statut | Date | Attribuer l’accès |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Disponible | Septembre 2017 | [Portail Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[interface de ligne de commande Azure](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Disponible | Septembre 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponible | Septembre 2017 | |
| Azure SQL | https://database.windows.net/ | Disponible | Octobre 2017 | |
| Hubs d'événements Azure | https://eventhubs.azure.net | Disponible | Décembre 2017 | |
| Azure Service Bus | https://servicebus.azure.net | Disponible | Décembre 2017 | |
| Stockage Azure | https://azure.microsoft.com/services/storage/ | VERSION PRÉLIMINAIRE | Mai 2018 | |
