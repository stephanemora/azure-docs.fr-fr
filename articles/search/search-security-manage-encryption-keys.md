---
title: Chiffrement des données au repos à l’aide de clés gérées par le client dans Azure Key Vault (préversion) - Recherche Azure
description: Complétez le chiffrement côté serveur des index et mappages de synonymes dans Recherche Azure grâce à des clés que vous créez et gérez dans Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 567f32cba76aaf2d1657b2476c4d11596d44dec5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753959"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Chiffrement Recherche Azure à l’aide de clés gérées par le client dans Azure Key Vault

> [!Note]
> Le chiffrement avec des clés gérées par le client est en préversion et n’a pas été conçu pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Vous pouvez également utiliser la version 8.0-preview du SDK .NET.
>
> Cette fonctionnalité n’est pas disponible pour les services gratuits. Vous devez utiliser un service de recherche facturable, créé à partir du 01-01-2019. Il n’existe aucune prise en charge sur le portail pour l’instant.

Par défaut, Recherche Azure chiffre au repos le contenu de l’utilisateur avec des [clés gérées par le service](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide de clés que vous créez et gérez dans Azure Key Vault. Cet article vous guide tout au long des étapes.

Le chiffrement côté serveur est pris en charge via l'intégration à [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés de chiffrement. Avec Azure Key Vault, vous pouvez également auditer l’utilisation des clés. 

Le chiffrement avec des clés gérées par le client est configuré au niveau de l'index ou du mappage de synonymes lorsque ces objets sont créés, et non au niveau du service de recherche. Vous ne pouvez pas chiffrer un contenu existant. 

Vous pouvez utiliser différentes clés provenant de différents coffres de clé. Cela signifie qu'un même service de recherche peut héberger plusieurs index/mappages de synonymes chiffrés, chaque élément pouvant être chiffré à l'aide d'une clé différente gérée par le client, ainsi que des index/ mappages de synonymes non chiffrés à l'aide de clés gérées par le client. 

## <a name="prerequisites"></a>Prérequis

Les services suivants sont utilisés dans cet exemple. 

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce tutoriel.

+ [Créez une ressource Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou recherchez un coffre existant dans votre abonnement.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) est utilisé pour les tâches de configuration.

+ [Postman](search-fiddler.md), [Azure PowerShell](search-create-index-rest-api.md) et le [SDK Recherche Azure](https://aka.ms/search-sdk-preview) permettent d’appeler la préversion de l’API REST. Il n’existe pour l’instant aucun portail ou prise en charge du SDK .NET pour le chiffrement géré par le client.

## <a name="1---enable-key-recovery"></a>1 - Activer la récupération de clé

Cette étape est facultative mais fortement recommandée. Après avoir créé la ressource Azure Key Vault, activez la **suppression réversible** et la **protection contre le vidage** dans le coffre de clés sélectionné en exécutant les commandes PowerShell ou Azure CLI suivantes :   

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
> En raison de la nature même du chiffrement avec des clés gérées par le client, Recherche Azure ne pourra pas récupérer vos données si votre clé de coffre Azure est supprimée. Pour éviter toute perte de données causée par des suppressions accidentelles de clés du coffre, il est fortement recommandé d'activer la suppression réversible et la protection contre le vidage dans le coffre de clés sélectionné. Pour plus d'informations, consultez [Suppression réversible d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 - Créer une clé

Si vous utilisez une clé existante pour chiffrer le contenu Recherche Azure, ignorez cette étape.

1. [Connectez-vous au portail Azure](https://portal.azure.com) et accédez au tableau de bord do coffre de clés.

1. Sélectionnez le paramètre **Clés** dans le volet de navigation de gauche, puis cliquez sur **Générer/Importer**.

1. Dans le volet **Créer une clé**, dans la liste **Options**, choisissez la méthode que vous voulez utiliser pour créer une clé. Vous pouvez **Générer** une nouvelle clé, **Charger** une clé existante ou utiliser **Restaurer la sauvegarde** pour sélectionner une sauvegarde d’une clé.

1. Entrez un **nom** pour votre clé et sélectionnez éventuellement d’autres propriétés de clé.

1. Cliquez sur le bouton **Créer** pour démarrer le déploiement.

Notez l’identificateur de la clé : il se compose de l’**URI de la valeur de la clé**, du **nom de la clé** et de la **version de la clé**. Vous en aurez besoin pour définir un index chiffré dans Recherche Azure.
 
![Créer une clé de coffre](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Créer une clé de coffre")

## <a name="3---create-a-service-identity"></a>3 - Créer une identité de service

L'attribution d'une identité à votre service de recherche vous permet d'accorder des droits d'accès Key Vault à votre service de recherche. Votre service de recherche utilisera son identité pour s'authentifier auprès d’Azure Key Vault.

Recherche Azure prend en charge deux façons d'attribuer une identité : une identité managée ou une application Azure Active Directory managée en externe. 

Si possible, utilisez une identité managée. C'est le moyen le plus simple d'attribuer une identité à votre service de recherche et le mieux adapté à la plupart des scénarios. Si vous utilisez plusieurs clés pour des index et des mappages de synonymes, ou si votre solution se trouve dans une architecture distribuée qui exclut l'authentification basée sur l'identité, utilisez l'[approche Azure Active Directory managée en externe](#aad-app) avancée, décrite à la fin de cet article.

 En général, une identité managée permet à votre service de recherche de s'authentifier auprès d’Azure Key Vault, sans stocker les informations d'identification dans le code. Le cycle de vie de ce type d'identité managée est lié au cycle de vie de votre service de recherche, qui ne peut avoir qu'une seule identité managée. [En savoir plus sur les identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Pour créer une identité managée, [connectez-vous au portail Azure](https://portal.azure.com) et ouvrez votre tableau de bord du service de recherche. 

1. Cliquez sur **Identité** dans le volet de navigation de gauche, définissez son statut sur **On** (Activé), puis cliquez sur **Enregistrer**.

![Activer une identité managée](./media/search-enable-msi/enable-identity-portal.png "Activer une identité managée")

## <a name="4---grant-key-access-permissions"></a>4 - Accorder des autorisations d’accès à la clé

Pour activer votre service de recherche afin d’utiliser votre clé Key Vault, vous devrez accorder à votre service de recherche certaines autorisations d’accès.

Les autorisations d’accès peuvent être révoquées à tout moment. Une fois révoqué, tout index ou mappage de synonymes d’un service de recherche qui utilise ce coffre de clés sera inutilisable. Une restauration ultérieure des autorisations d’accès au coffre de clés restaurera l’accès à l’index ou au mappage de synonymes. Pour plus d’informations, consultez [Accès sécurisé à un coffre de clés](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de présentation de votre coffre de clés. 

1. Sélectionnez le paramètre **Stratégies d’accès** dans le volet de navigation de gauche, puis cliquez sur **+Ajouter un nouveau**.

   ![Ajouter une nouvelle stratégie d’accès au coffre de clés](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Ajouter une nouvelle stratégie d’accès au coffre de clés")

1. Cliquez sur **Sélectionner le principal** puis choisissez votre service Recherche Azure. Vous pouvez le rechercher par nom ou par l’ID d’objet affiché après avoir activé l’identité managée.

   ![Sélectionner le principal de la stratégie d'accès au coffre de clés](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Sélectionner le principal de la stratégie d'accès au coffre de clés")

1. Cliquez sur **Autorisations de clé**, puis sélectionnez *Obtenir*, *Ne pas inclure la clé* et *Inclure la clé*. Vous pouvez utiliser le modèle *Azure Data Lake Storage ou Azure Storage* pour sélectionner rapidement les autorisations requises.

   Recherche Azure doit obtenir les [autorisations d’accès](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations) suivantes :

   * *Obtenir* : permet à votre service de recherche de récupérer les parties publiques de votre clé dans un coffre de clés
   * *Inclure la clé* : permet à votre service de recherche d’utiliser votre clé pour protéger la clé de chiffrement interne
   * *Ne pas inclure la clé* : permet à votre service de recherche d’utiliser votre clé pour désencapsuler la clé de chiffrement interne

   ![Sélectionner les autorisations d’accès au coffre de clés](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Sélectionner les autorisations d’accès au coffre de clés")

1. Cliquez sur **OK** puis sur **Enregistrer** pour enregistrer les changements de stratégie d'accès.

> [!Important]
> Le contenu chiffré dans Recherche Azure est configuré pour utiliser une clé Azure Key Vault spécifique avec une **version** spécifique. Si vous modifiez la clé ou la version, l’index ou le mappage de synonymes doit être mis à jour pour utiliser la nouvelle clé\version **avant** de supprimer la clé\version précédente. Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et vous ne pourrez pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.   

## <a name="5---encrypt-content"></a>5 - Chiffrer le contenu

La création d'un index ou d'un mappage de synonymes chiffré avec une clé gérée par le client n'est pas encore possible avec le portail Azure. Utilisez l’API REST Recherche Azure pour créer un tel index ou mappage de synonymes.

L'index et le mappage de synonymes prennent en charge une nouvelle propriété de niveau supérieur, **encryptionKey**, utilisée pour spécifier la clé. 

En utilisant l’**URI du coffre de clés**, le **nom de la clé** et la **version de la clé** de votre clé Key Vault, nous pouvons créer une définition **encryptionKey** :

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
> Aucune de ces informations sur le coffre de clés n'est considérée comme secrète et peut être facilement récupérée en accédant à la page de la clé Azure Key Vault appropriée dans le portail Azure.

Si vous utilisez une application AAD pour l’authentification Key Vault au lieu d'utiliser une identité managée, ajoutez à votre clé de chiffrement les **informations d'identification d’accès** de l’application AAD : 
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

## <a name="example-index-encryption"></a>Exemple : Chiffrement d’index
Les détails de la création d'un nouvel index via l'API REST se trouvent dans la section [Créer un index (API REST du service Recherche Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index), où la seule différence ici consiste à spécifier les détails de la clé de chiffrement dans la définition de l'index : 

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
Vous pouvez maintenant envoyer la demande de création d'un index, puis commencer à utiliser l'index normalement.

## <a name="example-synonym-map-encryption"></a>Exemple : Chiffrement de mappage de synonymes

Les détails de la création d'un nouveau mappage de synonymes via l'API REST se trouvent dans la section [Créer un mappage de synonymes (API REST du service Recherche Azure)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), où la seule différence ici consiste à spécifier les détails de la clé de chiffrement dans la définition du mappage de synonymes : 

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
Vous pouvez maintenant envoyer la demande de création d’un mappage de synonymes, puis commencer à utiliser l'index normalement.

>[!Important] 
> Même si la propriété **encryptionKey** peut être ajoutée aux index ou mappages de synonymes Recherche Azure existants, elle peut être mise à jour en fournissant des valeurs différentes pour chacun des trois informations du coffre de clés (par exemple, en mettant à jour la version de la clé). Lorsque vous passez à une nouvelle clé Key Vault ou à une nouvelle version de clé, tout index ou mappage de synonymes Recherche Azure qui utilise la clé doit d'abord être mis à jour pour utiliser la nouvelle clé **avant** de supprimer la clé\version précédente. Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et il ne pourra pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.   
> Une restauration ultérieure des autorisations d’accès au coffre de clés restaurera l’accès au contenu.

## <a name="aad-app"></a> Avancé : Utiliser une application Azure Active Directory managée en externe

Lorsqu'une identité managée n'est pas possible, vous pouvez créer une application Azure Active Directory avec un principal de sécurité pour votre service Recherche Azure. Plus précisément, une identité managée n'est pas viable dans ces conditions :

* Vous ne pouvez pas accorder directement à votre service de recherche les droits d'accès au coffre de clés (par exemple, si le service de recherche se trouve dans un autre locataire Active Directory qu’Azure Key Vault).

* Un seul service de recherche est nécessaire pour héberger plusieurs index/mappages de synonymes chiffrés, chacun utilisant une clé différente d'un coffre de clés différent, où chaque coffre de clés doit utiliser **une identité différente**  pour l'authentification. Si l'utilisation d'une identité différente pour gérer différents coffres de clés n'est pas une exigence, vous pouvez utiliser l'option d'identité managée ci-dessus.  

Pour s'adapter à de telles topologies, Azure prend en charge la recherche à l'aide d’applications Azure Active Directory (AAD) pour l'authentification entre votre service de recherche et Key Vault.    
Pour créer une application AAD dans le portail :

1. [Créez une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenez l'ID de l'application et la clé d'authentification](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) car ces informations seront nécessaires pour créer un index chiffré. Vous devrez également fournir l’**ID d’application** et la **clé d’authentification**.

>[!Important]
> Lorsque vous décidez d'utiliser une application AAD d'authentification au lieu d'une identité managée, considérez le fait que Recherche Azure n'est pas autorisé à gérer votre application AAD en votre nom, et c'est à vous de gérer votre application AAD, par exemple la rotation périodique de la clé d'authentification de l'application.
> Lors du changement d'une application AAD ou de sa clé d'authentification, tout index ou mappage de synonymes Recherche Azure qui utilise cette application doit d'abord être mis à jour pour utiliser le nouvel ID d'application **avant** de supprimer l'application précédente ou sa clé d'autorisation, et avant de révoquer votre accès Key Vault.
> Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et il ne pourra pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.   

## <a name="next-steps"></a>Étapes suivantes

Si vous n’êtes pas familiarisé avec l’architecture de sécurité Azure, passez en revue la [documentation sur la sécurité Azure](https://docs.microsoft.com/azure/security/), et en particulier cet article :

> [!div class="nextstepaction"]
> [Chiffrement des données Azure au repos](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
