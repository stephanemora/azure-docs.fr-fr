---
title: Acquisition d’un jeton d’utilisateur dans Azure Cloud Shell
description: Comment acquérir un jeton pour l’utilisateur authentifié dans Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/29/2021
ms.openlocfilehash: 117fa3672c78de29cd88797add83fa6e3bf2bf79
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362730"
---
# <a name="acquire-a-token-in-azure-cloud-shell"></a>Acquérir un jeton dans Azure Cloud Shell

Azure Cloud Shell fournit un point de terminaison qui authentifie automatiquement l’utilisateur connecté au portail Azure. Utilisez ce point de terminaison pour obtenir des jetons d’accès afin d’interagir avec les services Azure.

## <a name="authenticating-in-the-cloud-shell"></a>Authentification dans Cloud Shell
Azure Cloud Shell a son propre point de terminaison qui interagit avec votre navigateur pour vous connecter automatiquement. Lorsque ce point de terminaison reçoit une demande, il renvoie la demande à votre navigateur, qui le transfère au cadre parent du portail. La fenêtre du portail envoie une demande à Azure Active Directory, et le jeton résultant est renvoyé.

Si vous souhaitez vous authentifier avec d’autres informations d’identification, vous pouvez le faire à l’aide des commandes `az login` ou `Connect-AzAccount`.

## <a name="acquire-and-use-access-token-in-cloud-shell"></a>Acquérir et utiliser un jeton d’accès dans Cloud Shell

### <a name="acquire-token"></a>Acquérir un jeton

Exécutez les commandes suivantes pour définir votre jeton d’accès d’utilisateur en tant que variable d’environnement, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The access token is $access_token
```

### <a name="use-token"></a>Utiliser un jeton

Exécutez la commande suivante pour obtenir la liste de toutes les machines virtuelles de votre compte, en utilisant le jeton acquis à l’étape précédente.

```
curl https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Compute/virtualMachines?api-version=2021-07-01 -H "Authorization: Bearer $access_token" -H "x-ms-version: 2019-02-02"
```

## <a name="handling-token-expiration"></a>Gestion de l’expiration du jeton

Le point de terminaison d’authentification local met en cache les jetons. Vous pouvez l’appeler aussi souvent que vous le souhaitez, et un appel d’authentification à Azure Active Directory se produira uniquement s’il n’y a aucun jeton stocké dans le cache ou si le jeton a expiré.

## <a name="limitations"></a>Limites
- Il existe une liste d’autorisation des ressources pour lesquelles les jetons Cloud Shell peuvent être fournis. Si vous exécutez une commande et recevez un message semblable à `"error":{"code":"AudienceNotSupported","message":"Audience https://newservice.azure.com/ is not a supported MSI token audience...."}`, vous êtes face à cette limitation. Vous pouvez ouvrir un cas sur [GitHub](https://github.com/Azure/CloudShell/issues) pour demander que ce service soit ajouté à la liste d’autorisation.
- Si vous vous connectez explicitement à l’aide de la commande `az login`, toute règle d’accès conditionnel que votre entreprise peut avoir en place sera évaluée en fonction du conteneur Cloud Shell plutôt que de la machine sur laquelle s’exécute votre navigateur. Le conteneur Cloud Shell n’est pas considéré comme un appareil géré pour ces stratégies, si bien que les droits peuvent être limités par la stratégie.
- Les identités managées Azure ne sont pas disponibles dans Azure Cloud Shell. [En savoir plus sur les identités managées Azure](../active-directory/managed-identities-azure-resources/overview.md).
