---
title: Utiliser des identités managées pour les ressources dans Azure Cloud Shell
description: Authentifier du code avec MSI dans Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72328717"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Utiliser des identités managées pour les ressources Azure dans Azure Cloud Shell

Azure Cloud Shell prend en charge l’autorisation avec des identités managées pour les ressources Azure. Utilisez cette option pour récupérer des jetons d’accès afin de communiquer en toute sécurité avec les services Azure.

## <a name="about-managed-identities-for-azure-resources"></a>À propos des identités gérées pour les ressources Azure
La gestion sécurisée des informations d’identification qui doivent se trouver dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. Dans Cloud Shell, vous devrez peut-être authentifier la récupération à partir de Key Vault pour une information d’identification qui peut-être nécessaire à un script.

Les identités managées pour les ressources Azure simplifient la résolution de ce problème en fournissant aux services Azure une identité managée automatiquement dans Azure AD (Azure Active Directory). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

## <a name="acquire-access-token-in-cloud-shell"></a>Acquérir un jeton d’accès dans Cloud Shell

Exécutez les commandes suivantes pour définir votre jeton d’accès MSI en tant que variable d’environnement, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Gestion de l’expiration du jeton

Le sous-système MSI local met en cache des jetons. Par conséquent, vous pouvez l’appeler autant de fois que vous le souhaitez et un appel réseau vers Azure AD survient uniquement si :
- une absence dans le cache se produit du fait d’une absence de jeton dans le cache
- le jeton a expiré

Si vous mettez le jeton en cache dans votre code, vous devez être prêt à gérer les scénarios dans lesquels la ressource indique que le jeton a expiré.

Pour gérer les erreurs de jeton, visitez la [page MSI sur le curling des jetons d’accès MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Acquisition de jetons d’accès à partir de machines virtuelles MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
