---
title: Erreurs Key Vault courantes dans Application Gateway
titleSuffix: Azure Application Gateway
description: Cet article permet d’identifier les problèmes liés au Coffre de clés (Key Vault) et de les résoudre pour les opérations lisses d’Application Gateway.
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: reference
ms.date: 07/12/2021
ms.author: jaysoni
ms.openlocfilehash: 9be1caf74a7eeaa225c2025767eeef86053c17a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532708"
---
# <a name="common-key-vault-errors-in-application-gateway"></a>Erreurs Key Vault courantes dans Application Gateway

Ce guide de dépannage vous aidera à comprendre les détails des codes d’erreur du Coffre de clés, leur cause et la ressource de Coffre de clés associée à l’origine du problème. Il comprend également un guide pas à pas pour la résolution de ce type de problèmes de configuration.

> [!NOTE]
> Les journaux de diagnostics du Coffre de clés dans Application Gateway sont générés toutes les quatre heures. Par conséquent, dans certains cas, vous devrez peut-être attendre que les journaux soient actualisés, si le diagnostic continue à afficher l’erreur une fois que vous avez corrigé la configuration.

> [!TIP]
> Nous vous recommandons d’utiliser un Identificateur de secret sans version. De cette façon, Application Gateway effectue automatiquement une rotation du certificat, si une version plus récente est disponible dans le Coffre de clés.  Exemple d’URI secret sans version `https://myvault.vault.azure.net/secrets/mysecret/`.

### <a name="list-of-error-codes-and-their-details"></a>Liste des codes d’erreur et de leurs détails

[comment]: # (Code d’erreur 1)
#### <a name="1-error-code-userassignedidentitydoesnothavegetpermissiononkeyvault"></a>**1) Code d’erreur :** UserAssignedIdentityDoesNotHaveGetPermissionOnKeyVault 

**Description :** L’Identité managée affectée par l’utilisateur associée ne dispose pas de l’autorisation « OBTENIR (GET) ». 

**Résolution :** Configurez la Stratégie d’accès du Coffre de clés pour accorder les autorisations OBTENIR pour l’Identité managée affectée par l’utilisateur associée sur les Secrets. 
1. Accédez au Coffre de clés lié dans le Portail Azure
2. Ouvrez le panneau des Stratégies d’accès
3. Sélectionnez « Stratégie d’accès au coffre » pour le Modèle d’autorisation
4. Sélectionnez « Obtenir » l’autorisation pour Secret pour l’Identité managée affectée par l’utilisateur
5. Enregistrer la configuration


:::image type="content" source="./media/application-gateway-key-vault-common-errors/no-get-permssion-for-managed-identity.png " alt-text=" L’Identité affectée par l’utilisateur ne dispose pas de l’autorisation Obtenir sur le Coffre de clés.":::

Pour obtenir le guide complet sur la Stratégie d’accès au Coffre de clés, reportez-vous à cet [article](../key-vault/general/assign-access-policy-portal.md)
</br></br>



[comment]: # (Code d’erreur 2)
#### <a name="2-error-code-secretdisabled"></a>**2) Code d’erreur  :** SecretDisabled 

**Description :** Le Certificat associé a été désactivé dans le Coffre de clés. 

**Résolution :** Réactivez la version de certificat actuellement utilisée pour la Passerelle d’application.
1. Accédez au Coffre de clés lié dans le Portail Azure
2. Ouvrez le panneau Certificats
3. Cliquez sur le nom du certificat requis, puis sur la version désactivée
4. Utilisez la bascule sur la page de gestion pour activer cette version de certificat

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-disabled.png" alt-text="Réactivez un Secret.":::
</br></br>


[comment]: # (Code d’erreur 3)
#### <a name="3-error-code-secretdeletedfromkeyvault"></a>**3) Code d’erreur :** SecretDeletedFromKeyVault 

**Description :** Le Certificat associé a été supprimé du Coffre de clés. 

**Résolution :** L’objet de certificat supprimé au sein d’un Coffre de clés peut être restauré à l’aide de sa fonctionnalité de récupération Soft-Delete. Pour récupérer un certificat supprimé, 
1. Accédez au Coffre de clés lié dans le Portail Azure
2. Ouvrez le panneau Certificats
3. Utilisez l’onglet « Certificats supprimés managés » pour récupérer un certificat supprimé.

