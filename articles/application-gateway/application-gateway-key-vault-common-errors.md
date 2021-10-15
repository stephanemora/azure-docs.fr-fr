---
title: Erreurs de coffre de clés courantes dans Application Gateway
titleSuffix: Azure Application Gateway
description: Cet article identifie les problèmes liés aux coffres de clés et vous aide à les résoudre pour le bon fonctionnement d’Application Gateway.
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: cfcacf84b5a35862c3ef1423ed58ff4d5d7f3142
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272317"
---
# <a name="common-key-vault-errors-in-azure-application-gateway"></a>Erreurs de coffre de clés courantes dans Azure Application Gateway

Cet article vous aide à comprendre les détails des codes d’erreur de coffre de clés que vous pouvez rencontrer, notamment ce qui provoque ces erreurs. Cet article contient également les étapes à suivre pour résoudre ce type d’erreurs de configuration.

> [!TIP]
> Utilisez un identificateur de secret qui ne spécifie pas de version. Ainsi, Azure Application Gateway permute automatiquement le certificat, si une version plus récente est disponible dans Azure Key Vault. Voici un exemple d’URI de secret sans version : `https://myvault.vault.azure.net/secrets/mysecret/`.

## <a name="list-of-error-codes-and-their-details"></a>Liste des codes d’erreur et de leurs détails

Les sections suivantes couvrent les différentes erreurs que vous pouvez rencontrer. Vous en trouverez les détails dans Azure Advisor et vous pouvez utiliser cet article de résoudre des problèmes pour corriger les problèmes. Pour plus d’informations, consultez [Créer des alertes Azure Advisor lors de nouvelles recommandations à l’aide du portail Azure](../advisor/advisor-alerts-portal.md).

> [!NOTE]
> Azure Application Gateway génère des journaux pour les diagnostics de coffre de clés toutes les quatre heures. Si le diagnostic continue à afficher l’erreur une fois la configuration corrigée, vous devrez peut-être attendre que les journaux soient actualisés.

[comment]: # (Code d’erreur 1)
### <a name="error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>Code d’erreur : UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**Description :** L’identité managée affectée par l’utilisateur associée ne dispose pas de l’autorisation « Get ». 

**Résolution :** Configurez la stratégie d’accès de Key Vault pour accorder cette autorisation à l’identité managée affectée par l’utilisateur associée sur des secrets. 
1. Accédez au coffre de clés lié dans le portail Azure.
1. Ouvrez le volet **Stratégies d’accès**.
1. Pour le **Modèle d’autorisation**, sélectionnez **Stratégie d’accès au coffre**.
1. Sous **Opérations de gestion des secrets**, sélectionnez l’autorisation **Get**.
1. Sélectionnez **Enregistrer**.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" Capture d’écran montrant comment résoudre l’erreur d’autorisation Get.":::

Pour plus d’informations, consultez [Attribuer une stratégie d’accès Key Vault à l’aide du portail Azure](../key-vault/general/assign-access-policy-portal.md).

[comment]: # (Code d’erreur 2)
### <a name="error-code-secretdisabled"></a>Code d’erreur : SecretDisabled 

**Description :** Le certificat associé a été désactivé dans Key Vault. 

**Résolution :** Réactivez la version de certificat actuellement utilisée pour la Passerelle d’application.
1. Accédez au coffre de clés lié dans le portail Azure.
1. Ouvrez le volet **Certificats**.
1. Sélectionnez le nom du certificat nécessaire, puis la version désactivée.
1. Dans la page de gestion, utilisez le bouton bascule pour activer cette version de certificat.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="Capture d’écran montrant comment réactiver un secret.":::

[comment]: # (Code d’erreur 3)
### <a name="error-code-secretdeletedfromkeyvault"></a>Code d’erreur : SecretDeletedFromKeyVault 

**Description :** Le certificat associé a été supprimé de Key Vault. 

**Résolution :** Pour récupérer un certificat supprimé : 
1. Accédez au coffre de clés lié dans le portail Azure.
1. Ouvrez le volet **Certificats**.
1. Utilisez l’onglet **Gérer les certificats supprimés** pour récupérer un certificat supprimé.

