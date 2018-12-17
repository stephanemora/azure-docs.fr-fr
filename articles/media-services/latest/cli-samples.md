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
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 9df9279a3922fa639385657660d6d0f4a55b5a4d
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132770"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemples de l’interface de ligne de commande Azure pour Azure Media Services

Le tableau suivant comprend des liens vers les exemples de l’interface de ligne de commande Azure pour Azure Media Services.

## <a name="examples"></a>Exemples

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
