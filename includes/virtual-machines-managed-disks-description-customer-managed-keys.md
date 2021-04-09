---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750782"
---
Vous pouvez choisir de gérer le chiffrement au niveau de chaque disque managé, avec vos propres clés. Le chiffrement côté serveur pour les disques managés avec des clés gérées par le client offre une expérience intégrée avec Azure Key Vault. Vous pouvez importer [vos clés RSA](../articles/key-vault/keys/hsm-protected-keys.md) vers votre Key Vault ou générer de nouvelles clés RSA dans Azure Key Vault. 

Les disques managés Azure gèrent le chiffrement et le déchiffrement de manière entièrement transparente à l’aide du [chiffrement d’enveloppe](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ils chiffrent les données à l’aide d’une clé de chiffrement de données [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, qui est à son tour protégée à l’aide de vos clés. Le service Stockage génère des clés de chiffrement de données et les chiffre avec des clés gérées par le client à l’aide du chiffrement RSA. Le chiffrement d’enveloppe vous permet de faire pivoter (modifier) régulièrement vos clés en fonction de vos stratégies de conformité sans perturber le fonctionnement de vos machines virtuelles. Lorsque vous faites pivoter vos clés, le service Stockage rechiffre les clés de chiffrement de données avec les nouvelles clés gérées par le client. 

#### <a name="full-control-of-your-keys"></a>Contrôle total de vos clés

Vous devez accorder l’accès aux disques managés dans votre Key Vault pour utiliser vos clés pour le chiffrement et le déchiffrement de la clé de chiffrement. Cela vous permet de contrôler intégralement vos données et vos clés. Vous pouvez désactiver vos clés ou révoquer l’accès aux disques managés à tout moment. Vous pouvez également auditer l’utilisation de la clé de chiffrement avec la surveillance Azure Key Vault pour vous assurer que seuls les disques managés ou d’autres services Azure approuvés accèdent à vos clés.

Pour les SSD Premium, les SSD Standard et les HDD Standard : si vous désactivez ou supprimez votre clé, toutes les machines virtuelles comportant des disques qui utilisent cette clé s’arrêtent automatiquement. Elles ne seront plus utilisables tant que la clé n’aura pas été réactivée ou que vous n’aurez pas affecté une nouvelle clé.    

Pour les Disques Ultra : si vous désactivez ou supprimez une clé, les machines virtuelles comportant des Disques Ultra qui utilisent cette clé ne s’arrêtent pas automatiquement. Une fois que vous aurez libéré et redémarré les machines virtuelles, les disques arrêteront d’utiliser la clé et les machines virtuelles ne sont plus en ligne. Pour les remettre en ligne, vous devrez affecter une nouvelle clé ou activer la clé existante.    

Le diagramme suivant montre comment les disques managés utilisent Azure Active Directory et Azure Key Vault pour effectuer des demandes en utilisant la clé gérée par le client :

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Workflow de disques managés et de clés gérées par le client. Un administrateur crée un Azure Key Vault, puis crée et configure un jeu de chiffrement de disque. Le jeu est associé à une machine virtuelle qui permet au disque d’utiliser Azure AD pour s’authentifier":::

La liste suivante décrit le diagramme plus en détail :

1. Un administrateur Azure Key Vault crée des ressources de coffre de clés.
1. L’administrateur du coffre de clés importe ses clés RSA vers Key Vault ou génère de nouvelles clés RSA dans Key Vault.
1. Cet administrateur crée une instance de ressource de jeu de chiffrement de disque, en spécifiant un ID Azure Key Vault et une URL de clé. Le jeu de chiffrement de disque est une nouvelle ressource introduite pour simplifier la gestion des clés pour les disques managés. 
1. Lors de la création d’un jeu de chiffrement de disque, une [Identité managée affectée par le système](../articles/active-directory/managed-identities-azure-resources/overview.md) est créée dans Azure Active Directory (AD) et est associée au jeu de chiffrement de disque. 
1. L’administrateur du coffre de clés Azure accorde ensuite à l’identité managée l’autorisation d’effectuer des opérations dans le coffre de clés.
1. Un utilisateur de machine virtuelle crée des disques en les associant au jeu de chiffrement de disque. L’utilisateur de la machine virtuelle peut également activer le chiffrement côté serveur avec des clés gérées par le client pour les ressources existantes en les associant au jeu de chiffrement de disque. 
1. Les disques managés utilisent l’identité managée pour envoyer des demandes au Azure Key Vault.
1. Pour la lecture ou l’écriture de données, les disques managés envoient des demandes à Azure Key Vault pour chiffrer (envelopper) et déchiffrer (désenvelopper) la clé de chiffrement des données, afin d’effectuer le chiffrement et le déchiffrement des données. 

Pour révoquer l’accès aux clés managées par le client, consultez [Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) et [Azure Key Vault CLI](/cli/azure/keyvault). La révocation de l’accès bloque efficacement l’accès à toutes les données dans le compte de stockage, car la clé de chiffrement n’est pas accessible au Stockage Azure.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Rotation automatique des clés gérées par le client (version préliminaire)

Vous pouvez activer la rotation automatique des clés vers la dernière version de la clé. Un disque fait référence à une clé via son jeu de chiffrement de disque. Lorsque vous activez la rotation automatique pour un jeu de chiffrement de disque, le système met automatiquement à jour tous les disques managés, les captures instantanées et les images référençant le jeu de chiffrement de disque pour utiliser la nouvelle version de la clé dans un délai d’une heure. La fonctionnalité est actuellement disponible en version préliminaire dans un nombre limité de régions. Pour en savoir plus sur la disponibilité régionale, consultez la section [Régions prises en charge](#supported-regions).