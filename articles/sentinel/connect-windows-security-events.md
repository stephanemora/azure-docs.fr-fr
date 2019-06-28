---
title: Connecter des données d’événement de sécurité Windows à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données d’événement de sécurité Windows à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 88b066818d53fd92e8238e270b9bc785d4275186
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233973"
---
# <a name="connect-windows-security-events"></a>Connecter les événements de sécurité Windows 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez transmettre en continu tous les événements de sécurité des serveurs Windows connectés à votre espace de travail Azure Sentinel. Cette connexion vous permet d’afficher des tableaux de bord, de créer des alertes personnalisées et d’améliorer les investigations. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité.  Vous pouvez sélectionner les événements à transmettre en continu :

- **Tous les événements** : tous les événements AppLocker et de sécurité Windows.
- **Courant** : un ensemble d’événements à des fins d’audit.
- **Minimal** : un petit ensemble d’événements qui peuvent indiquer des menaces potentielles. En activant cette option, vous ne pourrez pas avoir de piste d’audit complète.
- **Aucun** : aucun événement AppLocker ni de sécurité.

> [!NOTE]
> 
> - Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurer le connecteur d’événements de sécurité Windows

Pour intégrer complètement vos événements de sécurité Windows à Azure Sentinel :

1. Dans le portail Azure Sentinel, sélectionnez **Data connectors** (Connecteurs de données), puis cliquez sur la vignette **Événements de sécurité Windows**. 
1. Sélectionnez les types de données que vous souhaitez transmettre en continu.
1. Cliquez sur **Update**.
6. Pour utiliser le schéma pertinent dans Log Analytics pour les événements de sécurité Windows, recherchez **SecurityEvent**.

## <a name="validate-connectivity"></a>Valider la connectivité

Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les événements de sécurité Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

