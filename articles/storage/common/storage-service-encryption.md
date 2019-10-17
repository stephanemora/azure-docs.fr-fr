---
title: Chiffrement du Stockage Azure pour les données au repos | Microsoft Docs
description: Le Stockage Azure protège vos données en les chiffrant automatiquement avant de les rendre persistantes dans le cloud. Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés.
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a15d450d033c04c59f6981a887689f1fc08919f1
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958847"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Chiffrement du Stockage Azure pour les données au repos

Le Stockage Azure chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Les données dans Stockage Azure sont chiffrées et déchiffrées en toute transparence à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits, un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Le chiffrement du Stockage Azure est similaire au chiffrement BitLocker sur Windows.

Le chiffrement du Stockage Azure est activé pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. Étant donné que vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement du Stockage Azure.

Les comptes de stockage sont chiffrés quel que soit leur niveau de performances (standard ou premium) ou modèle de déploiement (Azure Resource Manager ou Classic). Toutes les options de redondance de Stockage Azure prennent en charge le chiffrement, et toutes les copies d’un compte de stockage sont chiffrées. Toutes les ressources de Stockage Azure sont chiffrées, y compris les objets blob, disques, fichiers, files d’attente et tables. Toutes les métadonnées d’objet sont également chiffrées.

Le chiffrement n’affecte pas les performances de Stockage Azure. Le chiffrement de Stockage Azure n’implique aucun coût supplémentaire.

