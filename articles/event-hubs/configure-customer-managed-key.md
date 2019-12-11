---
title: Configurer votre propre clé pour chiffrer les données Azure Event Hubs au repos
description: Cet article vous explique comment configurer votre propre clé pour chiffrer les données Azure Event Hubs au repos.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 3af951d120282767bd71bc569d8c0bfe39dafffe
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705462"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal-preview"></a>Configurer des clés gérées par le client pour chiffrer les données Azure Event Hubs au repos via le portail Azure (Préversion)
Azure Event Hubs fournit une fonctionnalité de chiffrement des données au repos avec Azure Storage Service Encryption (Azure SSE). Event Hubs utilise le service Stockage Azure pour stocker les données. Par défaut, toutes les données stockées avec ce service sont chiffrées à l'aide de clés gérées par Microsoft. 

>[!NOTE]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Nous vous déconseillons de l’utiliser dans un environnement de production.

## <a name="overview"></a>Vue d'ensemble
Azure Event Hubs prend désormais en charge le chiffrement des données au repos à l'aide de clés gérées par Microsoft ou de clés gérées par le client (Bring Your Own Key - BYOK). Cette fonctionnalité vous permet de créer, de faire pivoter, de désactiver et de révoquer l'accès aux clés gérées par le client qui sont utilisées pour chiffrer les données Azure Event Hubs au repos.

L'activation de la fonctionnalité BYOK sur votre espace de noms ne s'effectue qu'une seule fois.

> [!NOTE]
> La fonctionnalité BYOK est prise en charge par les clusters [Event Hubs Dedicated à un seul locataire](event-hubs-dedicated-overview.md). Elle ne peut pas être activée pour les espaces de noms Event Hubs standard.

Vous pouvez utiliser Azure Key Vault pour gérer vos clés et effectuer un audit sur leur utilisation. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../key-vault/key-vault-overview.md)

Cet article explique comment configurer un coffre de clés à l'aide de clés gérées par le client via le portail Azure. Pour savoir comment créer un coffre de clés à l’aide du portail Azure, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Pour utiliser des clés gérées par le client avec Azure Event Hubs, le coffre de clés doit contenir deux propriétés requises configurées. Il s'agit de :  **Suppression réversible** et **Ne pas vider**. Ces propriétés sont activées par défaut lorsque vous créez un coffre de clés dans le portail Azure. Toutefois, si vous devez activer ces propriétés sur un coffre de clés existant, vous devez utiliser PowerShell ou Azure CLI.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client
Pour activer des clés gérées par le client dans le portail Azure, procédez comme suit :

