---
title: Connecter des données d’événement de sécurité de Windows vers Azure Sentinel Preview | Microsoft Docs
description: Découvrez comment connecter les données d’événement de sécurité de Windows à Azure Sentinel.
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
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233973"
---
# <a name="connect-windows-security-events"></a>Connecter les événements de sécurité Windows 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
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

1. Dans le portail Azure Sentinel, sélectionnez **connecteurs de données** , puis cliquez sur le **les événements de sécurité Windows** vignette. 
1. Sélectionnez les types de données que vous souhaitez diffuser.
1. Cliquez sur **Update**.
6. Pour utiliser le schéma pertinent dans Analytique de journal pour les événements de sécurité Windows, recherchez **SecurityEvent**.

## <a name="validate-connectivity"></a>Valider la connectivité

Il peut prendre environ 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les événements de sécurité Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