En revanche, si un objet certificat est supprimé définitivement, vous devez créer un nouveau certificat et mettre à jour Application Gateway avec les détails du nouveau certificat. Quand vous effectuez la configuration par le biais d’Azure CLI ou d’Azure PowerShell, utilisez un URI d’identificateur de secret sans version. Ce choix permet aux instances de récupérer une version renouvelée du certificat, le cas échéant.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Capture d’écran montrant comment récupérer un certificat supprimé dans Key Vault.":::

[comment]: # (Code d’erreur 4)
### <a name="error-code-userassignedmanagedidentitynotfound"></a>Code d’erreur : UserAssignedManagedIdentityNotFound 

**Description :** L’identité managée affectée par l’utilisateur associée a été supprimée. 

**Résolution :** Pour réutiliser l’identité :
1. Recréez une identité managée portant le même nom que celui utilisé précédemment et sous le même groupe de ressources. Les journaux d’activité des ressources contiennent plus de détails. 
1. Après avoir créé l’identité, accédez à **Application Gateway - Contrôle d’accès (IAM)** . Attribuez au minimum le rôle **Lecteur** à l’identité.
1. Enfin, accédez à la ressource Key Vault souhaitée, puis définissez ses stratégies d’accès afin d’accorder des autorisations de secret **Get** pour cette nouvelle identité managée. 

Pour plus d’informations, consultez [Fonctionnement de l’intégration](./key-vault-certs.md#how-integration-works).

[comment]: # (Code d’erreur 5)
### <a name="error-code-keyvaulthasrestrictedaccess"></a>Code d’erreur : KeyVaultHasRestrictedAccess

**Description :** Il existe un paramètre réseau restreint pour Key Vault. 

**Résolution :** Cette erreur se produit quand vous activez le pare-feu Key Vault pour un accès restreint. Vous pouvez toujours configurer Application Gateway dans un réseau restreint de Key Vault, en effectuant les étapes suivantes :
1. Dans Key Vault, ouvrez le volet **Réseau**.
1. Sélectionnez l’onglet **Pare-feux et réseaux virtuels**, puis **Point de terminaison privé et réseaux sélectionnés**.
1. Ensuite, à l’aide de l’option Réseaux virtuels, ajoutez le réseau virtuel et le sous-réseau de votre instance Application Gateway. Pendant le processus, configurez également le point de terminaison de service « Microsoft.KeyVault » en cochant sa case.
1. Enfin, sélectionnez **Oui** pour autoriser les Services approuvés à contourner le pare-feu de Key Vault.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Capture d’écran montrant comment contourner l’erreur de réseau restreint.":::

[comment]: # (Code d’erreur 6)
### <a name="error-code-keyvaultsoftdeleted"></a>Code d’erreur : KeyVaultSoftDeleted 

**Description :** Le coffre de clés associé est en état de suppression réversible. 

**Résolution :** Dans le portail Azure, recherchez *key vault*. Sous **Services**, sélectionnez **coffres de clés**.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Capture d’écran montrant comment rechercher le service Key Vault.":::

Sélectionnez **Gérer les coffres supprimés**. À partir de là, vous pouvez trouver la ressource Key Vault supprimée et la récupérer.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="Capture d’écran montrant comment récupérer un coffre de clés supprimé.":::

[comment]: # (Code d’erreur 7)
### <a name="error-code-customerkeyvaultsubscriptiondisabled"></a>Code d’erreur : CustomerKeyVaultSubscriptionDisabled 

**Description :** L’abonnement à Key Vault est désactivé. 

**Résolution :** Votre abonnement Azure peut être désactivé pour différentes raisons. Pour effectuer l’action nécessaire à la résolution du problème, consultez [Réactivation d’un abonnement Azure désactivé](../cost-management-billing/manage/subscription-disabled.md).

## <a name="next-steps"></a>Étapes suivantes

Ces articles de résolution des problèmes peuvent être utiles si vous continuez à utiliser Application Gateway :

- [Vue d’ensemble du contrôle d’intégrité des ressources pour Azure Application Gateway](resource-health-overview.md)
- [Résoudre des problèmes d’affinité de session dans Azure Application Gateway](how-to-troubleshoot-application-gateway-session-affinity-issues.md)