Pour plus d’informations sur les modules cryptographiques de chiffrement de Stockage Azure, consultez [API de chiffrement : nouvelle génération](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Vous pouvez vous reposer sur les clés managées par Microsoft pour le chiffrement de votre compte de stockage, ou vous pouvez gérer le chiffrement avec vos propres clés. Si vous choisissez de gérer le chiffrement avec vos propres clés, deux options s’offrent à vous :

- Vous pouvez spécifier une *clé managée par le client* à utiliser pour le chiffrement et le déchiffrement de toutes les données dans le compte de stockage. Une clé managée par le client est utilisée pour chiffrer toutes les données de tous les services de votre compte de stockage.
- Vous pouvez spécifier une *clé fournie par le client* sur les opérations de stockage Blob. Un client qui effectue une requête de lecture ou d’écriture sur le stockage Blob peut inclure une clé de chiffrement dans la requête afin de contrôler la précision avec laquelle les données blob sont chiffrées et déchiffrées.

Le tableau suivant compare les options de gestion de clés pour le chiffrement de Stockage Azure.

|                                        |    Clés managées par Microsoft                             |    Clés managées par le client                                                                                                                        |    Clés fournies par le client                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Opérations de chiffrement/déchiffrement    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Services de stockage Azure pris en charge    |    Tous                                                |    Stockage Blob, Azure Files                                                                                                               |    Stockage d'objets blob                                                                  |
|    Stockage des clés                         |    Magasin de clés Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault ou tout autre magasin de clés                                                                 |
|    Responsabilité de la permutation des clés         |    Microsoft                                          |    Client                                                                                                                                     |    Client                                                                      |
|    Utilisation de la clé                           |    Microsoft                                          |    Portail Azure, API REST de fournisseur de ressources de stockage, bibliothèques de gestion de Stockage Azure, PowerShell, CLI        |    API REST de Stockage Azure (stockage d’objets blob), bibliothèques de client Stockage Azure    |
|    Accès aux clés                          |    Microsoft uniquement                                     |    Microsoft, client                                                                                                                    |    Client uniquement                                                                 |

Les sections suivantes décrivent en détail chacune des options de gestion des clés.

## <a name="microsoft-managed-keys"></a>Clés managées par Microsoft

Par défaut, votre compte de stockage utilise des clés de chiffrement managées par Microsoft. Vous pouvez voir les paramètres de chiffrement de votre compte de stockage dans la section **Chiffrement** du [Portail Azure](https://portal.azure.com), comme illustré dans l’image suivante.

![Afficher le compte chiffré avec des clés managées par Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>Clés managées par le client

Vous pouvez choisir de gérer le chiffrement de Stockage Azure au niveau du compte de stockage avec vos propres clés. Quand vous spécifiez une clé managée par le client au niveau du compte de stockage, cette clé est utilisée pour chiffrer et déchiffrer toutes les données dans le compte de stockage, y compris les données de blob, de file d’attente, de fichier et de table.  Les clés managées par le client offrent plus de flexibilité pour créer, permuter, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données.

Vous devez utiliser Azure Key Vault pour stocker vos clés managées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le compte de stockage et le coffre de clés doivent se trouver dans la même région, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md).

Ce diagramme montre comment Stockage Azure utilise Azure Active Directory et Azure Key Vault pour effectuer des requêtes à l’aide de la clé managée par le client :

![Diagramme montrant le fonctionnement des clés managées par le client dans Stockage Azure](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

La liste suivante décrit les étapes numérotées dans le diagramme :

1. Un administrateur Azure Key Vault accorde des autorisations d’accès à des clés de chiffrement à l’identité managée associée au compte de stockage.
2. Un administrateur Stockage Azure configure le chiffrement avec une clé managée par le client pour le compte de stockage.
3. Stockage Azure utilise l’identité managée associée au compte de stockage pour authentifier l’accès à Azure Key Vault par le biais d’Azure Active Directory.
4. Stockage Azure encapsule la clé de chiffrement du compte avec la clé du client dans Azure Key Vault.
5. Pour les opérations de lecture/écriture, Stockage Azure envoie des requêtes à Azure Key Vault pour encapsuler et désencapsuler la clé de chiffrement du compte afin d’effectuer des opérations de chiffrement et de déchiffrement.

Pour révoquer l’accès aux clés managées par le client sur le compte de stockage, consultez [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) et [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les données dans le compte de stockage, car la clé de chiffrement n’est pas accessible au Stockage Azure.

Les clés managées par le client ne sont pas prises en charge pour des [disques managés Azure](../../virtual-machines/windows/managed-disks-overview.md).

Pour savoir comment utiliser des clés managées par le client avec le Stockage Azure, consultez un de ces articles :

- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis le Portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Utiliser les clés managées par le client avec le chiffrement du Stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés managées par le client dans le portail Azure, une identité managée est affectée automatiquement à votre compte de stockage dans les coulisses. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le compte de stockage d’un annuaire Azure AD vers un autre, l’identité managée associée au compte de stockage n’est pas transférée vers le nouveau locataire ; les clés managées par le client risquent donc de ne plus fonctionner. Pour plus d’informations, consultez **Transfert d’un abonnement entre des répertoires Azure AD** dans [FAQ et problèmes connus en lien avec les identités managées pour ressources Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="customer-provided-keys-preview"></a>Clés fournies par le client (préversion)

Les clients effectuant des requêtes auprès du stockage d’objets blob Azure ont la possibilité de fournir une clé de chiffrement sur une requête individuelle. L’inclusion de la clé de chiffrement dans la requête offre un contrôle précis des paramètres de chiffrement pour les opérations de stockage d’objets blob. Les clés fournies par le client (préversion) peuvent être stockées dans Azure Key Vault ou dans un autre magasin de clés.

### <a name="encrypting-read-and-write-operations"></a>Chiffrement des opérations de lecture et d’écriture

Quand une application cliente fournit une clé de chiffrement dans la requête, Stockage Azure effectue le chiffrement et le déchiffrement de manière transparente lors de la lecture et de l’écriture des données blob. Un hachage SHA-256 de la clé de chiffrement est écrit avec le contenu d’un objet blob, et est utilisé pour vérifier que toutes les opérations suivantes sur l’objet blob utilisent la même clé de chiffrement. Stockage Azure ne stocke pas et ne gère pas la clé de chiffrement que le client envoie avec la requête. La clé est supprimée de façon sécurisée dès que le processus de chiffrement ou de déchiffrement est terminé.

Quand un client crée ou met à jour un objet blob à l’aide d’une clé fournie par le client, les requêtes de lecture et d’écriture suivantes pour cet objet blob doivent également fournir la clé. Si la clé n’est pas fournie sur une requête d’objet blob qui a déjà été chiffrée avec une clé fournie par le client, la requête échoue avec le code d’erreur 409 (conflit).

Si l’application cliente envoie une clé de chiffrement dans la requête et que le compte de stockage est également chiffré à l’aide d’une clé managée par Microsoft ou d’une clé managée par le client, Stockage Azure utilise la clé fournie dans la requête pour le chiffrement et le déchiffrement.

Pour envoyer la clé de chiffrement dans le cadre de la requête, un client doit établir une connexion sécurisée à Stockage Azure à l’aide du protocole HTTPS.

Chaque instantané d’objet blob peut avoir sa propre clé de chiffrement.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>En-têtes de requêtes pour spécifier les clés fournies par le client

Pour les appels REST, les clients peuvent utiliser les en-têtes suivants pour transmettre en toute sécurité des informations sur les clés de chiffrement sur une requête au stockage d’objets blob :

|En-tête de requête | Description |
|---------------|-------------|
|`x-ms-encryption-key` |Nécessaire pour les requêtes d’écriture et de lecture. Valeur de clé de chiffrement AES-256 encodée en base64. |
|`x-ms-encryption-key-sha256`| Nécessaire pour les requêtes d’écriture et de lecture. Valeur SHA256 encodée en base64 de la clé de chiffrement. |
|`x-ms-encryption-algorithm` | Nécessaire pour les requêtes d’écriture, facultatif pour les requêtes de lecture. Spécifie l’algorithme à utiliser lors du chiffrement des données à l’aide de la clé donnée. Doit être AES256. |

La spécification de clés de chiffrement dans la requête est facultative. Toutefois, si vous spécifiez l’un des en-têtes listés ci-dessus pour une opération d’écriture, vous devez les spécifier tous.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Opérations de stockage d’objets blob prenant en charge les clés fournies par le client

Les opérations de stockage d’objets blob suivantes prennent en charge l’envoi de clés de chiffrement fournies par le client sur une requête :

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block from URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page from URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Permuter des clés fournies par le client

Pour permuter une clé de chiffrement transmise dans la requête, téléchargez l’objet blob et rechargez-le avec la nouvelle clé de chiffrement.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser le portail Azure pour lire ou écrire dans un conteneur ou un objet blob chiffré avec une clé fournie dans la requête.
>
> Veillez à protéger la clé de chiffrement que vous fournissez dans une requête au stockage d’objets blob dans un magasin de clés sécurisé comme Azure Key Vault. Si vous tentez d’effectuer une opération d’écriture sur un conteneur ou un objet blob sans clé de chiffrement, l’opération échoue et vous perdez l’accès à l’objet.

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>Exemple : Utiliser une clé fournie par le client pour charger un objet blob dans .NET

L’exemple suivant crée une clé fournie par le client et utilise cette clé pour charger un objet blob. Le code charge un bloc, puis valide la liste de blocs pour écrire l’objet blob dans Stockage Azure. La clé est fournie sur l’objet [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) en définissant la propriété [CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey).

La clé est créée avec la classe [AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider). Pour créer une instance de cette classe dans votre code, ajoutez une instruction `using` qui référence l’espace de noms `System.Security.Cryptography` :

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Chiffrement du Stockage Azure et chiffrement de disque

Avec le chiffrement du Stockage Azure, tous les comptes de Stockage Azure et les ressources qu’ils contiennent sont chiffrés, y compris les objets blob de pages qui stockent des disques de machine virtuelle Azure. Les disques de machine virtuelle Azure peuvent également être chiffrés avec [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption utilise les fonctionnalités standard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) sur Windows et [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) sur Linux pour fournir des solutions de chiffrement basées sur le système d’exploitation qui sont intégrées à Azure Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-overview.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis le Portail Azure](storage-encryption-keys-portal.md)
- [Configurer les clés managées par le client pour le chiffrement du Stockage Azure depuis PowerShell](storage-encryption-keys-powershell.md)
- [Configurer des clés gérées par le client pour le chiffrement du stockage Azure depuis Azure CLI](storage-encryption-keys-cli.md)
