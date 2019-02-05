---
title: 'Exemple de script Azure CLI : Créer un abonnement Azure Event Grid | Microsoft Docs'
description: Le script Azure CLI de cette rubrique montre comment créer un abonnement Event Grid au niveau du compte pour tout changement d’état du travail.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098903"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Exemple CLI : Créer un abonnement Azure Event Grid 

Le script Azure CLI de cet article montre comment créer un abonnement Event Grid au niveau du compte pour tout changement d’état du travail.

## <a name="prerequisites"></a>Prérequis 

[Créer un compte Media Services](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples, consultez les [exemples Azure CLI](../cli-samples.md).
