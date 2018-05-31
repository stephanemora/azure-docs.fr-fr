---
title: Développer des applications pour Azure Stack | Microsoft Docs
description: Considérations relatives au développement pour le prototypage d’applications sur Azure Stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199192"
---
# <a name="develop-for-azure-stack"></a>Développer pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez commencer à développer des applications dès aujourd’hui, même si vous n’avez pas accès à un environnement Azure Stack. Comme Azure Stack fournit des services Microsoft Azure qui s’exécutent dans votre centre de données, vous pouvez utiliser des outils et des processus similaires pour développer sur Azure Stack comme vous le feriez avec Azure. Avec un peu de préparation et à l’aide des instructions fournies dans les rubriques suivantes, vous pouvez utiliser Azure pour émuler un environnement Azure Stack.

* Dans Azure, vous pouvez créer des modèles Azure Resource Manager pouvant être déployés sur Azure Stack. Pour obtenir des conseils sur le développement de modèles pour assurer la portabilité, consultez [Considérations relatives aux modèles](azure-stack-develop-templates.md).
* Il existe des différences de disponibilité de service et de gestion des versions de services entre Azure et Azure Stack. Vous pouvez utiliser le [module de stratégie Azure Stack](azure-stack-policy-module.md) pour restreindre les types de ressources et la disponibilité de service Azure à ce qui est disponible dans Azure Stack. La restriction des services garantit que vos applications s’appuient sur les services accessibles à Azure Stack.
* Les [modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) sont des exemples de scénarios courants qui montrent comment développer des modèles pouvant être déployés sur Azure et Azure Stack.
