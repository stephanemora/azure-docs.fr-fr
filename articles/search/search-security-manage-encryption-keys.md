---
title: Chiffrement des données au repos à l’aide de clés gérées par le client
titleSuffix: Azure Cognitive Search
description: Complétez le chiffrement côté serveur des index et mappages de synonymes dans Recherche cognitive Azure à l’aide de clés que vous créez et gérez dans Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: ed5d1f5b35bc9b6dee234678fa82af95e1d53bc7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553992"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configurer des clés gérées par le client pour le chiffrement des données dans le service Recherche cognitive Azure

Par défaut, le service Recherche cognitive Azure chiffre automatiquement le contenu indexé au repos avec des [clés gérées par le service](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Si vous avez besoin de davantage de protection, vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide de clés que vous créez et gérez dans Azure Key Vault. Cet article vous guide tout au long des étapes de configuration du chiffrement à l’aide de clés gérées par le client (CMK).

Le chiffrement CMK dépend d’[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés de chiffrement. Azure Key Vault vous permet également d’auditer l’utilisation des clés si vous [activez la journalisation](../key-vault/general/logging.md).  

Le chiffrement avec des clés gérées par le client est appliqué à des index individuels ou à des mappages de synonymes lors de la création de ces objets, et n’est pas spécifié au niveau du service de recherche lui-même. Seuls de nouveaux objets peuvent être chiffrés. Vous ne pouvez pas chiffrer un contenu existant.

Toutes les clés ne doivent pas nécessairement se trouver dans le même coffre de clés. Un service de recherche unique peut héberger plusieurs index chiffrés ou mappages de synonymes chiffrés avec leurs propres clés de chiffrement gérées par le client et stockées dans différents coffres de clés. Vous pouvez également avoir des index et des cartes de synonymes dans le même service qui ne sont pas chiffrés à l’aide de clés gérées par le client. 

## <a name="double-encryption"></a>Double chiffrement

Pour les services créés après le 1er août 2020 et dans certaines régions, l’étendue du chiffrement CMK inclut des disques temporaires, ce qui permet un [double chiffrement complet](search-security-overview.md#double-encryption) actuellement disponible dans les régions suivantes : 

+ USA Ouest 2
+ USA Est
+ États-Unis - partie centrale méridionale
+ Gouvernement américain - Virginie
+ Gouvernement des États-Unis – Arizona

Si vous utilisez une autre région ou un service créé avant le 1er août, votre chiffrement CMK est limité uniquement au disque de données, à l’exclusion des disques temporaires que le service utilise.

## <a name="prerequisites"></a>Prérequis

Les services suivants sont utilisés dans cet exemple. 

+ [Créez un service Recherche cognitive Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Créez une ressource Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) ou recherchez un coffre existant dans le même abonnement que le service Recherche Cognitive Azure. Cette fonctionnalité a la même exigence en matière d’abonnement.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) est utilisé pour les tâches de configuration.

+ Vous pouvez utiliser [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) et la [préversion du Kit de développement logiciel (SDK) .NET](https://aka.ms/search-sdk-preview) pour appeler l’API REST qui crée des index et des mappages de synonymes incluant un paramètre de clé de chiffrement. Il n’existe actuellement pas de prise en charge du portail pour l’ajout de clé aux index ou aux mappages de synonymes.

>[!Note]
> En raison de la nature du chiffrement avec des clés gérées par le client, le service Recherche cognitive Azure ne pourra pas récupérer vos données en cas de suppression de votre clé Azure Key Vault. Pour éviter la perte de données résultant de suppressions accidentelles d’Azure Key Vault, vous devez activer la suppression réversible et la protection contre le vidage sur le coffre de clés. La suppression réversible étant activée par défaut, vous ne rencontrerez des problèmes que si vous l’avez désactivée intentionnellement. La par défaut n’est pas activée par défaut, mais elle est requise pour le chiffrement CMK du service Recherche cognitive Azure. Pour plus d’informations, consultez les vues d’ensemble de la [suppression réversible](../key-vault/key-vault-ovw-soft-delete.md) et de la [protection contre le vidage](../key-vault/general/soft-delete-overview.md#purge-protection) .

## <a name="1---enable-key-recovery"></a>1 - Activer la récupération de clé

La **suppression réversible** et la **protection contre le vidage** doivent été activées pour le coffre de clés. Vous pouvez définir ces fonctionnalités à l’aide du portail ou des commandes PowerShell ou Azure CLI suivantes.

### <a name="using-powershell"></a>Utilisation de PowerShell

1. Exécutez `Connect-AzAccount` pour configurer vos informations d’identification Azure.

1. Exécutez la commande suivante pour vous connecter à votre coffre de clés, en remplaçant `<vault_name>` par un nom valide :

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault est créé avec la suppression réversible activée. Si elle est désactivée sur votre coffre, exécutez la commande suivante :

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Activer la protection contre le vidage :

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Enregistrez vos mises à jour :

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Créer une clé

Si vous utilisez une clé existante pour chiffrer le contenu de Recherche cognitive Azure, ignorez cette étape.

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de présentation de votre coffre de clés.

1. Sélectionnez le paramètre **Clés** dans le volet de navigation de gauche, puis cliquez sur **Générer/Importer**.

1. Dans le volet **Créer une clé**, dans la liste **Options**, choisissez la méthode que vous voulez utiliser pour créer une clé. Vous pouvez **Générer** une nouvelle clé, **Charger** une clé existante ou utiliser **Restaurer la sauvegarde** pour sélectionner une sauvegarde d’une clé.

1. Entrez un **nom** pour votre clé et sélectionnez éventuellement d’autres propriétés de clé.

1. Cliquez sur le bouton **Créer** pour démarrer le déploiement.

Notez l’identificateur de la clé : il se compose de l’**URI de la valeur de la clé**, du **nom de la clé** et de la **version de la clé**. Vous en aurez besoin pour définir un index chiffré dans Recherche cognitive Azure.
 
![Créer une clé de coffre de clés](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Créer une clé de coffre de clés")

## <a name="3---create-a-service-identity"></a>3 - Créer une identité de service

L'attribution d'une identité à votre service de recherche vous permet d'accorder des droits d'accès Key Vault à votre service de recherche. Votre service de recherche utilisera son identité pour s'authentifier auprès d’Azure Key Vault.

Recherche cognitive Azure prend en charge deux façons d'attribuer une identité : une identité managée ou une application Azure Active Directory managée en externe. 

Si possible, utilisez une identité managée. C'est le moyen le plus simple d'attribuer une identité à votre service de recherche et le mieux adapté à la plupart des scénarios. Si vous utilisez plusieurs clés pour des index et des mappages de synonymes, ou si votre solution se trouve dans une architecture distribuée qui exclut l'authentification basée sur l'identité, utilisez l'[approche Azure Active Directory managée en externe](#aad-app) avancée, décrite à la fin de cet article.

 En général, une identité managée permet à votre service de recherche de s'authentifier auprès d’Azure Key Vault, sans stocker les informations d'identification dans le code. Le cycle de vie de ce type d'identité managée est lié au cycle de vie de votre service de recherche, qui ne peut avoir qu'une seule identité managée. [En savoir plus sur les identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de présentation de votre service de recherche. 

1. Cliquez sur **Identité** dans le volet de navigation de gauche, définissez son statut sur **On** (Activé), puis cliquez sur **Enregistrer**.

![Activez une identité managée](./media/search-enable-msi/enable-identity-portal.png "Activer une identité managée")

## <a name="4---grant-key-access-permissions"></a>4 - Accorder des autorisations d’accès à la clé

Pour activer votre service de recherche afin d’utiliser votre clé Key Vault, vous devrez accorder à votre service de recherche certaines autorisations d’accès.

Les autorisations d’accès peuvent être révoquées à tout moment. Une fois révoqué, tout index ou mappage de synonymes d’un service de recherche qui utilise ce coffre de clés sera inutilisable. Une restauration ultérieure des autorisations d’accès au coffre de clés restaurera l’accès à l’index ou au mappage de synonymes. Pour plus d’informations, consultez [Accès sécurisé à un coffre de clés](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de présentation de votre coffre de clés. 

1. Sélectionnez le paramètre **Stratégies d’accès** dans le volet de navigation à gauche, puis cliquez sur **+ Ajouter nouveau**.

   ![Ajouter une stratégie d’accès au coffre de clés](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Ajouter une stratégie d’accès au coffre de clés")

1. Cliquez sur **Sélectionner le principal** puis choisissez votre service Recherche cognitive Azure. Vous pouvez le rechercher par nom ou par l’ID d’objet affiché après avoir activé l’identité managée.

   ![Sélectionnez le principal de la stratégie d'accès au coffre de clés](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Sélectionnez le principal de la stratégie d'accès au coffre de clés")

1. Cliquez sur **Autorisations de clé**, puis sélectionnez *Obtenir*, *Ne pas inclure la clé* et *Inclure la clé*. Vous pouvez utiliser le modèle *Azure Data Lake Storage ou Azure Storage* pour sélectionner rapidement les autorisations requises.

   Recherche cognitive Azure doit obtenir les [autorisations d’accès](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations) suivantes :

   * *Obtenir* : permet à votre service de recherche de récupérer les parties publiques de votre clé dans un coffre de clés
   * *Inclure la clé* : permet à votre service de recherche d’utiliser votre clé pour protéger la clé de chiffrement interne
   * *Ne pas inclure la clé* : permet à votre service de recherche d’utiliser votre clé pour désencapsuler la clé de chiffrement interne

   ![Sélectionner les autorisations de clé pour la stratégie d’accès au coffre de clés](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Sélectionner les autorisations de clé pour la stratégie d’accès au coffre de clés")

1. Dans **Autorisations du secret**, sélectionnez *Get*.

1. Pour **Autorisations de certificat**, sélectionnez *Get*.

1. Cliquez sur **OK** puis sur **Enregistrer** pour enregistrer les changements de stratégie d'accès.

> [!Important]
> Le contenu chiffré dans Recherche cognitive Azure est configuré pour utiliser une clé Azure Key Vault spécifique avec une **version** spécifique. Si vous modifiez la clé ou la version, l’index ou le mappage de synonymes doit être mis à jour pour utiliser la nouvelle clé\version **avant** de supprimer la clé\version précédente. Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et vous ne pourrez pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.   

## <a name="5---encrypt-content"></a>5 - Chiffrer le contenu

Pour ajouter une clé gérée par le client sur un index ou un mappage de synonymes, vous devez utiliser l’[API REST Recherche](https://docs.microsoft.com/rest/api/searchservice/) ou un Kit de développement logiciel (SDK). Le portail n’expose pas les mappages de synonymes ou les propriétés de chiffrement. Lorsque vous utilisez une API valide, les index et les mappages de synonymes prennent en charge une propriété **encryptionKey** de niveau supérieur. 

En utilisant l’**URI du coffre de clés**, le **nom de la clé** et la **version de la clé** de votre clé de coffre de clés, nous pouvons créer une définition **encryptionKey** comme suit :

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

## <a name="example-index-encryption"></a>Exemple : Chiffrement d’index
Les détails de la création d'un nouvel index via l'API REST se trouvent dans la section [Créer un index (API REST du service Recherche cognitive Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index), où la seule différence ici consiste à spécifier les détails de la clé de chiffrement dans la définition de l'index : 

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

## <a name="example-synonym-map-encryption"></a>Exemple : Chiffrement de mappage de synonymes

Les détails de la création d'un nouveau mappage de synonymes via l'API REST se trouvent dans la section [Créer un mappage de synonymes (API REST du service Recherche cognitive Azure)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), où la seule différence ici consiste à spécifier les détails de la clé de chiffrement dans la définition du mappage de synonymes : 

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
> Même si la propriété **encryptionKey** peut être ajoutée aux index ou mappages de synonymes Recherche cognitive Azure existants, elle peut être mise à jour en fournissant des valeurs différentes pour chacun des trois informations du coffre de clés (par exemple, en mettant à jour la version de la clé). Lorsque vous passez à une nouvelle clé Key Vault ou à une nouvelle version de clé, tout index ou mappage de synonymes Recherche cognitive Azure qui utilise la clé doit d'abord être mis à jour pour utiliser la nouvelle clé **avant** de supprimer la clé\version précédente. Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et il ne pourra pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.   
> Une restauration ultérieure des autorisations d’accès au coffre de clés restaurera l’accès au contenu.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> Avancé : Utiliser une application Azure Active Directory managée en externe

Lorsqu'une identité managée n'est pas possible, vous pouvez créer une application Azure Active Directory avec un principal de sécurité pour votre service Recherche cognitive Azure. Plus précisément, une identité managée n'est pas viable dans ces conditions :

* Vous ne pouvez pas accorder directement à votre service de recherche les droits d'accès au coffre de clés (par exemple, si le service de recherche se trouve dans un autre locataire Active Directory qu’Azure Key Vault).

* Un seul service de recherche est nécessaire pour héberger plusieurs index/mappages de synonymes chiffrés, chacun utilisant une clé différente d'un coffre de clés différent, où chaque coffre de clés doit utiliser **une identité différente**  pour l'authentification. Si l'utilisation d'une identité différente pour gérer différents coffres de clés n'est pas une exigence, vous pouvez utiliser l'option d'identité managée ci-dessus.  

Pour s'adapter à de telles topologies, Recherche cognitive Azure prend en charge la recherche à l'aide d’applications Azure Active Directory (AAD) pour l'authentification entre votre service de recherche et Key Vault.    
Pour créer une application AAD dans le portail :

1. [Créez une application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Obtenez l'ID de l'application et la clé d'authentification](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) car ces informations seront nécessaires pour créer un index chiffré. Vous devrez également fournir l’**ID d’application** et la **clé d’authentification**.

>[!Important]
> Lorsque vous décidez d'utiliser une application AAD d'authentification au lieu d'une identité managée, considérez le fait que Recherche cognitive Azure n'est pas autorisé à gérer votre application AAD en votre nom, et c'est à vous de gérer votre application AAD, par exemple la rotation périodique de la clé d'authentification de l'application.
> Lors du changement d'une application AAD ou de sa clé d'authentification, tout index ou mappage de synonymes Recherche cognitive Azure qui utilise cette application doit d'abord être mis à jour pour utiliser le nouvel ID d'application **avant** de supprimer l'application précédente ou sa clé d'autorisation, et avant de révoquer votre accès Key Vault.
> Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et il ne pourra pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.

## <a name="work-with-encrypted-content"></a>Utiliser des colonnes chiffrées

Avec le chiffrement CMK, vous remarquerez une latence pour l’indexation et les requêtes en raison du travail de chiffrement/déchiffrement supplémentaire. Le service Recherche cognitive Azure ne journalise pas l’activité de chiffrement, mais vous pouvez surveiller l’accès aux clés par le biais de la journalisation du coffre de clés. Nous vous recommandons d’[activer la journalisation](../key-vault/general/logging.md) dans le cadre de la configuration du coffre de clés.

Une rotation de clés est supposée se produire au fil du temps. Chaque fois que vous voulez opérer une rotation de clés, il est important de suivre cette séquence :

1. [Déterminez la clé utilisée par un index ou un mappage de synonyme](search-security-get-encryption-keys.md).
1. [Créez une clé dans le coffre de clés](../key-vault/keys/quick-create-portal.md), mais gardez la clé d’origine disponible.
1. [Mettez à jour les propriétés encryptionKey](https://docs.microsoft.com/rest/api/searchservice/update-index) sur un index ou un mappage de synonymes pour utiliser les nouvelles valeurs. Seuls des objets créés à l’origine avec cette propriété peuvent être mis à jour pour utiliser une autre valeur.
1. Désactivez ou supprimez la clé précédente dans le coffre de clés. Surveillez l’accès à la clé pour vérifier que la nouvelle clé est utilisée.

Pour des raisons de performances, le service de recherche met en cache la clé pendant plusieurs heures. Si vous désactivez ou supprimez la clé sans en fournir de nouvelle, les requêtes continuent de fonctionner sur une base temporaire jusqu’à ce que le cache expire. Toutefois, une fois que le service de recherche ne peut pas déchiffrer le contenu, vous obtenez le message suivant : « Accès interdit. La clé de requête utilisée a peut-être été révoquée. Réessayez. » 

## <a name="next-steps"></a>Étapes suivantes

Si vous n’êtes pas familiarisé avec l’architecture de sécurité Azure, passez en revue la [documentation sur la sécurité Azure](https://docs.microsoft.com/azure/security/), et en particulier cet article :

> [!div class="nextstepaction"]
> [Chiffrement des données Azure au repos](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
