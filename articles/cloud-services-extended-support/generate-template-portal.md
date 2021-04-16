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
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077099"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Générer un modèle ARM pour les services cloud (prise en charge étendue) à l’aide du portail Azure

Cet article explique comment télécharger le modèle ARM et le fichier de paramètres à partir du [portail Azure](https://portal.azure.com) pour votre service cloud. Vous pouvez utiliser le modèle ARM et le fichier de paramètres dans les déploiements via PowerShell pour créer ou mettre à jour un service cloud

## <a name="get-arm-template-via-portal"></a>Obtenir un modèle ARM via le portail

  1. Accédez au portail Azure pour [créer un service cloud](deploy-portal.md). Ajoutez vos fichiers de configuration, de package et de définition de service cloud. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="L’image montre la section de chargement de l’onglet des informations de base lors de la création.":::
  
  2. Une fois tous les champs remplis, passez à l’onglet Vérifier et créer afin de valider votre configuration de déploiement, puis cliquez sur **Télécharger un modèle pour l’automatisation** dans votre service cloud (support étendu).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="L’image montre le téléchargement du modèle dans le service cloud (support étendu) au sein du portail Azure.":::
  
  3. Téléchargez votre modèle et vos fichiers de paramètres. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="L’image montre le téléchargement du fichier de modèle sur le portail Azure.":::
  
  4. Copiez l’URI SAS de package et l’URI SAS de configuration à partir de l’onglet Vérifier et créer, puis ajoutez-les au fichier parameter.json. Vous pouvez désormais utiliser ces fichiers pour créer un service cloud via PowerShell.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="L’image montre les paramètres de l’URI SAS de package et de l’URI SAS de configuration dans le portail Azure.":::
  
## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [questions fréquentes (FAQ)](faq.md) concernant Cloud Services (support étendu).
- Déployer un service cloud Azure (support étendu) à l’aide du [portail Azure](deploy-portal.md)
  
