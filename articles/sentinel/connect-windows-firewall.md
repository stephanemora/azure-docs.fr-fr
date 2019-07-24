---
title: Connecter des données de pare-feu Windows à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données de pare-feu Windows à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 09e63612d6e0e70b1bb21c23b158f650d4c34080
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190602"
---
# <a name="connect-windows-firewall"></a>Connecter le Pare-feu Windows

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le connecteur de pare-feu Windows vous permet de connecter facilement les journaux d’activité du pare-feu Windows, s’ils sont connectés à votre espace de travail Azure Sentinel. Cette connexion vous permet d’afficher des tableaux de bord, de créer des alertes personnalisées et d’améliorer les investigations. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité. La solution collecte les événements de pare-feu Windows à partir des machines Windows sur lesquelles un agent Log Analytics est installé. 


> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="enable-the-connector"></a>Activer le connecteur 

1. Dans le portail Azure Sentinel, sélectionnez **Data connectors** (Connecteurs de données), puis cliquez sur la vignette **Pare-feu Windows**. 
1.  Si vos machines Windows se trouvent dans Azure :
    1. Cliquez sur **Installer l’agent sur la machine de virtuelle Windows Azure**.
    1. Dans la liste **Machines virtuelles**, sélectionnez la machine Windows que vous souhaitez diffuser en continu dans Azure Sentinel. Assurez-vous qu’il s’agit d’une machine virtuelle Windows.
    1. Dans la fenêtre qui s’ouvre pour cette machine virtuelle, cliquez sur **Connecter**.  
    1. Cliquez sur **Activer** dans la fenêtre **Connecteur de pare-feu Windows**. 

2. Si votre machine Windows n’est pas une machine virtuelle Azure :
    1. Cliquez sur **Installer l’agent sur les machines non Azure**.
    1. Dans la fenêtre **Agent direct**, sélectionnez **Télécharger l’agent Windows (64 bits)** ou **Télécharger l’agent Windows (32 bits)** .
    1. Installez l’agent sur votre machine Windows. Copiez l’**ID de l’espace de travail**, la **clé primaire** et la **clé secondaire**, et utilisez-les lorsque vous y êtes invité pendant l’installation.

4. Sélectionnez les types de données que vous souhaitez transmettre en continu.
5. Cliquez sur **Installer la solution**.
6. Pour utiliser le schéma pertinent dans Log Analytics pour le pare-feu Windows, recherchez **SecurityEvent**.

## <a name="validate-connectivity"></a>Valider la connectivité

Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter le pare-feu Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

