---
title: Accédez aux API Azure Healthcare avec la boucle
description: Cet article explique comment accéder aux API de santé avec la boucle
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: ddc7b291ba6ecd86e4915523345c40054794a993
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784944"
---
# <a name="access-the-healthcare-apis-preview-with-curl"></a>Accéder aux API de santé (préversion) à l’aide de la boucle 

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans cet article, vous allez apprendre à accéder aux API de santé Azure avec la boucle.

## <a name="prerequisites"></a>Prérequis

### <a name="powershell"></a>PowerShell

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous souhaitez exécuter le code localement, installez [PowerShell](/powershell/module/powershellget/) et [Azure AZ PowerShell](/powershell/azure/install-az-ps).
* si vous le souhaitez, vous pouvez exécuter les scripts dans Visual Studio Code avec l’extension du Client Rest. Pour plus d’informations, consultez [créer un lien vers la documentation du client Rest](using-rest-client.md).
* Téléchargez et installez la [boucle](https://curl.se/download.html).

### <a name="cli"></a>Interface de ligne de commande

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/).
* Si vous souhaitez exécuter le code localement, installez [Azure CLI](/cli/azure/install-azure-cli). 
* Si vous le souhaitez, vous pouvez installer un interpréteur de commandes bash, tel que git bash, qui est inclus dans [git pour Windows](https://gitforwindows.org/).
* si vous le souhaitez, exécutez les scripts dans Visual Studio Code avec l’extension du Client Rest. Pour plus d’informations, consultez [créer un lien vers la documentation du client Rest](using-rest-client.md).
* Téléchargez et installez la [boucle](https://curl.se/download.html).

## <a name="obtain-azure-access-token"></a>Obtenir un jeton d’accès Azure

Avant d’accéder aux API de santé, vous devez accorder à l’utilisateur ou à l’application cliente les autorisations appropriées. Pour plus d’informations sur l’octroi d’autorisations, consultez la rubrique autorisation des API pour les [soins de santé](authentication-authorization.md).

Il existe plusieurs façons d’obtenir un jeton d’accès Azure pour les API de santé. 

> [!NOTE]
> Assurez-vous que vous êtes connecté à Azure et que vous êtes dans l’abonnement Azure et le locataire où vous avez déployé l’instance des API de santé.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
### check Azure environment and PowerShell versions
Get-AzContext 
Set-AzContext -Subscription <subscriptionid>
$PSVersionTable.PSVersion
Get-InstalledModule -Name Az -AllVersions
curl --version

### get access token for the FHIR service
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
$token=(Get-AzAccessToken -ResourceUrl $fhirservice).Token

### Get access token for the DICOM service
$dicomtokenurl= "https://dicom.healthcareapis.azure.com/"
$token=$( Get-AzAccessToken -ResourceUrl $dicomtokenurl).Token
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
### check Azure environment and CLI versions
az account show --output table
az account set --subscription <subscriptionid>
cli –version
curl --version

### get access token for the FHIR service
$fhirservice=https://<fhirservice>.fhir.azurehealthcareapis.com
token=$(az account get-access-token --resource=$fhirservice --query accessToken --output tsv)

### get access token for the DICOM service
dicomtokenurl= https://dicom.healthcareapis.azure.com/
token=$(az account get-access-token --resource=$dicomtokenurl --query accessToken --output tsv)
```

---

## <a name="access-data-in-the-fhir-service"></a>Accéder aux données dans le service FHIR

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

---

`curl -X GET --header "Authorization: Bearer $token" $fhirservice/Patient`

[![Accédez aux données du service FHIR à l’aide d’un script de boucle. ](media/curl-fhir.png) ](media/curl-fhir.png#lightbox)

## <a name="access-data-in-the-dicom-service"></a>Accéder aux données dans le service DICOM

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
---

`curl -X GET --header "Authorization: Bearer $token" $dicomservice/changefeed?includemetadata=false`

[![Accédez aux données du service DICOM avec l’exécution d’un script de boucles. ](media/curl-dicom.png) ](media/curl-dicom.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à accéder aux données des API de santé à l’aide de la boucle.

pour en savoir plus sur l’accès aux données des api de santé à l’aide de l’extension Client REST dans Visual Studio Code, consultez 

>[!div class="nextstepaction"]
>[Accéder aux API de santé à l’aide du client REST](using-rest-client.md)