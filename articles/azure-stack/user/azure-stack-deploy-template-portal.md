---
title: Déployer des modèles à l’aide du portail dans Azure Stack | Microsoft Docs
description: Apprenez à utiliser le portail Azure Stack pour déployer des modèles.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4013b7136fd365b1d37348cfc1f0b46cce787ce2
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363121"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Déployer des modèles à l’aide du portail Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser le portail pour déployer des modèles Azure Resource Manager dans Azure Stack.

## <a name="to-deploy-a-template"></a>Pour déployer un modèle

1. Connectez-vous au portail, sélectionnez **+ Créer une ressource**, puis sélectionnez **Personnalisé**.
2. Sélectionnez **Déploiement de modèle**.
3. Sélectionnez **Modifier un modèle**, puis collez le code de votre modèle JSON dans la fenêtre de code. Sélectionnez **Enregistrer**.
4. Sélectionnez **Modifier les paramètres**, indiquez des valeurs pour les paramètres affichés, puis sélectionnez **OK**.
5. Sélectionnez **Abonnement**. Choisissez l’abonnement que vous voulez utiliser, puis sélectionnez **OK**.
6. Sélectionnez **Groupe de ressources**. Choisissez un groupe de ressources existant ou créez-en un, puis sélectionnez **OK**.
7. Sélectionnez **Créer**. Le tableau de bord contient maintenant une nouvelle vignette qui suit la progression du déploiement de votre modèle.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement de modèles, consultez :

[Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
