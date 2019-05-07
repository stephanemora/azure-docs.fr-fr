---
title: Connectez les données de Barracuda pour Azure en version préliminaire Sentinel | Microsoft Docs
description: Découvrez comment connecter les données Barracuda pour Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6b0285903537dafb004b5aca033b50560247c605
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204459"
---
# <a name="connect-your-barracuda-appliance"></a>Se connecter à votre appliance Barracuda 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connecteur de pare-feu d’applications Web (WAF) Barracuda vous permet de connecter facilement vos journaux Barracuda avec votre Azure Sentinel, pour afficher des tableaux de bord, créer des alertes personnalisées et améliore l’examen. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité. Sentinel Azure tire parti de l’intégration native entre **Barracuda** et Microsoft Azure OMS pour fournir une intégration transparente. 


> [!NOTE]
> Données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configurer et connecter WAF Barracuda
Pare-feu d’applications Web Barracuda peut intégrer et exporter les journaux directement vers Azure Sentinel via le serveur Azure OMS.
1. Accédez à [flux de configuration WAF Barracuda](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)et suivez les instructions pour configurer la connexion à l’aide de ces paramètres :
    - **ID de l’espace de travail**: copiez la valeur de votre ID d’espace de travail à partir de la page du connecteur Azure Sentinel Barracuda.
    - **Clé primaire**: copiez la valeur de votre clé primaire à partir de la page du connecteur Azure Sentinel Barracuda.
2. Dans le portail Azure Sentinel, accédez à l’espace de travail sur lequel vous avez déployé Azure Sentinel et sélectionnez les points de suspension (...) à la fin de la ligne et sélectionnez **paramètres avancés**. 
1. Sélectionnez **données** , puis **Syslog**.
1. Vérifiez que la fonctionnalité que vous définissez dans Barracuda existe et définie le niveau de gravité et cliquez sur **enregistrer**.
6. Pour utiliser le schéma pertinent dans Analytique de journal pour les événements de Barracuda, recherchez **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Valider la connectivité

Il peut prendre plus de 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des appliances de Barracuda pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

