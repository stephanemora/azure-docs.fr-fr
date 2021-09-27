---
title: Azure Communication Services - Activer les journaux de résumé des appels et de diagnostic d’appel et y accéder
titleSuffix: An Azure Communication Services concept document
description: Comment accéder aux journaux de résumé des appels et de diagnostic d’appel dans Azure Monitor
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: b5c2da9842ba21b63c7b36d5b7377f74a25a1e90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672270"
---
# <a name="enable-and-access-call-summary-and-call-diagnostic-logs"></a>Activer les journaux de résumé des appels et de diagnostic d’appel et y accéder

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Pour accéder à la télémétrie pour les ressources vocales et vidéo Azure Communication Services, effectuez les étapes suivantes.

## <a name="enable-logging"></a>Activation de la journalisation
1. Tout d’abord, vous devez créer un compte de stockage pour vos journaux. Consultez [Créez un compte de stockage](../../storage/common/storage-account-create.md?tabs=azure-portal) pour obtenir des instructions sur cette étape. Pour plus d’informations sur les types et caractéristiques des différentes options de stockage, consultez également [Vue d’ensemble du compte de stockage](../../storage/common/storage-account-overview.md). Si vous disposez déjà d’un compte de stockage Azure, passez à l’étape 2.
 
1. Quand vous avez créé votre compte de stockage, vous devez activer la journalisation en suivant les instructions fournies dans [Activer les journaux de diagnostic dans votre ressource](./logging-and-diagnostics.md#enable-diagnostic-logs-in-your-resource). Activez les cases à cocher des journaux « CallSummaryPRIVATEPREVIEW » et « CallDiagnosticPRIVATEPREVIEW ». 

1. Ensuite, activez la case à cocher « Archiver dans un compte de stockage », puis sélectionnez le compte de stockage pour vos journaux dans le menu déroulant qui suit. L’option « Envoyer à l’espace de travail Analytics » n’est pas disponible actuellement pour la préversion privée de cette fonctionnalité. Elle sera disponible quand cette fonctionnalité sera rendue publique.

:::image type="content" source="media\call-logs-images\call-logs-access-diagnostic-setting.png" alt-text="Azure Monitor - Configuration de diagnostic":::



## <a name="access-your-logs"></a>Accéder à vos journaux

Pour accéder à vos journaux, accédez au compte de stockage que vous avez spécifié à l’étape 3 plus haut sous [Comptes de stockage](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) dans le portail Azure. 

:::image type="content" source="media\call-logs-images\call-logs-access-storage.png" alt-text="Stockage dans le portail Azure":::

Vous pouvez alors télécharger tous les journaux ou des journaux spécifiques.

:::image type="content" source="media\call-logs-images\call-logs-access-storage-resource.png" alt-text="Stockage dans le portail Azure - Téléchargement":::

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [journalisation et les diagnostics](./logging-and-diagnostics.md)