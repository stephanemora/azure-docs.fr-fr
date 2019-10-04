---
title: Migrer des ressources de sécurité Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de sécurité Azure depuis Azure Allemagne vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: f411e4fce32e31bfd02db554999e467770409857
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033651"
---
# <a name="migrate-security-resources-to-global-azure"></a>Migrer des ressources de sécurité vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources de sécurité Azure depuis Azure Allemagne vers Azure global.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-active-directory"></a>Azure Active Directory

Pour plus d’informations sur la migration d’Azure Active Directory, consultez [Migrer des identités](./germany-migration-identity.md#azure-active-directory).

## <a name="key-vault"></a>Key Vault

Certaines fonctionnalités d’Azure Key Vault ne peuvent pas être migrées depuis Azure Allemagne vers Azure global.

### <a name="encryption-keys"></a>Clés de chiffrement

Vous ne pouvez pas migrer de clés de chiffrement. Créez de nouvelles clés dans la région cible, puis utilisez ces clés pour protéger la ressource cible (par exemple, Stockage Azure ou Azure SQL Database). Migrez en toute sécurité les données depuis l’ancienne région vers la nouvelle région.

### <a name="application-secrets"></a>Secrets de l’application

Les secrets d’application comprennent des certificats, des clés de compte de stockage et d’autres secrets liés à l’application. Lors d’une migration, créez d’abord un coffre de clés dans Azure global. Réalisez ensuite l’une des opérations suivantes :

- Créez de nouveaux secrets d’application.
- Lisez les secrets actuels dans Azure Allemagne, puis entrez la valeur dans le nouveau coffre.

```powershell
Get-AzKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```

Pour plus d'informations :

- Actualisez vos connaissances en complétant les [tutoriels sur Key Vault](https://docs.microsoft.com/azure/key-vault/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Key Vault](../key-vault/key-vault-overview.md).
- Examinez les [cmdlets PowerShell de Key Vault](/powershell/module/az.keyvault/).

## <a name="vpn-gateway"></a>Passerelle VPN

Actuellement, il n’est pas possible de migrer une instance de passerelles VPN Azure depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle instance de passerelles VPN Azure dans Azure global.

Vous pouvez collecter des informations sur la configuration de votre passerelle VPN actuelle à l’aide du portail ou de PowerShell. Dans PowerShell, utilisez un ensemble de cmdlets qui commencent par `Get-AzVirtualNetworkGateway*`.

Assurez-vous de mettre à jour la configuration locale. En outre, supprimez toutes les règles existantes des anciennes plages d’adresses IP une fois que vous aurez mis à jour votre environnement réseau Azure.

Pour plus d'informations :

- Actualisez vos connaissances en complétant les [tutoriels sur la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/#step-by-step-tutorials).
- Découvrez comment [créer une connexion de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).
- Examinez les cmdlets [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell.
- Lisez le billet de blog [Créer une connexion de site à site](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/).
  
## <a name="application-gateway"></a>Application Gateway

Actuellement, il n’est pas possible de migrer une instance Azure Application Gateway depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle passerelle dans Azure global.

Vous pouvez collecter des informations sur la configuration de votre passerelle actuelle à l’aide du portail ou de PowerShell. Dans PowerShell, utilisez un ensemble de cmdlets qui commencent par `Get-AzApplicationGateway*`.

Pour plus d'informations :

- Actualisez vos connaissances en complétant les [tutoriels sur Application Gateway](https://docs.microsoft.com/azure/application-gateway/#step-by-step-tutorials).
- Découvrez comment [créer une passerelle d’application](../application-gateway/quick-create-portal.md).
- Examinez les cmdlets [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Médias](./germany-migration-media.md)

