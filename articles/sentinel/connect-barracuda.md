---
title: Connecter des données Barracuda à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Barracuda à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 33953c3a6b4244ec50ca5b1505b6d48621527d64
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620548"
---
# <a name="connect-your-barracuda-appliance"></a>Connecter votre appliance Barracuda 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le connecteur du pare-feu d’applications web (WAF) Barracuda vous permet de connecter facilement vos journaux Barracuda à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité. Azure Sentinel tire parti de l’intégration native entre **Barracuda** et Microsoft Monitoring Agent afin de fournir une intégration fluide. 


> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configurer et connecter le pare-feu d’applications web Barracuda
Le pare-feu d’applications web Barracuda peut intégrer et exporter des journaux directement dans Azure Sentinel via Microsoft Monitoring Agent.
1. Accédez à [Flux de configuration du pare-feu d’applications web Barracuda](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/) et suivez les instructions pour configurer la connexion à l’aide de ces paramètres :
    - **ID de l’espace de travail** : copiez la valeur de l’ID de votre espace de travail à partir de la page du connecteur Azure Sentinel Barracuda.
    - **Clé primaire** : copiez la valeur de votre clé primaire à partir de la page du connecteur Azure Sentinel Barracuda.
2. Dans le portail Azure Sentinel, accédez à l’espace de travail sur lequel vous avez déployé Azure Sentinel et sélectionnez les points de suspension (...) à la fin de la ligne, puis **Paramètres avancés**. 
1. Sélectionnez **Données**, puis **Syslog**.
1. Veillez à ce que l’installation dans laquelle vous définissez Barracuda existe, définissez la gravité puis cliquez sur **Enregistrer**.
6. Pour utiliser le schéma approprié dans Log Analytics pour les événements Barracuda, recherchez **CommonSecurityLog** et **barracuda_CL**.


## <a name="validate-connectivity"></a>Valider la connectivité

Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances Barracuda à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

