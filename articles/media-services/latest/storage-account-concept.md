---
title: Comptes de stockage dans Azure Media Services | Microsoft Docs
description: Cet article explique comment Media Services utilise des comptes de stockage.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: f5fc1385bff92851db81cd4ed397d66cb8a832f2
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54350929"
---
# <a name="storage-accounts"></a>Comptes de stockage

Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. Le compte Media Services et le compte de stockage associé doivent faire partie du même centre de données et du même groupe de ressources.

Vous devez disposer d’un compte de stockage **principal** et vous pouvez avoir n’importe quel nombre de comptes de stockage **secondaires** associés à votre compte Media Services. Media Services prend en charge les comptes **v2 à usage général** (GPv2) ou **v1 à usage général** (GPv1). 

>[!NOTE]
> Les comptes Blob uniquement ne sont pas autorisés en tant que comptes **principaux**. 

Nous vous recommandons d’utiliser GPv2 pour pouvoir choisir entre des niveaux de stockage chaud ou froid. Pour plus d’informations sur les comptes de stockage, consultez la [présentation générale du compte de stockage Azure](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Ressources d’un compte de stockage

Dans Media Services v3, les API Stockage sont utilisées pour charger des fichiers. Pour savoir comment utiliser les API Stockage avec Media Services pour charger vos fichiers d’entrée, consultez [Create a job input from a local file](job-input-from-local-file-how-to.md) (Créer une entrée de travail à partir d’un fichier local). 

> [!Note]
> Vous ne devez pas essayer de modifier le contenu des conteneurs d’objets blob qui ont été générés par le SDK Media Services sans utiliser les API Media Services.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment attacher un compte de stockage à votre compte Media Services, consultez [Créer un compte](create-account-cli-quickstart.md).
