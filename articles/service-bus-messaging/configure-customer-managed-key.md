---
title: Configurer votre propre clé pour chiffrer les données Azure Service Bus au repos
description: Cet article vous explique comment configurer votre propre clé pour chiffrer les données Azure Service Bus au repos.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851963"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>Configurer des clés gérées par le client pour chiffrer les données Azure Service Bus au repos à l’aide du Portail Azure (Préversion)
Azure Service Bus Premium fournit une fonctionnalité de chiffrement des données au repos à l’aide d’Azure Storage Service Encryption (Azure SSE). Service Bus Premium utilise le service Stockage Azure pour stocker les données. Par défaut, toutes les données stockées avec ce service sont chiffrées à l’aide de clés gérées par Microsoft. 

## <a name="overview"></a>Vue d'ensemble
Azure Service Bus prend désormais en charge le chiffrement des données au repos à l’aide de clés gérées par Microsoft ou de clés gérées par le client (Bring Your Own Key ; BYOK). Cette fonctionnalité vous permet de créer, de faire pivoter, de désactiver et de révoquer l’accès aux clés gérées par le client qui sont utilisées pour chiffrer les données Azure Service Bus au repos.

L'activation de la fonctionnalité BYOK sur votre espace de noms ne s'effectue qu'une seule fois.

> [!NOTE]
> Il existe quelques inconvénients à la clé gérée par le client pour le chiffrement côté service. 
>   * Cette fonctionnalité est prise en charge par le niveau de service [Azure Service Bus Premium](service-bus-premium-messaging.md). Elle ne peut pas être activée pour les espaces de noms Service Bus de niveau standard.
>   * Le chiffrement ne peut être activé que pour les espaces de noms nouveaux ou vides. Si l’espace de noms contient des données, l’opération de chiffrement échoue.
>   * Si des [points de terminaison de service de réseau virtuel](service-bus-service-endpoints.md) sont configurés sur Azure Key Vault pour votre espace de noms Service Bus, la fonctionnalité BYOK ne sera pas prise en charge. 

Vous pouvez utiliser Azure Key Vault pour gérer vos clés et effectuer un audit sur leur utilisation. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../key-vault/key-vault-overview.md)

