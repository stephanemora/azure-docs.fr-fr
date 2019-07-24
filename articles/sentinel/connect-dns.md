---
title: Connecter des données DNS dans Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données DNS dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 1c79aad557efb85a8797584c33c74983ef645d07
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611312"
---
# <a name="connect-your-domain-name-server"></a>Connecter votre serveur de noms de domaine

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez vous connecter n’importe quel serveur DNS (Domain Name Server) s’exécutant sous Windows à Azure Sentinel. Pour cela, vous devez installer un agent sur l’ordinateur DNS. À l’aide des journaux DNS, vous pouvez obtenir des informations sur la sécurité, les performances et les opérations dans l’infrastructure DNS de votre organisation, en collectant, analysant et mettant en corrélation des analyses et des journaux d’audit, ainsi que d’autres des données connexes des serveurs DNS.

Lorsque vous activez la connexion du journal DNS, vous pouvez :
- Identifier les clients qui tentent de résoudre des noms de domaine malveillant
- Identifier les enregistrements de ressources obsolètes
- Identifier les noms de domaine fréquemment interrogés et les clients DNS communiquant
- Afficher la charge de la demande sur les serveurs DNS
- Afficher les échecs d’inscription DNS dynamique

## <a name="connected-sources"></a>Sources connectées

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution :

| **Source connectée** | **Support** | **Description** |
| --- | --- | --- |
| [Agents Windows](../azure-monitor/platform/agent-windows.md) | OUI | La solution collecte des informations DNS auprès d’agents Windows. |
| [Agents Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | Non | La solution ne collecte aucune information DNS auprès d’agents Linux directs. |
| [Groupe d’administration de Microsoft System Center Operations Manager](../azure-monitor/platform/om-agents.md) | OUI | La solution collecte des informations DNS auprès d’agents dans un groupe d’administration d’Operations Manager connecté. Une connexion directe entre l’agent Operations Manager et Azure Monitor n’est pas obligatoire. Les données sont transférées du groupe d’administration à l’espace de travail Log Analytics. |
| [Compte Azure Storage](../azure-monitor/platform/collect-azure-metrics-logs.md) | Non | Le stockage Azure n’est pas utilisé par la solution. |

### <a name="data-collection-details"></a>Détails sur la collecte de données

La solution collecte des données liées aux inventaires DNS et aux événements DNS sur les serveurs DNS sur lesquels un agent Log Analytics est installé. Les données liées aux inventaires, comme le nombre de serveurs, de zones et d’enregistrements de ressources DNS, sont collectées en exécutant les applets de commande PowerShell de DNS. Les données sont mises à jour tous les deux jours. Les données liées aux événements sont collectées quasiment en temps réel à partir des [journaux d’analyse et d’audit](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fournis par le biais de la fonctionnalité améliorée de diagnostics et de journalisation DNS de Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Connexion à votre appliance DNS

1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données** et choisissez la vignette **DNS**.
1. Si vos ordinateurs DNS se trouvent dans Azure :
    1. Cliquez sur **Installer l’agent sur la machine de virtuelle Windows Azure**.
    1. Dans la liste **Machines virtuelles**, sélectionnez l’ordinateur DNS que vous souhaitez diffuser en continu dans Azure Sentinel. Assurez-vous qu’il s’agit d’une machine virtuelle Windows.
    1. Dans la fenêtre qui s’ouvre pour cette machine virtuelle, cliquez sur **Connecter**.  
    1. Cliquez sur **Activer** dans la fenêtre **Connecteur DNS**. 

2. Si votre ordinateur DNS n’est pas une machine virtuelle Azure :
    1. Cliquez sur **Installer l’agent sur les machines non Azure**.
    1. Dans la fenêtre **Agent direct**, sélectionnez **Télécharger l’agent Windows (64 bits)** ou **Télécharger l’agent Windows (32 bits)** .
    1. Installez l’agent sur votre ordinateur DNS. Copiez l’**ID de l’espace de travail**, la **clé primaire** et la **clé secondaire**, et utilisez-les lorsque vous y êtes invité pendant l’installation.

3. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux DNS, recherchez **DnsEvents**.

## <a name="validate"></a>Valider 

Dans Log Analytics, recherchez le schéma **DnsEvents** et assurez-vous qu’il existe des événements.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances locales DNS à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
