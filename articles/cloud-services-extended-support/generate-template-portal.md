---
title: Générer un modèle ARM pour les services cloud (prise en charge étendue) à l’aide du portail Azure
description: Générer et télécharger le fichier de paramètres et de modèle ARM pour Cloud Services (prise en charge étendue) à l’aide du portail Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 215abb1ce8d65b5ecdd25aeb78c17c70e801a9d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555052"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Générer un modèle ARM pour les services cloud (prise en charge étendue) à l’aide du portail Azure

Cet article explique comment récupérer le modèle ARM et le fichier de paramètres à partir du [portail Azure](https://portal.azure.com) après le déploiement du service cloud (support étendu). Le modèle ARM et le fichier de paramètres peuvent être utilisés dans des déploiements futurs pour mettre à niveau ou mettre à jour un service cloud (prise en charge étendue)

## <a name="get-arm-template-via-portal"></a>Obtenir un modèle ARM via le portail

  1. Accédez à votre groupe de ressources, puis sélectionnez Déploiements.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="L’image montre la sélection des déploiements sous le groupe de ressources sur le portail Azure.":::
  
  2. Sélectionnez votre service cloud (prise en charge étendue), puis cliquez sur le modèle.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="L’image montre la sélection d’un modèle sous le service cloud (prise en charge étendue) sur le portail Azure.":::
  
  3. Téléchargez votre modèle et vos fichiers de paramètres. Ils peuvent être utilisés pour les déploiements ultérieurs via PowerShell.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="L’image montre le téléchargement du fichier de modèle sur le portail Azure.":::
  
## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [questions fréquentes (FAQ)](faq.md) concernant Cloud Services (support étendu).
- Déployer un service cloud Azure (support étendu) à l’aide du [portail Azure](deploy-portal.md)
  