1. Accédez à votre cluster Event Hubs Dedicated.
1. Sélectionnez l'espace de noms sur lequel vous souhaitez activer la fonctionnalité BYOK.
1. Sur la page **Paramètres** de votre espace de noms Event Hubs, sélectionnez **Chiffrement (préversion)** . 
1. Sélectionnez **Clé gérée par le client**, comme indiqué sur l'illustration suivante. 

    ![Activer une clé gérée par le client](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurer un coffre de clés avec des clés
Après avoir activé une clé gérée par le client, vous devez l'associer à votre espace de noms Azure Event Hubs. Event Hubs prend uniquement en charge Azure Key Vault. Si vous activez l'option **Chiffrement à l'aide de la clé gérée par le client** dans la section précédente, vous devez importer la clé dans Azure Key Vault. En outre, les fonctionnalités **Suppression réversible** et **Ne pas vider** doivent être configurées pour la clé. Ces paramètres peuvent être configurés à l'aide de [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) ou de l'[interface CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Pour créer un coffre de clés, suivez le [guide de démarrage rapide](../key-vault/key-vault-overview.md) d'Azure Key Vault. Pour plus d'informations sur l'importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../key-vault/about-keys-secrets-and-certificates.md).
1. Pour activer à la fois la suppression réversible et la protection contre le vidage lors de la création d'un coffre, utilisez la commande [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create).

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pour ajouter la protection contre le vidage à un coffre existant (où la suppression réversible est déjà activée), utilisez la commande [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update).

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pour créer des clés, procédez comme suit :
    1. Pour créer une clé, sélectionnez **Générer/Importer** dans le menu **Clés** sous **Paramètres**.
        
        ![Sélectionner le bouton Générer/Importer](./media/configure-customer-managed-key/select-generate-import.png)
    1. Définissez **Options** sur **Générer** et donnez un nom à la clé.

        ![Créer une clé](./media/configure-customer-managed-key/create-key.png) 
    1. Vous pouvez maintenant sélectionner cette clé dans la liste déroulante pour l'associer à l'espace de noms Event Hubs à des fins de chiffrement. 

        ![Sélectionner une clé dans le coffre de clés](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Renseignez les détails de la clé, puis cliquez sur **Sélectionner**. Cela permettra de chiffrer les données au repos à l'aide d'une clé gérée par le client sur l'espace de noms. 

        > [!NOTE]
        > Dans la préversion, vous ne pouvez sélectionner qu'une seule clé. 

## <a name="rotate-your-encryption-keys"></a>Faire pivoter vos clés de chiffrement
Vous pouvez faire pivoter votre clé dans le coffre de clés à l'aide du mécanisme de rotation d'Azure Key Vault. Pour plus d'informations, consultez [Configurer l'audit et la rotation des clés](../key-vault/key-vault-key-rotation-log-monitoring.md). Des dates d'activation et d'expiration peuvent également être définies pour automatiser la rotation des clés. Le service Event Hubs détectera les nouvelles versions des clés et commencera à les utiliser automatiquement.

## <a name="revoke-access-to-keys"></a>Révoquer l'accès aux clés
La révocation de l'accès aux clés de chiffrement ne videra pas les données d'Event Hubs. Mais il sera impossible d'accéder aux données à partir de l'espace de noms Event Hubs. Vous pouvez révoquer la clé de chiffrement en appliquant une stratégie d'accès ou en supprimant la clé. Pour en savoir plus sur les stratégies d'accès et sur la sécurisation de votre coffre de clés, consultez [Sécuriser l'accès à un coffre de clés](../key-vault/key-vault-secure-your-key-vault.md).

Une fois la clé de chiffrement révoquée, le service Event Hubs devient inutilisable sur l'espace de noms chiffré. Si l'accès à la clé est activé ou si la clé de suppression est restaurée, le service Event Hubs choisira la clé afin de vous permettre d'accéder aux données à partir de l'espace de noms Event Hubs chiffré.

> [!NOTE]
> Si vous supprimez une clé de chiffrement existante de votre coffre de clés et que vous la remplacez par une nouvelle clé sur l'espace de noms Event Hubs, dans la mesure où la clé supprimée est toujours valide (car mise en cache) pendant une heure maximum, vos anciennes données (qui ont été chiffrées avec l'ancienne clé) peuvent rester accessibles avec les nouvelles données, qui ne sont désormais accessibles qu'à l'aide de la nouvelle clé. Il s'agit du comportement par défaut dans la préversion de la fonctionnalité. 

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic 
En configurant les journaux de diagnostic pour les espaces de noms BYOK, vous disposez des informations requises sur les opérations lorsqu'un espace de noms est chiffré à l'aide de clés gérées par le client. Ces journaux peuvent être activés et ultérieurement transmis en continu à un hub d'événements, analysés via l'analytique des journaux d'activité ou transmis en continu au stockage pour effectuer des analyses personnalisées. Pour en savoir plus sur les journaux de diagnostic, consultez [Présentation des journaux de diagnostic Azure](../azure-monitor/platform/resource-logs-overview.md).

## <a name="enable-user-logs"></a>Activer les journaux utilisateur
Pour activer les journaux relatifs aux clés gérées par le client, procédez comme suit.

