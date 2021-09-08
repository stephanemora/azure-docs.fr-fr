---
title: Migrer des ressources de sécurité Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de sécurité Azure depuis Azure Allemagne vers Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: db8c13e214145df91f4b385f95c54e8084f3206c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "122562091"
---
# <a name="migrate-security-resources-to-global-azure"></a>Migrer des ressources de sécurité vers Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

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

Pour plus d’informations :

- Actualisez vos connaissances en complétant les [tutoriels sur Key Vault](../key-vault/index.yml).
- Examinez la [vue d’ensemble de Key Vault](../key-vault/general/overview.md).
- Examinez les [cmdlets PowerShell de Key Vault](/powershell/module/az.keyvault/).

## <a name="vpn-gateway"></a>Passerelle VPN

Actuellement, il n’est pas possible de migrer une instance de passerelles VPN Azure depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle instance de passerelles VPN Azure dans Azure global.

Vous pouvez collecter des informations sur la configuration de votre passerelle VPN actuelle à l’aide du portail ou de PowerShell. Dans PowerShell, utilisez un ensemble de cmdlets qui commencent par `Get-AzVirtualNetworkGateway*`.

Assurez-vous de mettre à jour la configuration locale. En outre, supprimez toutes les règles existantes des anciennes plages d’adresses IP une fois que vous aurez mis à jour votre environnement réseau Azure.

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur la passerelle VPN](../vpn-gateway/index.yml).
- Découvrez comment [créer une connexion de site à site](../vpn-gateway/tutorial-site-to-site-portal.md).
- Examinez les cmdlets [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell.
- Lisez le billet de blog [Créer une connexion de site à site](/archive/blogs/ralfwi/connecting-clouds).
  
## <a name="application-gateway"></a>Application Gateway

Actuellement, il n’est pas possible de migrer une instance Azure Application Gateway depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle passerelle dans Azure global.

Vous pouvez collecter des informations sur la configuration de votre passerelle actuelle à l’aide du portail ou de PowerShell. Dans PowerShell, utilisez un ensemble de cmdlets qui commencent par `Get-AzApplicationGateway*`.

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Application Gateway](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md).
- Découvrez comment [créer une passerelle d’application](../application-gateway/quick-create-portal.md).
- Examinez les cmdlets [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell.

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Outils d'administration](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)