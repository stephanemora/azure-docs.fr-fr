---
title: Connectez les données DNS dans Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment connecter les données DNS dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e34db2bdc78eb846cf4885b1ef083fd3b21e21b5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792049"
---
# <a name="connect-your-domain-name-server"></a>Se connecter à votre serveur de noms de domaine

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez vous connecter n’importe quel serveur DNS (Domain Name) s’exécutant sur Windows dans Azure Sentinel. Pour cela, vous devez installer un agent sur l’ordinateur DNS. À l’aide de DNS se connecte, vous pouvez obtenir des insights de relatives aux opérations dans l’infrastructure DNS de votre organisation, les performances et la sécurité en collectant, analyse, et mettre en corrélation des analytiques et les journaux d’audit et autres des données liées à partir des serveurs DNS.

Lorsque vous activez la connexion du journal DNS, vous pouvez :
- Identifier les clients qui tentent de résoudre des noms de domaine malveillant
- Identifier les enregistrements de ressources obsolètes
- Identifier les noms de domaine fréquemment interrogés et les clients DNS communiquant
- Afficher la charge de la demande sur les serveurs DNS
- Afficher les échecs d’inscription DNS dynamique

## <a name="how-it-works"></a>Fonctionnement

Connexion de DNS s’effectue en installant un agent sur l’ordinateur DNS. L’agent extrait les événements à partir du DNS et les transmet à l’Analytique de journal.

## <a name="connect-your-dns-appliance"></a>Se connecter à votre appliance de DNS

1. Dans le portail Azure Sentinel, sélectionnez **connecteurs de données** et choisissez le **DNS** vignette.
1. Si vos machines DNS se trouvent dans Azure :
    1. Cliquez sur **télécharger et installer l’agent pour les machines virtuelles Windows**.
    1. Dans le **machines virtuelles** , sélectionnez l’ordinateur DNS que vous souhaitez diffuser en continu dans Azure Sentinel. Assurez-vous qu’il s’agit d’une machine virtuelle Windows.
    1. Dans la fenêtre qui s’ouvre pour cette machine virtuelle, cliquez sur **Connect**.  
    1. Cliquez sur **activer** dans le **connecteur DNS** fenêtre. 

2. Si votre ordinateur DNS n’est pas une machine virtuelle Azure :
    1. Cliquez sur **télécharger et installer l’agent pour les ordinateurs non Azure de Windows**.
    1. Dans le **agent Direct** fenêtre, sélectionnez **agent de téléchargement de Windows (64 bits)** ou **agent de téléchargement de Windows (32 bits)**.
    1. Installez l’agent sur votre ordinateur DNS. Copie le **ID de l’espace de travail**, **clé primaire**, et **clé secondaire** et les utiliser lorsque vous y êtes invité pendant l’installation.

3. Pour utiliser le schéma pertinent dans Analytique de journal pour les journaux DNS, recherchez **DnsEvents**.

## <a name="validate"></a>Valider 

Analytique de journal, recherchez le schéma **DnsEvents** et assurez-vous qu’il existe des événements.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des appliances locales DNS à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).