1. Sur le portail Azure, accédez à l'espace de noms pour lequel la fonctionnalité BYOK est activée.
1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

    ![Sélectionner Paramètres de diagnostic](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Sélectionnez **+Ajouter le paramètre de diagnostic**. 

    ![Sélectionner Ajouter le paramètre de diagnostic](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Entrez un **nom** et sélectionnez l'emplacement où vous souhaitez diffuser les journaux.
1. Sélectionnez **CustomerManagedKeyUserLogs** et **Enregistrer**. Cette action active les journaux de la fonctionnalité BYOK sur l'espace de noms.

    ![Sélectionner l'option des journaux utilisateur des clés gérées par le client](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schéma du journal 
Tous les journaux d’activité sont stockés au format JSON (JavaScript Object Notation). Chaque entrée comporte des champs de type chaîne au format décrit dans le tableau suivant. 

| Nom | Description |
| ---- | ----------- | 
| TaskName | Description de la tâche en échec. |
| ActivityId | ID interne utilisé à des fins de suivi. |
| category | Définit la classification de la tâche. Par exemple, si la clé de votre coffre de clés est désactivée, il s'agit d'une catégorie d'informations ou, s'il est impossible d'annuler l'inclusion d'une clé dans un wrapper, il peut s'agir d'une erreur. |
| resourceId | ID de ressource Azure Resource Manager |
| keyVault | Nom complet du coffre de clés. |
| key | Nom de clé utilisé pour chiffrer l'espace de noms Event Hubs. |
| version | Version de la clé utilisée. |
| operation | Opération exécutée sur la clé dans votre coffre de clés. Par exemple, activation/désactivation de la clé ou inclusion/annulation de l'inclusion dans un wrapper. |
| code | Code associé à l'opération. Exemple : Code d'erreur ; 404 signifie que la clé est introuvable. |
| message | Tout message d'erreur associé à l'opération. |

Voici un exemple de journal pour une clé gérée par le client :

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Résolution des problèmes
Il est recommandé de toujours activer les journaux, comme indiqué dans la section précédente. Cela permet de suivre les activités lorsque le chiffrement BYOK est activé. Cela permet également d'identifier les problèmes.

Vous trouverez ci-dessous les codes d'erreur courants à rechercher lorsque le chiffrement BYOK est activé.

| Action | Code d'erreur | État consécutif des données |
| ------ | ---------- | ----------------------- | 
| Inclure une autorisation dans un wrapper/Annuler l'inclusion d'une autorisation dans un wrapper à partir d'un coffre de clés | 403 |    Inaccessible |
| Supprimer l'appartenance à un rôle AAD d'un principal AAD qui a accordé l'autorisation d'inclusion/d'annulation de l'inclusion dans un wrapper. | 403 |  Inaccessible |
| Supprimer une clé de chiffrement du coffre de clés | 404 | Inaccessible |
| Supprimer le coffre de clés | 404 | Inaccessible (si la suppression réversible - qui est un paramètre obligatoire - est activée.) |
| Modification de la période d'expiration de la clé de chiffrement pour qu'elle soit déjà obsolète | 403 |   Inaccessible  |
| Modification de la configuration NBF (pas avant) pour que la clé de chiffrement principale ne soit pas active | 403 | Inaccessible  |
| Sélection de l'option **Autoriser les services MSFT** pour le pare-feu du coffre de clés, ou autre type de blocage de l'accès réseau au coffre de clés contenant la clé de chiffrement | 403 | Inaccessible |
| Déplacement du coffre de clés vers un autre locataire | 404 | Inaccessible |  
| Problème réseau intermittent ou interruption de DNS/AAD/MSI |  | Accessible à l'aide d'une clé de chiffrement de données en cache |

> [!IMPORTANT]
> Pour activer la géorécupération d'urgence sur un espace de noms utilisant le chiffrement BYOK, l'espace de noms secondaire destiné à l'appariement doit se trouver dans un cluster dédié, et une identité managée affectée par le système doit y être activée. Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Si des points de terminaison de service de réseau virtuel sont configurés sur Azure Key Vault pour votre espace de noms Event Hubs, la fonctionnalité BYOK ne sera pas prise en charge. 


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :
- [Vue d’ensemble d’Event Hubs](event-hubs-about.md)
- [Vue d'ensemble de Key Vault](../key-vault/key-vault-overview.md)




