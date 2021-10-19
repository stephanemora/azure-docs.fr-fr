---
title: obtient un jeton d’accès à l’aide de Azure CLI ou Azure PowerShell service FHIR
description: Cet article explique comment obtenir un jeton d’accès pour le service FHIR à l’aide de la Azure CLI ou Azure PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 672089ac061430121916efff67280c08c1c6943f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619427"
---
# <a name="get-access-token-for-fhir-service-using-azure-cli-or-azure-powershell"></a>Obtenir un jeton d’accès pour le service FHIR à l’aide de Azure CLI ou Azure PowerShell

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Dans cet article, vous allez apprendre à obtenir un jeton d’accès pour le service FHIR dans les API de santé Azure (par le biais du service FHIR) à l’aide de la Azure CLI. Lorsque vous [approvisionnez le service FHIR](fhir-portal-quickstart.md), vous configurez un ensemble d’utilisateurs ou de principaux de service qui ont accès au service. Si votre ID d’objet utilisateur figure dans la liste des ID d’objet autorisés, vous pouvez accéder le service à l’aide d’un jeton obtenu à l’aide d’Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Dans cet article, vous allez apprendre à obtenir un jeton d’accès pour le service FHIR dans les API de santé Azure (par le biais du service FHIR) à l’aide de Azure PowerShell. Lorsque vous [approvisionnez le service FHIR](fhir-portal-quickstart.md), vous configurez un ensemble d’utilisateurs ou de principaux de service qui ont accès au service. Si votre ID d’objet utilisateur figure dans la liste des ID d’objet autorisés, vous pouvez accéder au service à l’aide d’un jeton obtenu à l’aide de Azure PowerShell.

[!INCLUDE [azure-powershell-requirements.md](../../../includes/azure-powershell-requirements.md)]

---
## <a name="obtain-a-token"></a>Obtenir un jeton

Le service FHIR utilise un `resource`  ou un `Audience` URI égal à l’URI du serveur FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Vous pouvez obtenir un jeton et le stocker dans une variable (nommée `$token`) avec la commande suivante :

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-with-fhir-service"></a>Utiliser avec le service FHIR

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

---

>[!div class="nextstepaction"]
>[Accédez à l’API FHIR à l’aide de Postman](../use-postman.md)
