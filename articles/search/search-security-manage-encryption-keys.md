---
title: Chiffrer des données avec des clés gérées par le client
titleSuffix: Azure Cognitive Search
description: Complétez le chiffrement côté serveur des index et mappages de synonymes dans Recherche cognitive Azure à l’aide de clés que vous créez et gérez dans Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 9dc1244c6747ea5616bee59ccf9b972a89d0c4a0
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042179"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configurer des clés gérées par le client pour le chiffrement des données dans le service Recherche cognitive Azure

Par défaut, le service Recherche cognitive Azure chiffre automatiquement le contenu avec des [clés gérées par le service](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Si vous avez besoin de davantage de protection, vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide de clés que vous créez et gérez dans Azure Key Vault. Les objets qui peuvent être chiffrés incluent des index, des listes de synonymes, des indexeurs, des sources de données et des ensembles de compétences.

Cet article vous guide tout au long des étapes de configuration du chiffrement à l’aide de clés gérées par le client (CMK). Voici quelques points à retenir :

+ Le chiffrement à l’aide de clés gérées par le client dépend d’[Azure Key Vault](../key-vault/general/overview.md). Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés de chiffrement.

+ Le chiffrement avec les clés gérées par le client est activé lors de la création d’objets, en fonction de l’objet. Vous ne pouvez pas chiffrer un contenu existant.

Le chiffrement étant coûteux à déchiffrer en termes de ressources informatiques, seul le contenu sensible est chiffré. Cela comprend tout le contenu des index et des listes de synonymes. Pour les indexeurs, les sources de données et les ensembles de compétences, seuls les champs qui stockent les chaînes de connexion, les descriptions, les clés et les entrées utilisateur sont chiffrés. Par exemple, les ensembles de compétences ont des clés Cognitive Services et certaines compétences acceptent les entrées utilisateur, telles que les entités personnalisées. Les clés et les entrées utilisateur dans les compétences sont chiffrées.

## <a name="double-encryption"></a>Double chiffrement

Le double chiffrement est une extension du chiffrement des clés gérées par le client (CMK). Le chiffrement CMK s’applique au stockage à long terme qui est écrit sur un disque de données. Le terme *chiffrement double* fait référence au chiffrement supplémentaire du stockage à faible terme (du contenu écrit sur les disques temporaires). Aucune configuration n’est requise. Lorsque vous appliquez des CMK à des objets, le chiffrement double est appelé automatiquement.

Bien que le chiffrement double soit disponible dans toutes les régions, la prise en charge a été déployée en deux phases. Le premier déploiement était en août 2020 et incluait les cinq régions répertoriées ci-dessous. Le deuxième déploiement en mai 2021 un double chiffrement étendu à toutes les régions restantes. Si vous utilisez CMK sur un service plus ancien et souhaitez un double chiffrement, vous devrez créer un nouveau service de recherche dans la région de votre choix.

| Région | Date de création de service |
|--------|-----------------------|
| USA Ouest 2 | Après le 1er août 2020 |
| USA Est | Après le 1er août 2020 |
| États-Unis - partie centrale méridionale  | Après le 1er août 2020 |
| Gouvernement américain - Virginie  | Après le 1er août 2020 |
| Gouvernement des États-Unis – Arizona  | Après le 1er août 2020 |
| [Toutes les autres régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=search#select-product) | Après le 13 mai 2021 |

## <a name="prerequisites"></a>Prérequis

Les outils et services suivants sont utilisés dans ce scénario.

+ [Recherche cognitive Azure](search-create-service-portal.md) sur un [niveau facturable](search-sku-tier.md#tier-descriptions) (de base ou version ultérieure, dans n’importe quelle région).
+ [Azure Key Vault](../key-vault/general/overview.md), vous pouvez créer un coffre de clés Azure à l’aide du [portail Azure](../key-vault//general/quick-create-portal.md), d’[Azure CLI](../key-vault//general/quick-create-cli.md) ou d’[Azure PowerShell](../key-vault//general/quick-create-powershell.md). dans le même abonnement que Recherche cognitive Azure. La **suppression réversible** et la **protection contre le vidage** doivent été activées pour le coffre de clés.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Si vous n’en avez pas, [configurez un nouveau locataire](../active-directory/develop/quickstart-create-new-tenant.md).

Vous devez disposer d’une application de recherche capable de créer l’objet chiffré. Dans ce code, vous allez référencer une clé de coffre de clés et des informations d’inscription auprès d’Active Directory. Ce code peut être une application fonctionnelle ou un code prototype tel que l’[exemple de code C# DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> Vous pouvez utiliser [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) ou [Azure PowerShell](search-get-started-powershell.md) pour appeler les API REST qui créent des index et des mappages de synonymes incluant un paramètre de clé de chiffrement. Vous pouvez également utiliser des SDK Azure. Il n’existe pas de prise en charge du portail pour l’ajout de clé aux index ou aux mappages de synonymes.

## <a name="key-vault-tips"></a>Conseils Key Vault

Si vous débutez avec Azure Key Vault, consultez ce guide de démarrage rapide pour en savoir plus sur les tâches de base : [définir et récupérer un secret à partir de Azure Key Vault à l’aide de PowerShell\](../key-vault/secrets/quick-create-powershell.md). Voici quelques conseils pour l’utilisation de Key Vault :

+ Utilisez autant de coffres de clés que vous le souhaitez. Les clés gérées peuvent se trouver dans différents coffres de clés. Un service de recherche peut posséder plusieurs objets chiffrés, chacun chiffré avec des clés de chiffrement gérées par le client et stockées dans différents coffres de clés.

+ [Activez la journalisation](../key-vault/general/logging.md) sur Key Vault afin de pouvoir surveiller l’utilisation de clé.

+ Veillez à suivre des procédures strictes lors de la rotation régulière des clés de coffre de clés et des secrets et inscriptions d’application Active Directory. Mettez toujours à jour tout le [contenu chiffré](search-security-get-encryption-keys.md) pour utiliser les nouveaux secrets et les nouvelles clés avant de supprimer les anciens et les anciennes. Si vous omettez cette étape, votre contenu ne peut pas être déchiffré.

## <a name="1---enable-purge-protection"></a>1 - Activer la protection contre le vidage :

En tant que première étape, assurez-vous que le [soft-delete](../key-vault/general/soft-delete-overview.md) et la [protection contre les purges](../key-vault/general/soft-delete-overview.md#purge-protection) sont activés dans le coffre de clés. En raison de la nature du chiffrement avec des clés gérées par le client, personne ne peut récupérer vos données en cas de suppression de votre clé Azure Key Vault. 

Pour éviter la perte de données résultant de suppressions accidentelles d’Azure Key Vault, vous devez activer la suppression réversible et la protection contre le vidage sur le coffre de clés. La suppression réversible étant activée par défaut, vous ne rencontrerez des problèmes que si vous l’avez désactivée intentionnellement. Par défaut, la protection contre le vidage n’est pas activée, mais elle est requise pour le chiffrement à clé gérée par le client dans Recherche cognitive. 

Vous pouvez définir les deux propriétés à l’aide du portail ou en utilisant des commandes PowerShell ou Azure CLI.

### <a name="using-azure-portal"></a>En passant par le portail Azure

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de présentation de votre coffre de clés.

1. Sur la page **Vue d’ensemble**, sous **Essentials**, activez les options **Suppression réversible** et **Protection contre le vidage**.

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

+ Si [Azure CLI est installé](/cli/azure/install-azure-cli), vous pouvez exécuter la commande suivante pour activer les propriétés requises.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 – Créer une clé dans Key Vault

Ignorez la génération de clé si vous avez déjà une clé dans Azure Key Vault que vous souhaitez utiliser, mais collectez l’identificateur de clé. Vous aurez besoin de ces informations lors de la création d’un objet chiffré.

1. [Connectez-vous au portail Azure](https://portal.azure.com) et ouvrez la page de présentation de votre coffre de clés.

1. Sélectionnez **Clés** sur la gauche, puis sélectionnez **+ Générer/importer**.

1. Dans le volet **Créer une clé**, dans la liste **Options**, choisissez la méthode que vous voulez utiliser pour créer une clé. Vous pouvez **Générer** une nouvelle clé, **Charger** une clé existante ou utiliser **Restaurer la sauvegarde** pour sélectionner une sauvegarde d’une clé.

1. Entrez un **nom** pour votre clé et sélectionnez éventuellement d’autres propriétés de clé.

1. Sélectionnez **Créer** pour démarrer le déploiement.

1. Notez l’identificateur de la clé : il se compose de l’**URI de la valeur de la clé**, du **nom de la clé** et de la **version de la clé**. Vous en aurez besoin pour définir un index chiffré dans Recherche cognitive Azure.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Créer une clé de coffre de clés":::

## <a name="3---register-an-app"></a>3 - Inscrire une application

1. Dans [Portail Azure](https://portal.azure.com), recherchez la ressource Azure Active Directory pour votre abonnement.

1. Sur la gauche, sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez **Nouvelle inscription**.

1. Donnez un nom à l’inscription, peut-être un nom similaire au nom de l’application de recherche. Sélectionnez **Inscription**.

1. Une fois l’inscription de l’application créée, copiez l’ID d’application. Vous devrez fournir cette chaîne à votre application. 

   Si vous utilisez le code [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), collez cette valeur dans le fichier **appsettings.json**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID d’application dans la section Essentials":::

1. Ensuite, sélectionnez **Certificats et secrets** sur la gauche.

1. Sélectionnez **Nouveau secret client**. Attribuez un nom d’affichage au secret, puis sélectionnez **Ajouter**.

1. Copiez le secret de l’application. Si vous utilisez l’exemple, collez cette valeur dans le fichier **appsettings.json**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Secret de l’application":::

## <a name="4---grant-permissions"></a>4 - Accorder des autorisations

Dans cette étape, vous allez créer une stratégie d’accès dans Key Vault. Cette stratégie donne à l’application que vous avez inscrite auprès d’Active Directory l’autorisation d’utiliser votre clé gérée par le client.

Les autorisations d’accès peuvent être révoquées à tout moment. Une fois révoqué, tout index ou mappage de synonymes d’un service de recherche qui utilise ce coffre de clés sera inutilisable. Une restauration ultérieure des autorisations d’accès au coffre de clés restaurera l’accès à l’index ou au mappage de synonymes. Pour plus d’informations, consultez [Accès sécurisé à un coffre de clés](../key-vault/general/security-features.md).

1. Connectez-vous au portail Azure et ouvrez la page **Vue d’ensemble** de votre coffre de clés. 

1. Sur la gauche, sélectionnez **Stratégies d’accès**, puis **+ Ajouter une stratégie d’accès**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Ajouter une stratégie d’accès au coffre de clés":::

1. Choisissez **Sélectionner le principal** et sélectionnez l’application que vous avez inscrite auprès d’Active Directory. Vous pouvez effectuer une recherche par nom.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Sélectionnez le principal de la stratégie d'accès au coffre de clés":::

1. Dans **Autorisations de clé**, choisissez *Obtenir*, *Désenvelopper la clé* et *Envelopper la clé*.

1. Dans **Autorisations du secret**, sélectionnez *Obtenir*.

1. Dans **Autorisations de certificat**, sélectionnez *Obtenir*.

1. Sélectionnez **Ajouter** puis **Enregistrer**.

> [!Important]
> Le contenu chiffré dans Recherche cognitive Azure est configuré pour utiliser une clé Azure Key Vault spécifique avec une **version** spécifique. Si vous modifiez la clé ou la version, l’index ou le mappage de synonymes doit être mis à jour pour utiliser la nouvelle clé\version **avant** de supprimer la clé\version précédente. Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et vous ne pourrez pas déchiffrer le contenu une fois que l'accès aux clés sera perdu.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - Chiffrer le contenu

Pour ajouter une clé gérée par le client sur un index, une carte de synonymes, un indexeur, une source de données ou des ensembles de compétences, utilisez l'[API REST de recherche](/rest/api/searchservice/) ou un kit de développement logiciel (SDK) Azure pour créer un objet pour lequel le chiffrement est activé. Le portail n’expose pas les mappages de synonymes ou les propriétés de chiffrement. 

1. Appelez les API Create pour spécifier la propriété **encryptionKey** :

   + [Création d'index](/rest/api/searchservice/create-index)
   + [Créer des cartes de synonymes](/rest/api/searchservice/create-synonym-map)
   + [Create Indexer](/rest/api/searchservice/create-indexer)
   + [Créer une source de données](/rest/api/searchservice/create-data-source)
   + [Créer un ensemble de compétences](/rest/api/searchservice/create-skillset)

1. Insérez la construction encryptionKey dans la définition de l’objet. Cette propriété est une propriété de premier niveau, sur le même niveau que le nom et la description. Les [exemples ci-dessous](#rest-examples) montrent le positionnement des propriétés. Si vous utilisez le même coffre de clés, la même clé et la même version, vous pouvez coller la même construction encryptionKey dans chaque objet pour lequel vous activez le chiffrement.

   L’exemple JSON suivant illustre une encryptionKey, avec des valeurs d’espace réservé pour Azure Key Vault et l’inscription de l’application dans Azure Active Directory :

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

Une fois que vous avez créé l’objet chiffré sur le service de recherche, vous pouvez l’utiliser comme n’importe quel autre objet de ce type. Le chiffrement est transparent pour l’utilisateur et le développeur.

> [!Note]
> Aucune de ces informations sur le coffre de clés n'est considérée comme secrète et peut être facilement récupérée en accédant à la page de la clé Azure Key Vault appropriée dans le portail Azure.

## <a name="rest-examples"></a>Exemples REST

Cette section montre le code JSON pour plusieurs objets afin que vous puissiez voir où trouver `encryptionKey` dans une définition d’objet.

### <a name="index-encryption"></a>Chiffrement d’index

Les détails de la création d’un nouvel index via l’API REST se trouvent dans la section [Créer un index (API REST)](/rest/api/searchservice/create-index), où la seule différence ici consiste à spécifier les détails de la clé de chiffrement dans la définition de l’index :

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
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true}
 ],
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

Vous pouvez maintenant envoyer la demande de création d'un index, puis commencer à utiliser l'index normalement.

### <a name="synonym-map-encryption"></a>Chiffrement de mappage de synonymes

Créez une carte de synonymes chiffrée en procédant de la manière décrite dans [Créer un mappage de synonymes (API REST du service Recherche cognitive Azure)](/rest/api/searchservice/create-synonym-map). Utilisez la propriété `encryptionKey` pour spécifier la clé de chiffrement à utiliser.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
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

Vous pouvez maintenant envoyer la demande de création d’un mappage de synonymes, puis commencer à utiliser l'index normalement.

### <a name="data-source-encryption"></a>Chiffrement de source de données

Créez une source de données chiffrée en utilisant la section [Créer une source de données (API REST)](/rest/api/searchservice/create-data-source). Utilisez la propriété `encryptionKey` pour spécifier la clé de chiffrement à utiliser.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
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

Vous pouvez maintenant envoyer la demande de création de la source de données, puis commencer à utiliser celle-ci normalement.

### <a name="skillset-encryption"></a>Chiffrement d’ensemble de compétences

Créez un ensemble de compétences chiffré en procédant de la manière décrite dans [Créer une API REST Skillset](/rest/api/searchservice/create-skillset). Utilisez la propriété `encryptionKey` pour spécifier la clé de chiffrement à utiliser.

```json
{
    "name": "skillset1",
    "skills":  [ omitted for brevity ],
    "cognitiveServices": { omitted for brevity },
      "knowledgeStore":  { omitted for brevity  },
    "encryptionKey": (optional) { 
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "accessCredentials": {
            "applicationId": "00000000-0000-0000-0000-000000000000",
            "applicationSecret": "myApplicationSecret"}
    }
}
```

Vous pouvez maintenant envoyer la demande de création de l’ensemble de compétences, puis commencer à utiliser celui-ci normalement.

### <a name="indexer-encryption"></a>Chiffrement d’indexeur

Créez un indexeur chiffré en procédant de la manière décrite dans [Créer une API REST Indexer](/rest/api/searchservice/create-indexer). Utilisez la propriété `encryptionKey` pour spécifier la clé de chiffrement à utiliser.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
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

Vous pouvez maintenant envoyer la demande de création de l’indexeur, puis commencer à utiliser celui-ci normalement.

>[!Important]
> Même si la propriété `encryptionKey` peut être ajoutée aux index de recherche ou mappages de synonymes existants, elle peut être mise à jour en fournissant des valeurs différentes pour chacune des trois informations du coffre de clés (par exemple, en mettant à jour la version de la clé). Lorsque vous passez à une nouvelle clé Key Vault ou à une nouvelle version de clé, tout index de recherche ou mappage de synonymes qui utilise la clé doit d’abord être mis à jour pour utiliser la nouvelle clé/version **avant** de supprimer la clé/version précédente. Si vous ne le faites pas, l'index ou le mappage de synonymes deviendra inutilisable et il ne pourra pas déchiffrer le contenu une fois que l'accès aux clés sera perdu. Cependant, une restauration ultérieure des autorisations d’accès au coffre de clés restaurera l’accès au contenu.

## <a name="simpler-alternative-trusted-service"></a>Alternative plus simple : Service approuvé

En fonction de la configuration du locataire et des exigences d’authentification, vous pourrez peut-être implémenter une approche plus simple pour accéder à une clé de coffre de clés. Au lieu de créer et d’utiliser une application Active Directory, vous pouvez faire d’un service de recherche un service approuvé en lui donnant une identité managée par le système ou attribuer une identité managée affectée par l'utilisateur à votre service de recherche. Vous devez ensuite utiliser le service de recherche approuvé ou une identité managée affectée par l'utilisateur comme principe de sécurité, plutôt qu’une application inscrite auprès d’AD, pour accéder à la clé de coffre de clés.

Ces deux approches vous permettent d’omettre les étapes d’inscription des applications et des secrets d’application, et simplifient la définition de la clé de chiffrement.

### <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

En général, une identité managée permet à votre service de recherche de s’authentifier auprès d’Azure Key Vault, sans stocker les informations d’identification (ApplicationID ou ApplicationSecret) dans le code. Le cycle de vie de ce type d'identité managée est lié au cycle de vie de votre service de recherche, qui ne peut avoir qu'une seule identité managée. Pour plus d’informations sur le fonctionnement des identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

1. Faites de votre service de recherche un service approuvé.
   ![Turn on system assigned managed identity](./media/search-managed-identities/turn-on-system-assigned-identity.png "Activer l’identité managée affectée par le système")

1. Quand vous configurez une stratégie d’accès dans Azure Key Vault, choisissez le service de recherche approuvé comme principe (au lieu de l’application inscrite auprès d’AD). Attribuez les mêmes autorisations (GET multiples, WRAP, UNWRAP) comme indiqué dans l’étape d’attribution des autorisations de la clé d’accès.

1. Utilisez une construction simplifiée de `encryptionKey` qui omet les propriétés Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Les conditions qui vous empêchent d’adopter cette approche simplifiée sont les suivantes :

+ Vous ne pouvez pas accorder directement à votre service de recherche les droits d'accès au coffre de clés (par exemple, si le service de recherche se trouve dans un autre locataire Active Directory qu’Azure Key Vault).

+ Un seul service de recherche est nécessaire pour héberger plusieurs index/mappages de synonymes chiffrés, chacun utilisant une clé différente d'un coffre de clés différent, où chaque coffre de clés doit utiliser **une identité différente**  pour l'authentification. Étant donné qu’un service de recherche ne peut avoir qu’une seule identité managée, une configuration nécessitant plusieurs identités disqualifie l’approche simplifiée pour votre scénario.  

### <a name="user-assigned-managed-identity-preview"></a>Identité managée affectée par l’utilisateur (préversion)

> [!IMPORTANT] 
> La prise en charge des identités gérées affectée par l’utilisateur est en préversion publique dans les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Les versions API REST 2021-04-30-Preview et [API REST Gestion 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) offrent cette fonctionnalité.

Affecter une identité managée affectée par l'utilisateur à votre service de recherche lui permet de s’authentifier auprès d’Azure Key Vault, sans stocker les informations d’identification (ApplicationID ou ApplicationSecret) dans le code. Le cycle de vie de ce type d’identité managée est indépendant du cycle de vie de votre service de recherche. Pour plus d’informations sur le fonctionnement des identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md)

1. Si vous n’avez pas encore créé d’identité managée affectée par l’utilisateur, vous devez en créer une. Pour créer une identité managée affectée par l’utilisateur, suivez les étapes ci-dessous :

    1. Connectez-vous au [portail Azure](https://portal.azure.com/).
    1. Sélectionnez **+ Créer une ressource**.
    1. Dans la barre de recherche « Services de recherche et marketplace », recherchez « Identité managée affectée par l’utilisateur », puis sélectionnez **Créer**.
    1. Donnez à l’identité un nom descriptif.

1. Ensuite, attribuez l’identité managée affectée par l’utilisateur au service de recherche. Cela peut être fait à l’aide de l’API de gestion [2021-04-01-preview](/rest/api/searchmanagement/management-api-versions).

    La propriété d’identité prend un type et une ou plusieurs identités affectées par l’utilisateur complètes :
    
    * **type** est le type d’identité utilisé pour la ressource. Le type « SystemAssigned, UserAssigned » comprend à la fois une identité créée par le système et un ensemble d’identités attribuées par l’utilisateur. Le type « None » supprime toutes les identités du service.
    * **userAssignedIdentities** comprend les détails de l’identité managée affectée par l’utilisateur.
        * Format de l’identité managée affectée par l’utilisateur : 
            * /subscriptions/**ID d’abonnement**/resourcegroups/**nom du groupe de ressources**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nom de l’identité managée**
    
    Exemple d’attribution d’une identité managée affectée par l’utilisateur à un service de recherche :
    
    ```http
    PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
    Content-Type: application/json

    {
      "location": "[region]",
      "sku": {
        "name": "[sku]"
      },
      "properties": {
        "replicaCount": [replica count],
        "partitionCount": [partition count],
        "hostingMode": "default"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]": {}
        }
      }
    } 
    ```

1. Quand vous configurez une stratégie d’accès dans Azure Key Vault, choisissez l’identité managée affectée par l'utilisateur comme principe (au lieu de l’application inscrite auprès d’AD). Attribuez les mêmes autorisations (GET multiples, WRAP, UNWRAP) comme indiqué dans l’étape d’attribution des autorisations de la clé d’accès.

1. Utilisez une construction simplifiée de `encryptionKey` qui omet les propriétés Active Directory et ajoutez une identité de propriété. Veillez à utiliser la version de l’API REST 2021-04-30-preview.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://[key vault name].vault.azure.net",
        "keyVaultKeyName": "[key vault key name]",
        "keyVaultKeyVersion": "[key vault key version]",
        "identity" : { 
            "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
            "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
        }
      }
    }
    ```

## <a name="work-with-encrypted-content"></a>Utiliser des colonnes chiffrées

Avec le chiffrement à l’aide de clés gérées par le client, vous remarquerez une latence pour l’indexation et les requêtes en raison du travail de chiffrement/déchiffrement supplémentaire. Le service Recherche cognitive Azure ne journalise pas l’activité de chiffrement, mais vous pouvez surveiller l’accès aux clés par le biais de la journalisation du coffre de clés. Nous vous recommandons d’[activer la journalisation](../key-vault/general/logging.md) dans le cadre de la configuration du coffre de clés.

Une rotation de clés est supposée se produire au fil du temps. Chaque fois que vous voulez opérer une rotation de clés, il est important de suivre cette séquence :

1. [Déterminez la clé utilisée par un index ou un mappage de synonyme](search-security-get-encryption-keys.md).
1. [Créez une clé dans le coffre de clés](../key-vault/keys/quick-create-portal.md), mais gardez la clé d’origine disponible.
1. [Mettez à jour les propriétés encryptionKey](/rest/api/searchservice/update-index) sur un index ou un mappage de synonymes pour utiliser les nouvelles valeurs. Seuls des objets créés à l’origine avec cette propriété peuvent être mis à jour pour utiliser une autre valeur.
1. Désactivez ou supprimez la clé précédente dans le coffre de clés. Surveillez l’accès à la clé pour vérifier que la nouvelle clé est utilisée.

Pour des raisons de performances, le service de recherche met en cache la clé pendant plusieurs heures. Si vous désactivez ou supprimez la clé sans en fournir de nouvelle, les requêtes continuent de fonctionner sur une base temporaire jusqu’à ce que le cache expire. Toutefois, une fois que le service de recherche ne peut pas déchiffrer le contenu, vous obtenez le message suivant : « Accès interdit. La clé de requête utilisée a peut-être été révoquée. Réessayez. » 

## <a name="next-steps"></a>Étapes suivantes

Si vous n’êtes pas familiarisé avec l’architecture de sécurité Azure, passez en revue la [documentation sur la sécurité Azure](../security/index.yml), et en particulier cet article :

> [!div class="nextstepaction"]
> [Chiffrement des données Azure au repos](../security/fundamentals/encryption-atrest.md)
