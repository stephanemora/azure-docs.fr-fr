---
title: Rôles et exigences pour Azure Data Share en préversion
description: Rôles et exigences pour Azure Data Share en préversion
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 76889904dd89da0b44dad6b47250351c73f75653
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788524"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Rôles et exigences pour Azure Data Share en préversion

Cet article décrit les rôles requis pour partager des données à l’aide de la préversion d’Azure Data Share, ainsi que pour accepter et recevoir des données à l’aide de la préversion d’Azure Data Share. 

## <a name="roles-and-requirements"></a>Rôles et conditions requises

Pour partager ou recevoir des données à l’aide d’Azure Data Share, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être en mesure d’accorder des autorisations du partage de données au compte de stockage dont vous partagez les données à ou sur lequel vous recevez des données. Il s’agit en général d’une autorisation qui existe dans le rôle **propriétaire** ou dans un rôle personnalisé avec l’autorisation Microsoft.Authorization/role assignments/write affectée. 

Pour partager ou recevoir des données sur un compte de stockage Azure, vous devez être un propriétaire du compte de stockage sur lequel vous recevez des données. Même si vous avez créé le compte de stockage, cela ne vous accorde pas automatiquement la propriété du compte de stockage. Pour vous ajouter vous-même au rôle de propriétaire de votre compte de stockage Azure, procédez comme suit.

1. Accéder à un compte de stockage dans le portail Azure
1. Sélectionner **Contrôle d’accès (IAM)**
1. Cliquez sur **Ajouter**.
1. Ajoutez-vous en tant que propriétaire des données blob du stockage

Pour visualiser les autorisations dont vous disposez dans l’abonnement, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du Portail Azure, puis sélectionnez **Autorisations**. Si vous avez accès à plusieurs abonnements, sélectionnez l’abonnement approprié. 

## <a name="resource-provider-registration"></a>Inscription du fournisseur de ressources 

Lorsque vous acceptez une invitation Azure Data Share, vous devez inscrire manuellement le fournisseur de ressources Microsoft.DataShare dans votre abonnement. Suivez ces étapes pour inscrire le fournisseur de ressources Microsoft.DataShare dans votre abonnement Azure. 

1. Dans le portail Azure, accédez aux **Abonnements**
1. Sélectionnez l’abonnement que vous utilisez pour Azure Data Share
1. Cliquez sur **Fournisseurs de ressources**
1. Recherchez Microsoft.DataShare
1. Cliquez sur **S’inscrire**

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les rôles dans Azure : [Comprendre les définitions de rôles](../role-based-access-control/role-definitions.md)