Cet article explique comment configurer un coffre de clés à l'aide de clés gérées par le client via le portail Azure. Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Pour utiliser des clés gérées par le client avec Azure Service Bus, le coffre de clés doit contenir deux propriétés requises configurées. Il s'agit de :  **Suppression réversible** et **Ne pas vider**. Ces propriétés sont activées par défaut lorsque vous créez un coffre de clés dans le portail Azure. Toutefois, si vous devez activer ces propriétés sur un coffre de clés existant, vous devez utiliser PowerShell ou Azure CLI.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client
Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre espace de noms Service Bus Premium.
2. Sur la page **Paramètres** de votre espace de noms Service Bus, sélectionnez **Chiffrement (préversion)** .
3. Sélectionnez **Clé gérée par le client**, comme indiqué sur l'illustration suivante.

    ![Activer une clé gérée par le client](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurer un coffre de clés avec des clés

Après avoir activé une clé gérée par le client, vous devez l’associer à votre espace de noms Azure Service Bus. Service Bus prend uniquement en charge Azure Key Vault. Si vous activez l'option **Chiffrement à l'aide de la clé gérée par le client** dans la section précédente, vous devez importer la clé dans Azure Key Vault. En outre, les fonctionnalités **Suppression réversible** et **Ne pas vider** doivent être configurées pour la clé. Ces paramètres peuvent être configurés à l'aide de [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) ou de l'[interface CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Pour créer un coffre de clés, suivez le [guide de démarrage rapide](../key-vault/key-vault-overview.md) d'Azure Key Vault. Pour plus d'informations sur l'importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../key-vault/about-keys-secrets-and-certificates.md).
1. Pour activer à la fois la suppression réversible et la protection contre le vidage lors de la création d'un coffre, utilisez la commande [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create).

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pour ajouter la protection contre le vidage à un coffre existant (où la suppression réversible est déjà activée), utilisez la commande [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update).

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pour créer des clés, procédez comme suit :
    1. Pour créer une clé, sélectionnez **Générer/Importer** dans le menu **Clés** sous **Paramètres**.
        
        ![Sélectionner le bouton Générer/Importer](./media/configure-customer-managed-key/select-generate-import.png)

    1. Définissez **Options** sur **Générer** et donnez un nom à la clé.

        ![Créer une clé](./media/configure-customer-managed-key/create-key.png) 

    1. Vous pouvez maintenant sélectionner cette clé dans la liste déroulante pour l’associer à l’espace de noms Service Bus à des fins de chiffrement. 

        ![Sélectionner une clé dans le coffre de clés](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > À des fins de redondance, vous pouvez ajouter jusqu’à 3 clés. Dans le cas où l’une des clés a expiré ou n’est pas accessible, les autres clés seront utilisées pour le chiffrement.
        
    1. Renseignez les détails de la clé, puis cliquez sur **Sélectionner**. Cela permettra de chiffrer les données au repos à l'aide d'une clé gérée par le client sur l'espace de noms. 


> [!IMPORTANT]
> Si vous envisagez d’utiliser une clé gérée par le client avec la géorécupération d’urgence, veuillez consulter ce qui suit : 
>
> Pour activer le chiffrement au repos avec la clé gérée par le client, une [stratégie d’accès](../key-vault/key-vault-secure-your-key-vault.md) est configurée pour l’identité managée de Service Bus sur le coffre Azure Key Vault spécifié. Cela garantit un accès contrôlé à Azure Key Vault à partir de l’espace de noms Azure Service Bus.
>
> Pour cette raison :
> 
>   * Si [la géorécupération d’urgence](service-bus-geo-dr.md) est déjà activée pour l’espace de noms Service Bus et que vous cherchez à activer la clé gérée par le client, alors : 
>     * Brisez l’appairage.
>     * [Configurez la stratégie d’accès](../key-vault/managed-identity.md) de l’identité managée pour les espaces de noms principal et secondaire sur le coffre de clés.
>     * Configurez le chiffrement sur l’espace de noms principal.
>     * Appairez à nouveau les espaces de noms principal et secondaire.
> 
>   * Si vous envisagez d’activer la géorécupération d’urgence sur un espace de noms Service Bus où la clé gérée par le client est déjà configurée, alors :
>     * [Configurez la stratégie d’accès](../key-vault/managed-identity.md) de l’identité managée pour l’espace de noms secondaire sur le coffre de clés.
>     * Appairez les espaces de noms principal et secondaire.


## <a name="rotate-your-encryption-keys"></a>Faire pivoter vos clés de chiffrement

Vous pouvez faire pivoter votre clé dans le coffre de clés à l'aide du mécanisme de rotation d'Azure Key Vault. Pour plus d'informations, consultez [Configurer l'audit et la rotation des clés](../key-vault/key-vault-key-rotation-log-monitoring.md). Des dates d'activation et d'expiration peuvent également être définies pour automatiser la rotation des clés. Le service Service Bus détectera les nouvelles versions des clés et commencera à les utiliser automatiquement.

## <a name="revoke-access-to-keys"></a>Révoquer l'accès aux clés

La révocation de l’accès aux clés de chiffrement ne videra pas les données de Service Bus. Cependant, il sera impossible d’accéder aux données à partir de l’espace de noms Service Bus. Vous pouvez révoquer la clé de chiffrement en appliquant une stratégie d'accès ou en supprimant la clé. Pour en savoir plus sur les stratégies d'accès et sur la sécurisation de votre coffre de clés, consultez [Sécuriser l'accès à un coffre de clés](../key-vault/key-vault-secure-your-key-vault.md).

Une fois la clé de chiffrement révoquée, le service Service Bus devient inutilisable sur l’espace de noms chiffré. Si l’accès à la clé est activé ou si la clé supprimée est restaurée, le service Service Bus choisira la clé afin de vous permettre d’accéder aux données à partir de l’espace de noms Service Bus chiffré.

> [!NOTE]
> Si vous supprimez une clé de chiffrement existante de votre coffre de clés et que vous la remplacez par une nouvelle clé sur l’espace de noms Service Bus, dans la mesure où la clé supprimée est toujours valide (car mise en cache) pendant une heure maximum, vos anciennes données (qui ont été chiffrées avec l’ancienne clé) peuvent rester accessibles avec les nouvelles données, qui ne sont désormais accessibles qu’à l’aide de la nouvelle clé. Il s'agit du comportement par défaut dans la préversion de la fonctionnalité. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :
- [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md)
- [Vue d'ensemble de Key Vault](../key-vault/key-vault-overview.md)