En revanche, si un objet de certificat est supprimé définitivement, vous devrez créer un nouveau certificat et mettre à jour la Passerelle d’application avec les nouveaux détails du certificat. Lors de la configuration par le biais d’Azure CLI ou d’Azure PowerShell, il est recommandé d’utiliser un URI d’identificateur de secret sans version pour permettre aux instances de récupérer une version renouvelée du certificat, le cas échéant.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/secret-deleted.png " alt-text="Récupérer un certificat supprimé dans un Coffre de clés.":::
</br></br>


[comment]: # (Code d’erreur 4)
#### <a name="4-error-code-userassignedmanagedidentitynotfound"></a>**4) Code d’erreur :** UserAssignedManagedIdentityNotFound 

**Description :** L’Identité managée affectée par l’utilisateur associée a été supprimée. 

**Résolution :** Pour résoudre ce problème, suivez les instructions ci-dessous.
1. Recréez une Identité managée portant le même nom que celui utilisé précédemment et sous le même Groupe de ressources. Vous pouvez consulter les Journaux d’activité des ressources pour plus d’informations. 
2. Une fois créée, affectez au minimum à la nouvelle Identité managée le rôle Lecteur sous Passerelle d’application - Contrôle d’accès (IAM).
3. Enfin, accédez à la ressource du Coffre de clés souhaitée et définissez ses Stratégies d’accès pour autoriser Obtenir les Autorisations de secret pour cette nouvelle Identité managée. 

[Plus d’informations](./key-vault-certs.md#how-integration-works)
</br></br>

[comment]: # (Code d’erreur 5)
#### <a name="5-error-code-keyvaulthasrestrictedaccess"></a>**5) Code d’erreur :** KeyVaultHasRestrictedAccess

**Description:** Paramètres de Réseau restreint pour le Coffre de clés. 

**Résolution :** Vous rencontrerez ce problème lors de l’activation Pare-feu du Coffre de clés pour un accès restreint. Vous pouvez toujours configurer votre Passerelle d’application dans un réseau restreint de Coffre de clés de la manière suivante.
1. Sous le panneau Mise en réseau du Coffre de clés
2. Choisissez un Point de terminaison privé et des réseaux sélectionnés sous l’onglet « Pare-feu et Réseaux virtuels »
3. Enfin, sélectionnez « Oui » pour autoriser les Services approuvés à contourner le pare-feu du Coffre de clés.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-restricted-access.png" alt-text="Le Coffre de clés a un accès restreint.":::
</br></br>


[comment]: # (Code d’erreur 6)
#### <a name="6-error-code-keyvaultsoftdeleted"></a>**6) Code d’erreur :** KeyVaultSoftDeleted 

**Description :** Le Coffre de clés associé est en état de suppression réversible. 

**Résolution :** Récupérer un Coffre de clés en suppression réversible est assez simple. Dans le portail Azure, accédez à la page de service des Coffres de clés.

:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-1.png" alt-text="Recherchez le service Coffre de clés.":::
</br></br>
Cliquez sur l’onglet Coffres supprimés managés. À partir de là, vous pouvez trouver la ressource Coffre de clés supprimée et la récupérer.
:::image type="content" source="./media/application-gateway-key-vault-common-errors/key-vault-soft-deleted-2.png" alt-text="Récupérer un Coffre de clés supprimé à l’aide de la Suppression réversible.":::
</br></br>


[comment]: # (Code d’erreur 7)
#### <a name="7-error-code-customerkeyvaultsubscriptiondisabled"></a>**7) Code d’erreur :** CustomerKeyVaultSubscriptionDisabled 

**Description :** L’Abonnement au Coffre de clés est désactivé. 

**Résolution :** Votre abonnement Azure peut être désactivé en raison de diverses raisons. Veuillez vous référer au guide pour[Réactiver un abonnement Azure désactivé](../cost-management-billing/manage/subscription-disabled.md) et prendre les mesures nécessaires.
</br></br>



