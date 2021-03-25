---
title: Vue d’ensemble de la sécurité pour Azure Data Share
description: Vue d’ensemble de la sécurité pour Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678424"
---
# <a name="security-overview-for-azure-data-share"></a>Vue d’ensemble de la sécurité pour Azure Data Share

Cet article fournit une vue d’ensemble de la sécurité du service Azure Data Share.

## <a name="security-overview"></a>Présentation de la sécurité

Azure Data Share s’appuie sur la sécurité sous-jacente fournie par Azure pour protéger les données au repos et en transit. Les données sont chiffrées au repos, lorsque le magasin de données sous-jacent le permet. Les données en transit sont également chiffrées à l’aide de TLS 1.2. Les métadonnées concernant un partage de données sont également chiffrées lorsqu’elles sont au repos et en transit. Azure Data Share ne stocke pas le contenu des données client qui est partagé.

Azure Data Share tire parti de l’identité managée (anciennement MSI) pour accéder aux magasins de données utilisés pour le partage de données. Il n’y a pas d’échange d’informations d’identification entre un fournisseur de données et un consommateur de données. Pour plus d’informations sur l’identité managée, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Pour plus d’informations sur les rôles et les autorisations nécessaires pour partager des données, reportez-vous à [Rôles et conditions requises](concepts-roles-permissions.md).

## <a name="access-control"></a>Contrôle d’accès

Les contrôles d’accès à Azure Data Share peuvent être définis au niveau des ressources Data Share, afin que seules les personnes autorisées puissent y accéder. Les propriétaires et contributeurs d’une ressource Data Share peuvent partager des données, recevoir des partages et modifier les partages existants. Les lecteurs d’une ressource Data Share peuvent afficher les partages, mais pas les modifier. 

Une fois qu’un partage a été créé ou reçu, les utilisateurs disposant des autorisations appropriées sur la ressource Data Share peuvent y apporter des modifications. Si un utilisateur ayant créé ou reçu un partage quitte l’organisation, cela n’entraine pas l’interruption du partage ou l’arrêt du flux des données. Les autres utilisateurs disposant des autorisations appropriées sur la ressource Data Share peuvent continuer à gérer le partage.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Partager des données à partir de ou vers des magasins de données avec le pare-feu activé
Pour partager des données à partir de ou vers des comptes de stockage avec le pare-feu activé, vous devez sélectionner **Autoriser les services Microsoft approuvés** dans votre compte de stockage. Consultez [Configurer des pare-feu Stockage Azure et des réseaux virtuels](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) pour obtenir des détails.


## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).
