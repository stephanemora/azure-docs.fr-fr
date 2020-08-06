---
title: Obtenir un jeton d’accès à l’aide d’Azure CLI – API Azure pour FHIR
description: Cet article explique comment obtenir un jeton d’accès pour l’API Azure pour FHIR à l’aide d’Azure CLI.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: bdecf0dafa80f72eb528b4771c00befff8355e83
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072848"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Obtenir un jeton d’accès pour l’API Azure pour FHIR à l’aide d’Azure CLI

Cet article explique comment obtenir un jeton d’accès pour l’API Azure pour FHIR à l’aide d’Azure CLI. Lorsque vous [approvisionnez l’API Azure pour FHIR](fhir-paas-portal-quickstart.md), vous pouvez configurer un ensemble d’utilisateurs ou de principaux de service qui ont accès au service. Si votre ID d’objet utilisateur figure dans la liste des ID d’objet autorisés, vous pouvez accéder le service à l’aide d’un jeton obtenu à l’aide d’Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Se connecter avec Azure CLI

Avant de pouvoir obtenir un jeton, vous devez vous connecter en utilisant l’utilisateur pour lequel vous souhaitez obtenir un jeton :

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>Obtenir un jeton

L’API Azure pour FHIR utilise `resource` ou `Audience` avec l’URI du serveur FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com`. Vous pouvez obtenir un jeton et le stocker dans une variable (nommée `$token`) avec la commande suivante :

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Utilisation avec l’API Azure pour FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment obtenir un jeton d’accès pour l’API Azure pour FHIR à l’aide d’Azure CLI. Pour savoir comment accéder à l’API FHIR à l’aide de Postman, effectuez le didacticiel Postman.

>[!div class="nextstepaction"]
>[Accédez à l’API FHIR à l’aide de Postman](access-fhir-postman-tutorial.md)
