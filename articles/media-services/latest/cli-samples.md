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
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109246"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemples de l’interface de ligne de commande Azure pour Azure Media Services

Le tableau suivant comprend des liens vers les exemples de l’interface de ligne de commande Azure pour Azure Media Services.

## <a name="examples"></a>Exemples

|  |  |
|---|---|
|**Mettre à l'échelle**||
| [Mettre à l’échelle les unités réservées Multimédia](media-reserved-units-cli-how-to.md)|Pour les travaux d’analyse audio et vidéo déclenchés par Media Services v3 ou Video Indexer, nous vous recommandons de provisionner votre compte avec des MRU 10 S3. <br/>Le script montre comment utiliser CLI pour mettre à l’échelle des unités réservée Multimédia.|
|**Compte**||
| [Créer un compte Media Services](./scripts/cli-create-account.md) | Crée un compte Azure Media Services. Crée également un service principal qui peut être utilisé pour accéder aux API afin de gérer le compte par programmation. |
| [Réinitialiser les informations d’identification de compte](./scripts/cli-reset-account-credentials.md)|Réinitialise vos informations d’identification de compte et récupère les paramètres du fichier app.config.|
|**Éléments multimédias**||
| [Créer des éléments multimédias](./scripts/cli-create-asset.md)|Crée un élément multimédia Media Services vers lequel charger du contenu.|
| [Charger un fichier](./scripts/cli-upload-file-asset.md)|Charge un fichier local sur un conteneur de stockage.|
| **Transformations** et **travaux**||
| [Créer des transformations](./scripts/cli-create-transform.md)|Montre comment créer des transformations. Les transformations décrivent un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio (également désigné par le terme « recette »).<br/> Vous devez toujours vérifier si une transformation portant le nom de votre choix et « recette » existe déjà. Dans ce cas, réutilisez-la. |
| [Créer des travaux](./scripts/cli-create-jobs.md)|Soumet un travail à une transformation de codage simple à l’aide d’URL HTTPs.|
| [Créer un Event Grid](./scripts/cli-create-event-grid.md)|Crée un abonnement Event Grid au niveau du compte pour les modifications de l’état du travail.|
| **Remettre**||
| [Publier un élément multimédia](./scripts/cli-publish-asset.md)| Crée un localisateur de diffusion en continu et récupère les URL de diffusion en continu. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Configure un filtre pour un élément Vidéo à la demande et montre comment utiliser CLI pour créer des [Filtres de compte](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) et des [Filtres de ressource](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Voir aussi

[Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
