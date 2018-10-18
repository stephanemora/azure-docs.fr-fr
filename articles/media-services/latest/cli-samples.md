---
title: Exemples de l’interface de ligne de commande Azure - Azure Media Services | Microsoft Docs
description: Exemples de l’interface de ligne de commande Azure pour le service Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5559f9055da3a2a852427c0f27d367159cdc7655
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388505"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemples de l’interface de ligne de commande Azure pour Azure Media Services

Le tableau suivant comprend des liens vers les exemples de l’interface de ligne de commande Azure pour Azure Media Services.

|  |  |
|---|---|
|**Compte**||
| [Créer un compte Media Services](./scripts/cli-create-account.md) | Crée un compte Azure Media Services. Crée également un service principal qui peut être utilisé pour accéder aux API afin de gérer le compte par programmation. |
| [Réinitialiser les informations d’identification de compte](./scripts/cli-reset-account-credentials.md)|Réinitialise vos informations d’identification de compte et récupère les paramètres du fichier app.config.|
|**Éléments multimédias**||
| [Créer des éléments multimédias](./scripts/cli-create-asset.md)|Crée un élément multimédia Media Services vers lequel charger du contenu.|
| [Charger un fichier](./scripts/cli-upload-file-asset.md)|Charge un fichier local sur un conteneur de stockage.|
| [Publier un élément multimédia](./scripts/cli-publish-asset.md)| Crée un localisateur de diffusion en continu et récupère les URL de diffusion en continu. |
| **Transformations** et **travaux**||
| [Créer des transformations](./scripts/cli-create-transform.md)|Montre comment créer des transformations. Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »).<br/> Vous devez toujours vérifier si une transformation portant le nom de votre choix et « recette » existe déjà. Dans ce cas, réutilisez-la. |
| [Créer des travaux](./scripts/cli-create-jobs.md)|Soumet un travail à une transformation de codage simple à l’aide d’URL HTTPs.|
| [Créer un Event Grid](./scripts/cli-create-event-grid.md)|Crée un abonnement Event Grid au niveau du compte pour les modifications de l’état du travail.|

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
