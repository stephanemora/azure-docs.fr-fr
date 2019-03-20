---
title: Collecter des données de pare-feu Windows dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment collecter des données de pare-feu Windows dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2356a7e5426037ffe9fc8b304ac113f4a3fe2a17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103347"
---
# <a name="connect-windows-firewall"></a>Connecter le Pare-feu Windows

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le connecteur de pare-feu Windows vous permet de connecter facilement vos journaux de pare-feu Windows, s’ils sont connectés à votre espace de travail Azure Sentinel. Cette connexion permet d’afficher des tableaux de bord, créer des alertes personnalisées et améliore l’examen. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité.  


> [!NOTE]
> 
> - Données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="enable-the-connector"></a>Activer le connecteur 

1. Dans le portail Azure Sentinel, sélectionnez **collecte des données** , puis cliquez sur le **pare-feu de Windows** vignette. 
1. Sélectionnez les types de données que vous souhaitez diffuser.
1. Cliquez sur **Installer**.

## <a name="validate-connectivity"></a>Valider la connectivité

Il peut prendre plus de 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des pare-feu de Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

