---
title: Chiffrement au repos à l’aide de clés gérées par le client dans Azure Key Vault (version préliminaire) - recherche Azure
description: Chiffrement côté serveur de supplément sur les index et les cartes de synonymes dans Azure Search via les clés que vous créez et gérez dans Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 567f32cba76aaf2d1657b2476c4d11596d44dec5
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753959"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Chiffrement de recherche Azure à l’aide de clés gérées par le client dans Azure Key Vault

> [!Note]
> Le chiffrement avec des clés gérées par le client est en version préliminaire et les a pas été conçu pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Vous pouvez également utiliser la SDK .NET version 8.0 préliminaire.
>
> Cette fonctionnalité n’est pas disponible pour les services gratuits. Vous devez utiliser un service de recherche facturables créé l’ou après 2019-01-01. Il n’existe aucune prise en charge de portail pour l’instant.

Par défaut, recherche Azure chiffre le contenu de l’utilisateur au repos avec [clés gérées par le service](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Vous pouvez compléter le chiffrement par défaut avec une couche supplémentaire de chiffrement à l’aide de clés que vous créez et gérez dans Azure Key Vault. Cet article vous guide à travers les étapes.

Chiffrement côté serveur est prise en charge grâce à l’intégration avec [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés de chiffrement. Avec Azure Key Vault, vous pouvez également auditer l’utilisation des clés. 

Chiffrement avec des clés gérées par le client est configuré du niveau index ou synonyme carte lorsque ces objets sont créés et non sur le niveau de service de recherche. Vous ne pouvez pas chiffrer le contenu qui existe déjà. 

Vous pouvez utiliser différentes clés provenant de différents coffres de clé. Cela signifie que d’un service de recherche unique peut héberger plusieurs mappages indexes\synonym chiffré, chacune chiffrée potentiellement à l’aide d’une autre clé gérée par le client, en même temps que les mappages d’indexes\synonym qui ne sont pas chiffrés à l’aide de clés gérées par le client. 

## <a name="prerequisites"></a>Conditions préalables

Les services suivants sont utilisés dans cet exemple. 

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

+ [Créer une ressource Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou trouver un coffre existant sous votre abonnement.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) est utilisé pour les tâches de configuration.

+ [Postman](search-fiddler.md), [Azure PowerShell](search-create-index-rest-api.md) et [SDK Azure Search](https://aka.ms/search-sdk-preview) peut être utilisé pour appeler l’API REST d’évaluation. Il n’existe aucun portail ou la prise en charge du SDK .NET pour le chiffrement de gérée par le client pour l’instant.

## <a name="1---enable-key-recovery"></a>1 - activer la récupération de clé

Cette étape est facultatif mais fortement recommandé. Après avoir créé la ressource Azure Key Vault, activer **suppression réversible** et **Protection purger** dans le coffre de clés sélectionné en exécutant les commandes PowerShell ou Azure CLI suivantes :   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> En raison de la nature du chiffrement avec la fonctionnalité de clés gérées par le client, Azure Search ne sera pas en mesure de récupérer vos données si votre clé de coffre de clé Azure est supprimé. Pour éviter la perte de données provoquée par une suppression accidentelle de clé Key Vault, il est recommandé d’activer les purger de Protection et de suppression réversible sur le coffre de clés sélectionné. Pour plus d’informations, consultez [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 - créer une nouvelle clé

Si vous utilisez une clé existante pour chiffrer le contenu recherche Azure, ignorez cette étape.

1. [Connectez-vous au portail Azure](https://portal.azure.com) et accédez au tableau de bord coffre de clés.

1. Sélectionnez le **clés** définition à partir du volet de navigation gauche, puis cliquez sur **+ générer/importation**.

1. Dans le volet **Créer une clé**, dans la liste **Options**, choisissez la méthode que vous voulez utiliser pour créer une clé. Vous pouvez **Générer** une nouvelle clé, **Charger** une clé existante ou utiliser **Restaurer la sauvegarde** pour sélectionner une sauvegarde d’une clé.

1. Entrez un **nom** pour votre clé et éventuellement sélectionner d’autres propriétés de clé.

1. Cliquez sur le **créer** bouton pour démarrer le déploiement.

Prenez note de l’identificateur de clé : il est composé à partir de la **valeur Uri de la clé**, le **nom de la clé**et le **version de la clé**. Vous en aurez besoin de définir un index chiffré dans Azure Search.
 
![Créer une nouvelle clé de coffre de clés](./media/search-manage-encryption-keys/create-new-key-vault-key.png "créer une nouvelle clé de coffre de clés")

## <a name="3---create-a-service-identity"></a>3 - créer une identité de service

Affectation d’une identité à votre service de recherche permet d’accorder des autorisations d’accès de coffre de clés à votre service de recherche. Votre service de recherche utilise son identité auprès d’Azure Key vault.

Recherche Azure prend en charge deux méthodes pour l’affectation d’identité : une identité gérée ou une application Azure Active Directory géré en externe. 

Si possible, utilisez une identité gérée. Il est plus simple d’affectation d’une identité à votre service de recherche et doit fonctionner dans la plupart des scénarios. Si vous utilisez plusieurs clés d’index et de cartes de synonymes, ou si votre solution se trouve dans une architecture distribuée qui ne permet pas l’authentification basée sur l’identité, utilisez avancées [approche d’Azure Active Directory géré en externe](#aad-app)décrites à la fin de cet article.

 En règle générale, une identité gérée permet à votre service de recherche s’authentifier sur Azure Key Vault sans stocker les informations d’identification dans le code. Le cycle de vie de ce type d’identité gérée est liée au cycle de vie de votre service de recherche, qui ne peut avoir qu’une seule identité gérée. [En savoir plus sur les identités managé](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Pour créer une identité gérée, [connectez-vous au portail de toAzure](https://portal.azure.com) et ouvrez votre tableau de bord de service de recherche. 

1. Cliquez sur **identité** dans le volet de navigation de gauche, modifier son état à **sur**, puis cliquez sur **enregistrer**.

![Activer une identité gérée](./media/search-enable-msi/enable-identity-portal.png "activer une identité managée")

## <a name="4---grant-key-access-permissions"></a>4 - accorder des autorisations d’accès aux clés

Pour activer votre service de recherche à utiliser votre clé de coffre de clés, vous devrez accorder votre recherche certaines autorisations d’accès de service.

Autorisations d’accès peut être révoquées à tout moment donné. Une fois que révoqué, toute carte de service recherche index ou un synonyme qui utilise ce coffre de clés sera inutilisable. Restauration des autorisations d’accès de coffre de clé plus tard est restaurer l’accès de carte index\synonym. Pour plus d’informations, consultez [sécuriser l’accès à un coffre de clés](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de vue d’ensemble de votre coffre de clés. 

1. Sélectionnez le **stratégies d’accès** définition à partir du volet de navigation gauche, puis cliquez sur **+ Ajouter nouveau**.

   ![Ajouter une nouvelle stratégie d’accès de coffre de clés](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "ajouter une nouvelle stratégie d’accès de coffre de clés")

1. Cliquez sur **sélectionner le principal** et sélectionnez votre service recherche Azure. Vous pouvez rechercher pour elle par nom ou par l’ID d’objet qui a été affiché après avoir activé l’identité gérée.

   ![Entité de stratégie d’accès sélectionnez coffre de clés](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "principal de stratégie d’accès sélectionnez coffre de clés")

1. Cliquez sur **autorisations de clé** et sélectionnez *obtenir*, *Unwrap Key* et *Wrap Key*. Vous pouvez utiliser la *stockage Azure Data Lake ou stockage Azure* modèle permettent de sélectionner rapidement les autorisations requises.

   Recherche Azure doit être accordée par le code suivant [autorisations d’accès](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Obtenir* -permet à votre service de recherche récupérer les parties de votre clé dans un coffre de clés publiques
   * *Wrap Key* -permet à votre service de recherche à utiliser votre clé pour protéger la clé de chiffrement interne
   * *Unwrap Key* -permet à votre service de recherche à utiliser votre clé à désencapsuler la clé de chiffrement interne

   ![Sélectionnez les autorisations de clé de coffre de clés accès stratégie](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "sélectionner les autorisations de clé de stratégie accès coffre de clés")

1. Cliquez sur **OK** et **enregistrer** les modifications de stratégie d’accès.

> [!Important]
> Le contenu chiffré dans Azure search est configuré pour utiliser une clé Azure Key Vault spécifique avec un spécifique **version**. Si vous modifiez la clé ou une version, le mappage de l’index ou le synonyme doit être mis à jour pour utiliser le nouveau key\version **avant** suppression le key\version précédente. Ne parvient pas à faire affichera l’index ou le synonyme mapper inutilisable, vous ne pourrez pas déchiffrer le contenu une fois que l’accès de la clé est perdue.   

## <a name="5---encrypt-content"></a>5 - chiffrer le contenu

Création d’une carte d’index ou le synonyme chiffrée avec la clé gérée par le client n’est pas encore possible à l’aide du portail Azure. Permet d’utiliser l’API REST de recherche d’Azure pour créer un mappage ces index ou le synonyme.

Prise en charge un nouveau niveau supérieur de mapper des index et synonyme **encryptionKey** propriété utilisée pour spécifier la clé. 

À l’aide de la **coffre de clés Uri**, **nom de la clé** et **version de la clé** de votre clé Key vault, nous pouvons créer un **encryptionKey** définition :

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Aucune de ces détails du coffre de clés sont considérés comme secret et peut être facilement récupéré en accédant à la page appropriée de clés Azure Key Vault dans le portail Azure.

Si vous utilisez une application AAD pour l’authentification de coffre de clés au lieu d’utiliser une identité gérée, ajouter l’application AAD **accéder aux informations d’identification** pour votre clé de chiffrement : 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Exemple : Chiffrement de l’index
Détails de la création d’un nouvel index via l’API REST a pu être trouvés à [Create Index (API REST Service Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index), où la seule différence ici est spécifiant les détails de la clé de chiffrement dans le cadre de la définition d’index : 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Vous pouvez désormais envoyer la demande de création d’index et puis démarrer normalement à l’aide de l’index.

## <a name="example-synonym-map-encryption"></a>Exemple : Chiffrement de carte de synonyme

Détails de la création d’une nouvelle carte de synonymes via l’API REST, consultez [créer une carte de synonymes (API REST Service Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), où la seule différence ici est spécifiant les détails de la clé de chiffrement dans le cadre de la définition de la carte de synonyme : 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Vous pouvez désormais envoyer la demande de création de carte de synonyme et puis démarrer normalement.

>[!Important] 
> Bien que **encryptionKey** ne peut pas être ajouté aux index de recherche Azure existants ou les cartes de synonymes, il peut être mis à jour en fournissant des valeurs différentes pour les trois détails de coffre de clés (par exemple, la mise à jour de la version de la clé). Si vous modifiez une nouvelle clé de coffre de clés ou une nouvelle version de clé, tout mappage d’index ou un synonyme de recherche Azure qui utilise la clé doit tout d’abord être mis à jour pour utiliser le nouveau key\version **avant** suppression le key\version précédente. Ne parvient pas à faire affichera l’index ou carte de synonymes inutilisable, car il ne pourra pas déchiffrer le contenu accès une fois la clé est perdue.   
> Restauration des autorisations d’accès de coffre de clé plus tard restaurera l’accès au contenu.

## <a name="aad-app"></a> Avancé : Utiliser une application Azure Active Directory gérée en externe

Lorsqu’une identité gérée n’est pas possible, que vous pouvez créer une application Azure Active Directory avec une sécurité principal pour votre service Azure Search. Plus précisément, une identité gérée n’est pas viable dans ces conditions :

* Vous ne pouvez pas directement accorder de votre recherche service les autorisations d’accès pour le coffre de clés (par exemple, si le service de recherche est dans un autre client d’Active Directory que le coffre de clés Azure).

* Un seul service de recherche est nécessaire pour héberger plusieurs mappages de cryptée indexes\synonym, chacun utilisant une autre clé à partir d’un coffre de clés différentes, où chaque coffre de clés doit utiliser **une autre identité** pour l’authentification. Si vous utilisez une autre identité pour gérer différents coffres de clés n’est pas obligatoire, envisagez d’utiliser l’option d’identité gérée ci-dessus.  

Pour prendre en compte ces topologies, la fonction recherche Azure prend en charge à l’aide d’applications Azure Active Directory (AAD) pour l’authentification entre votre service de recherche et le coffre de clés.    
Pour créer une application AAD dans le portail :

1. [Créez une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenir la clé d’authentification et l’ID application](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) comme ceux sera nécessaire pour la création d’un index de cryptée. Vous devrez fournir les valeurs incluent **ID d’application** et **clé d’authentification**.

>[!Important]
> Lorsque vous décidez d’utiliser une application AAD d’authentification au lieu d’une identité gérée, prendre en compte le fait que Azure Search n’est pas autorisé à gérer votre application AAD à votre place, et il vous incombe de gérer votre application AAD, telles que la rotation périodique de la clé d’authentification d’application.
> Lorsque vous modifiez une application AAD ou sa clé d’authentification, tout mappage d’index ou un synonyme de recherche Azure qui utilise cette application doit tout d’abord être mis à jour pour utiliser la nouvelle application ID\key **avant** suppression de l’application précédente ou son autorisation de clé et avant de révoquer votre accès au Key Vault à celui-ci.
> Ne parvient pas à faire affichera l’index ou carte de synonymes inutilisable, car il ne pourra pas déchiffrer le contenu accès une fois la clé est perdue.   

## <a name="next-steps"></a>Étapes suivantes

Si vous n’êtes pas familiarisé avec l’architecture de sécurité Azure, passez en revue la [documentation sur la sécurité d’Azure](https://docs.microsoft.com/azure/security/)et en particulier, cet article :

> [!div class="nextstepaction"]
> [Chiffrement des données Azure au repos](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
