---
title: Chargement et stockage sur le cloud avec Azure Media Services | Microsoft Docs
description: Cet article décrit les concepts de téléchargement et de stockage sur le cloud.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550144"
---
# <a name="cloud-upload-and-storage"></a>Chargement et stockage sur le cloud

Pour commencer à gérer, chiffrer, coder, analyser, gérer et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services. Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. 

Le compte Media Services et tous les comptes de stockage associés doivent être faire partie du même abonnement Azure. Il est fortement recommandé d’utiliser des comptes de stockage situés au même emplacement que le compte Media Services afin d’éviter des frais supplémentaires de sortie des données et une importante latence.

Vous devez disposer d’un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Media Services prend en charge les comptes **v2 à usage général** (GPv2) ou **v1 à usage général** (GPv1). 

>[!NOTE]
> Les comptes Blob uniquement ne sont pas autorisés en tant que comptes **principaux**. 

Nous vous recommandons d’utiliser GPv2 pour pouvoir choisir entre des niveaux de stockage chaud ou froid. Pour plus d’informations sur les comptes de stockage, consultez la [présentation générale du compte de stockage Azure](../../storage/common/storage-account-overview.md). 

Vous pouvez choisir entre différentes références SKU pour votre compte de stockage. Pour plus d’informations, consultez [Comptes de stockage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Si vous voulez faire des expériences avec des comptes de stockage, utilisez `--sku Standard_LRS`. Cependant, lors de la sélection d’une référence SKU pour la production, envisagez `--sku Standard_RAGRS`, qui offre la réplication géographique pour la continuité de l’activité. 

## <a name="assets-in-a-storage-account"></a>Ressources d’un compte de stockage

Dans Media Services v3, les API Stockage sont utilisées pour charger des fichiers dans des éléments multimédias. Pour plus d’informations, consultez [Assets concept](assets-concept.md) (Concept des éléments multimédias).

> [!Note]
> Vous ne devez pas essayer de modifier le contenu des conteneurs d’objets blob qui ont été générés par le SDK Media Services sans utiliser les API Media Services.
 
## <a name="storage-side-encryption"></a>Chiffrement côté stockage

Pour protéger vos éléments au repos, les ressources doivent être chiffrées par le chiffrement côté stockage. Le tableau suivant montre comment fonctionne le chiffrement côté stockage dans Media Services v3 :

|Option de chiffrement|Description|Media Services v3|
|---|---|---|
|Chiffrement du stockage de Media Services| Chiffrement AES-256, clé gérée par Media Services|Non pris en charge<sup>(1)</sup>|
|[Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chiffrement côté serveur proposé par le stockage Azure, clé gérée par Azure ou par un client|Pris en charge|
|[Chiffrement de stockage côté client](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Chiffrement côté client proposé par le stockage Azure, clé gérée par un client dans un coffre de clés|Non pris en charge|

<sup>1</sup> Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

## <a name="storage-account-errors"></a>Erreurs relatives au compte de stockage

L’état « Déconnecté » pour un compte Media Services indique que ce compte n’a plus accès à un ou plusieurs des comptes de stockage attachés en raison d’un changement dans les clés d’accès du stockage. Des clés d’accès du stockage à jour sont nécessaires à Media Services pour effectuer de nombreuses tâches dans le compte.

Voici les principaux scénarios qui aboutissent à un compte Media Services n’ayant pas accès aux comptes de stockage attachés. 

|Problème|Solution|
|---|---|
|Le compte Media Services ou un ou plusieurs comptes de stockage attachés ont été migrés vers des abonnements distincts. |Migrez le ou les comptes de stockage ou le compte Media Services afin qu’ils soient tous dans le même abonnement. |
|Le compte Media Services utilise un compte de stockage attaché dans un abonnement différent car il s’agissait d’un ancien compte Media Services où ceci était pris en charge. Tous les anciens comptes Media Services ont été convertis en comptes modernes basés sur Azure Resource Manager et ont un état Déconnecté. |Migrez le compte de stockage ou le compte Media Services afin qu’ils soient tous dans le même abonnement.|

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment attacher un compte de stockage à votre compte Media Services, consultez [Créer un compte](create-account-cli-quickstart.md).
