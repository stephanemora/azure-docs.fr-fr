---
title: Rôles et exigences pour Azure Data Share en préversion
description: Découvrez les rôles et exigences du contrôle d’accès requis pour les fournisseurs de données et les consommateurs de données qui souhaitent partager des données dans Azure Data Share (préversion).
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: c0841f6386440776c6ea719f9932a53cada9d9c4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166374"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Rôles et exigences pour Azure Data Share en préversion

Cet article décrit les rôles requis pour partager des données à l’aide de la préversion d’Azure Data Share, ainsi que pour accepter et recevoir des données à l’aide de la préversion d’Azure Data Share. 

## <a name="roles-and-requirements"></a>Rôles et conditions requises

Azure Data Share utilise des identités gérées pour les services Azure (anciennement appelés fichiers MSI) pour s’authentifier auprès des comptes de stockage sous-jacents afin de pouvoir lire les données devant être partagées par un fournisseur de données et recevoir des données partagées en tant que consommateur de données. Ainsi, il n’y a pas d’échange d’informations d’identification entre un fournisseur de données et un consommateur de données. 

Managed Service Identity doit être autorisé à accéder aux comptes de stockage sous-jacents. Le service Azure Data Share utilise la Managed Service Identity de la ressource Azure Data Share pour lire et écrire des données. L’utilisateur d’Azure Data Share a besoin de pouvoir créer une attribution de rôle pour Managed Service Identity pour le compte de stockage à partir duquel il partage des données. L’autorisation de créer des attributions de rôles existe dans le rôle **propriétaire**, le rôle Administrateur des accès utilisateur ou un rôle personnalisé avec l’autorisation Microsoft.Authorization/role assignments/write affectée. 

Si vous n’êtes pas propriétaire du compte de stockage en question et que vous ne parvenez pas à créer vous-même une attribution de rôle pour l’identité gérée de la ressource Azure Data Share, vous pouvez demander à un administrateur Azure de créer une attribution de rôle pour votre compte. 

Voici un résumé des rôles attribués à l’identité gérée de la ressource Data Share :

| |  |  |
|---|---|---|
|**Type de stockage**|**Compte de stockage du fournisseur de données source**|**Compte de stockage du consommateur de données cible**|
|un stockage Azure Blob| Lecteur des données blob du stockage | Contributeur aux données Blob du stockage
|Azure Data Lake Gen1 | Propriétaire | Non pris en charge
|Azure Data Lake Gen2 | Lecteur des données blob du stockage | Contributeur aux données Blob du stockage
|
### <a name="data-providers"></a>Fournisseurs de données 
Pour ajouter un jeu de données à Azure Data Share, l’identité managée de la ressource du partage de données des fournisseurs de données doit être ajoutée au rôle de lecteur de données de blob de stockage. Cette opération s’effectue automatiquement avec le service Azure Data Share si l’utilisateur ajoute les jeux de données par le biais d’Azure et est propriétaire du compte de stockage, ou s’il est membre d’un rôle personnalisé avec l’autorisation Microsoft.Authorization/role assignments/write affectée. 

L’utilisateur peut également demander à un administrateur Azure d’ajouter manuellement l’identité managée de la ressource de partage de données au rôle de lecteur de données de blob de stockage. La création manuelle de cette attribution de rôle par l’administrateur annule le besoin d’être un propriétaire du compte de stockage ou d’avoir une attribution de rôle personnalisée. Cela s’applique aux données partagées à partir du stockage Azure ou d’Azure Data Lake Gen2. 

Si vous partagez des données à partir d’Azure Data Lake Gen1, l’attribution de rôle doit être effectuée pour le rôle de propriétaire. 

Pour créer une attribution de rôle pour l’identité managée de la ressource Data Share, suivez les étapes ci-dessous :

1. Accédez au compte de stockage.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Sous *Rôle*, sélectionnez *Lecteur de données du blob de stockage*.
1. Sous *Sélectionner*, tapez le nom de votre compte Azure Data Share.
1. Cliquez sur *Enregistrer*.

### <a name="data-consumers"></a>Consommateurs de données
Pour recevoir des données, vous devez ajouter l’identité managée du partage de données des consommateurs de données au rôle de contributeur de données du blob de stockage. Ce rôle est requis pour permettre au service Azure Data Share d’écrire dans le compte de stockage. Cette opération s’effectue automatiquement avec le service Azure Data Share si l’utilisateur ajoute les jeux de données par le biais d’Azure et est propriétaire du compte de stockage, ou s’il est membre d’un rôle personnalisé avec l’autorisation Microsoft.Authorization/role assignments/write affectée. 

L’utilisateur peut également demander à un administrateur Azure d’ajouter manuellement l’identité managée de la ressource de partage de données au rôle de contributeur de données de blob de stockage. La création manuelle de cette attribution de rôle par l’administrateur annule le besoin d’être un propriétaire du compte de stockage ou d’avoir une attribution de rôle personnalisée. Notez que cela s’applique aux données partagées vers le stockage Azure ou Azure Data Lake Gen2. La réception de données sur Azure Data Lake Gen1 n’est pas prise en charge. 

Pour créer une attribution de rôle pour l’identité managée de la ressource Data Share manuellement, suivez les étapes ci-dessous :

1. Accédez au compte de stockage.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Sous *Rôle*, sélectionnez *Contributeur de données du blob de stockage*. 
1. Sous *Sélectionner*, tapez le nom de votre compte Azure Data Share.
1. Cliquez sur *Enregistrer*.

Si vous partagez des données à l’aide de nos API REST, vous devrez créer manuellement ces attributions de rôle en ajoutant le compte de partage de données aux rôles appropriés. 

Pour en savoir plus sur l’ajout d’une attribution de rôle, reportez-vous à [cette documentation](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment), qui décrit comment ajouter une attribution de rôle à une ressource Azure. 

## <a name="resource-provider-registration"></a>Inscription du fournisseur de ressources 

Lorsque vous acceptez une invitation Azure Data Share, vous devez inscrire manuellement le fournisseur de ressources Microsoft.DataShare dans votre abonnement. Suivez ces étapes pour inscrire le fournisseur de ressources Microsoft.DataShare dans votre abonnement Azure. 

1. Dans le portail Azure, accédez à **Abonnements**.
1. Sélectionnez l’abonnement que vous utilisez pour Azure Data Share.
1. Cliquez sur **Fournisseurs de ressources**.
1. Recherchez Microsoft.DataShare.
1. Cliquez sur **S'inscrire**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les rôles dans Azure : [Comprendre les définitions de rôles](../role-based-access-control/role-definitions.md)

