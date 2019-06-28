---
title: Connecter des données DNS dans Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données DNS dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6429568b33ece3ed4f26614e55e8c3069dd65d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204406"
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

## <a name="how-it-works"></a>Fonctionnement

La connexion DNS s’effectue en installant un agent sur l’ordinateur DNS. L’agent extrait les événements à partir du DNS et les transmet à Log Analytics.

## <a name="connect-your-dns-appliance"></a>Connexion à votre appliance DNS

1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données** et choisissez la vignette **DNS**.
1. Si vos ordinateurs DNS se trouvent dans Azure :
    1. Cliquez sur **Download & install agent for Windows virtual machines (Télécharger et installer l’agent pour les machines virtuelles Windows)** .
    1. Dans la liste **Machines virtuelles**, sélectionnez l’ordinateur DNS que vous souhaitez diffuser en continu dans Azure Sentinel. Assurez-vous qu’il s’agit d’une machine virtuelle Windows.
    1. Dans la fenêtre qui s’ouvre pour cette machine virtuelle, cliquez sur **Connecter**.  
    1. Cliquez sur **Activer** dans la fenêtre **Connecteur DNS**. 

2. Si votre ordinateur DNS n’est pas une machine virtuelle Azure :
    1. Cliquez sur **Download & install agent for Windows non-Azure machines (Télécharger et installer l’agent pour les ordinateurs non Azure Windows)** .
    1. Dans la fenêtre **Agent direct**, sélectionnez **Télécharger l’agent Windows (64 bits)** ou **Télécharger l’agent Windows (32 bits)** .
    1. Installez l’agent sur votre ordinateur DNS. Copiez l’**ID de l’espace de travail**, la **clé primaire** et la **clé secondaire**, et utilisez-les lorsque vous y êtes invité pendant l’installation.

3. Pour utiliser le schéma pertinent dans Log Analytics pour les journaux DNS, recherchez **DnsEvents**.

## <a name="validate"></a>Valider 

Dans Log Analytics, recherchez le schéma **DnsEvents** et assurez-vous qu’il existe des événements.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances locales DNS à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
