---
title: 'Rechercher des ID d’objet d’identité pour l’authentification : API Azure pour FHIR'
description: Cet article explique comment localiser les ID d’objet d’identité nécessaires pour configurer l’authentification pour l’API Azure pour FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: b120b21a89a7105a25ba610402da99f9dce4b7e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033647"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Rechercher des ID d’objet d’identité pour la configuration de l’authentification

Dans cet article, vous allez apprendre à rechercher les ID d’objet d’identité nécessaires pour configurer la liste des ID d’objet d’identité autorisés pour l’API Azure pour FHIR.

Le service API Azure pour FHIR&reg; complètement managé est configuré pour autoriser l’accès uniquement à une liste prédéfinie d’ID d’objet d’identité. Lorsqu’une application ou un utilisateur tente d’accéder à l’API FHIR, un jeton de porteur doit être présenté. Ce jeton de porteur aura certaines revendications (champs). Pour accorder l’accès à l’API FHIR, le jeton doit contenir le bon émetteur (`iss`), le public (`aud`) et un ID d’objet (`oid`) à partir d’une liste d’ID d’objet autorisés. Un ID d’objet d’identité est l’ID d’objet d’un utilisateur ou d’un principal de service dans Azure Active Directory.

## <a name="configure-list-of-allowed-object-ids"></a>Configurer la liste des ID d’objet autorisés

Lorsque vous créez une nouvelle instance de l’API Azure pour FHIR, vous pouvez configurer une liste d’ID d’objet autorisés :

![Configurer les ID d’objet autorisés](media/quickstart-paas-portal/configure-allowed-oids.png)

Ces ID d’objet peuvent être des ID d’utilisateurs ou de principaux de service spécifiques dans votre Azure Active Directory.

## <a name="find-user-object-id-using-powershell"></a>Rechercher des ID d’objet utilisateur à l’aide de PowerShell

Si vous avez un utilisateur avec le nom d’utilisateur `myuser@consoso.com`, vous pouvez localiser les utilisateurs `ObjectId` à l’aide de la commande PowerShell suivante :

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

ou vous pouvez utiliser l’interface de ligne de commande Azure :

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id-using-powershell"></a>Rechercher un ID d’objet d’un principal de service à l’aide de PowerShell

Supposons que vous avez enregistré une [application cliente de service](register-service-azure-ad-client-app.md) et que vous souhaitez autoriser ce client de service pour accéder à l’API Azure pour FHIR, vous pouvez trouver l’ID d’objet du principal de service client avec la commande PowerShell suivante :

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

où `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` est l’ID d’application client du service. Sinon, vous pouvez utiliser le `DisplayName` du client de service :

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Si vous utilisez l’interface Azure CLI, vous pouvez utiliser :

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à rechercher les ID d’objet d’identité nécessaires pour configurer les identités autorisées à accéder à une instance de l’API Azure pour FHIR. Ensuite, déployez une API Azure pour FHIR complètement managée :
 
>[!div class="nextstepaction"]
>[Déployer le serveur FHIR open source](fhir-paas-portal-quickstart.md)