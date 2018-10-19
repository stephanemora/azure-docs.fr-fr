---
title: Comptes de stockage dans Azure Media Services | Microsoft Docs
description: Cet article explique comment Media Services utilise des comptes de stockage.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 883aecaa82f1a83af22bf345d364c2b43776e559
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732207"
---
# <a name="storage-accounts"></a>Comptes de stockage

Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. 

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
