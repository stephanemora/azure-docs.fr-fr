---
title: Collecter les données d’événement de sécurité Windows dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment collecter les données d’événement de sécurité Windows dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3c79747bf33e1769af5f8d3589904ba15105f216
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087599"
---
# <a name="connect-windows-security-events"></a>Connecter les événements de sécurité Windows 

> [!IMPORTANT]
> Sentinel Azure est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez diffuser tous les événements de sécurité des serveurs Windows connectés à votre espace de travail Azure Sentinel. Cette connexion permet d’afficher des tableaux de bord, créer des alertes personnalisées et améliore l’examen. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité.  Vous pouvez sélectionner les événements à diffuser en continu :

- **Tous les événements** -sécurité de tous les Windows et les événements AppLocker.
- **Common** -un ensemble standard d’événements à des fins d’audit.
- **Minimale** -un petit ensemble d’événements qui peuvent indiquer des menaces potentielles. En activant cette option, vous ne pourrez pas avoir une piste d’audit complète.
- **Aucun** -aucune sécurité ni les événements AppLocker.

> [!NOTE]
> 
> - Données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurer le connecteur d’événements de sécurité Windows

Pour intégrer complètement vos événements de sécurité Windows avec Azure Sentinel :

1. Dans le portail Azure Sentinel, sélectionnez **collecte des données** , puis cliquez sur le **les événements de sécurité Windows** vignette. 
1. Sélectionnez les types de données que vous souhaitez diffuser.
1. Cliquez sur **Update**.


## <a name="validate-connectivity"></a>Valider la connectivité

Il peut prendre environ 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les événements de sécurité Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

