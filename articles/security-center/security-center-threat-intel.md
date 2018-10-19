---
title: Threat intelligence et carte des alertes de sécurité dans Azure Security Center | Microsoft Docs
description: Découvrez comment utiliser les fonctionnalités de carte des alertes de sécurité et Threat Intelligence dans Azure Security Center pour identifier les menaces potentielles sur vos machines virtuelles et ordinateurs.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: d82d52e05d85fbd5197f84f032a55dd9c74f9d1f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714426"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Carte des alertes de sécurité et informations sur les menaces
Cet article vous aide à utiliser la carte des alertes de sécurité et la carte Threat Intelligence basée sur les événements de sécurité d’Azure Security Center pour résoudre les problèmes liés à la sécurité.

## <a name="how-the-security-alerts-map-works"></a>Fonctionnement de la carte des alertes de sécurité
Security Center vous fournit une carte qui vous aide à identifier les menaces de sécurité qui pèsent sur l’environnement. Par exemple, vous pouvez déterminer si un ordinateur particulier fait partie d’un botnet et connaître l’origine de la menace. Un ordinateur peut devenir un nœud d’un botnet si un attaquant installe de manière illicite un programme malveillant qui interagit secrètement avec les commandes et contrôles qui gèrent le botnet. 

Pour générer cette carte, Security Center utilise des données provenant de nombreuses sources au sein de Microsoft. Security Center utilise ces données afin d’établir la carte des menaces potentielles qui pèsent sur votre environnement. 

Le [processus de réponse aux incidents de sécurité](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) contient une étape consistant à identifier la gravité du ou des systèmes compromis. Pendant cette phase, vous devez effectuer les tâches suivantes :

- Déterminer la nature de l’attaque.
- Déterminer le point d’origine de l’attaque.
- Déterminer le but de l’attaque L’attaque était-elle dirigée contre votre entreprise pour obtenir certaines informations à son sujet ou était-elle aléatoire ?
- Identifier les systèmes qui ont été compromis.
- Identifier les fichiers consultés et déterminer leur sensibilité.

Vous pouvez vous aider de la carte des alertes de sécurité de Security Center pour effectuer ces tâches.

## <a name="access-the-security-alerts-map"></a>Accès à la carte des alertes de sécurité
Pour visualiser les menaces qui pèsent actuellement sur votre environnement, ouvrez la carte des alertes de sécurité :

1. Ouvrez le tableau de bord **Security Center**.
2. Dans le volet gauche, sous **Protection contre les menaces**, sélectionnez **Carte des alertes de sécurité**. La carte s’ouvre.
3. Pour obtenir plus d’informations sur l’alerte et recevoir les étapes de correction à suivre, cliquez sur le point d’alerte sur la carte et suivez les instructions. 
 
La carte des alertes de sécurité est basée sur les alertes. Ces alertes sont basées sur les activités pour lesquelles une communication réseau a été associée à une adresse IP qui a été résolue, afin de déterminer si l’adresse IP est une adresse IP à risque connue (par exemple, un cryptomineur connu) ou une adresse IP qui n’est pas reconnue comme étant risquée. La carte fournit des alertes sur les abonnements que vous avez sélectionnés précédemment dans Azure. 

Les alertes figurant sur la carte sont affichées en fonction de l’emplacement géographique de leur origine détectée ; elles sont également à code de couleurs par niveau de gravité. 
    ![Informations Threat Intelligence](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Affichage du tableau de bord Threat Intelligence basé sur les événements
Pour afficher la carte Threat Intelligence basée sur les événements de sécurité bruts, vous pouvez suivre cette procédure. Cette carte affiche uniquement les événements qui impliquent une adresse IP considérée comme risquée, par exemple une adresse IP d’un botnet connu.

1. Ouvrez le tableau de bord **Security Center**.

1. Dans le volet gauche, sous **Protection contre les menaces**, sélectionnez **Carte des alertes de sécurité**. La carte s’ouvre.
2. Dans l’angle supérieur droit, cliquez sur **Accéder à la carte des événements de sécurité**.
3. Sélectionnez l’espace de travail pour lequel vous souhaitez afficher le tableau de bord.
4. En haut de la carte, sélectionnez **Afficher Threat intelligence classique**. Le tableau de bord **Threat intelligence** s’affiche.

   > [!NOTE]
   > Si la colonne située complètement à droite affiche **PLAN DE MISE À NIVEAU**, cet espace de travail utilise l’abonnement gratuit. Effectuez une mise à niveau vers le niveau Standard pour utiliser cette fonctionnalité. Si la colonne située complètement à droite affiche **MISE À JOUR REQUISE**, mettez à jour [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour utiliser cette fonctionnalité. Pour en savoir plus sur le plan de tarification, consultez la tarification dans Azure Security Center.
   >
5. Si vous avez plusieurs espaces de travail à examiner, hiérarchisez l’examen en fonction de la colonne **Adresse IP malveillante**. Elle indique le nombre actuel d’adresses IP malveillantes dans cet espace de travail. Sélectionnez l’espace de travail que vous souhaitez utiliser. Le tableau de bord **Informations sur les menaces** s’affiche.

    ![Informations sur les menaces](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. Le tableau de bord est divisé en quatre vignettes :

    a.  **Types de menaces**. récapitule les types de menaces détectés dans l’espace de travail sélectionné.

    b.  **Pays d’origine**. Agrège la quantité de trafic en fonction de l’emplacement d’origine.

    c.  **Emplacement de la menace**. vous aide à identifier les emplacements dans le monde qui communiquent actuellement avec votre environnement. Dans le mappage indiqué, les flèches orange (entrant) et rouges (sortant) identifient le sens du trafic. Sélectionnez une de ces flèches pour afficher le type de menace et la direction du trafic.

    d.  **Détails de la menace**. Montre plus de détails sur les menaces que vous avez sélectionnées dans le mappage.

Quelle que soit la vignette d’option sélectionnée, le tableau de bord qui s’affiche se base sur la requête de recherche dans les journaux. Les seules différences sont le type de requête et le résultat.

### <a name="threat-types"></a>Types de menaces
Sélectionnez la vignette **Types de menace** pour ouvrir le tableau de bord **Recherche dans les journaux**. Les options de filtre s’affichent à gauche, et les résultats de la requête à droite.

![Recherche dans les journaux](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

Le résultat de la requête affiche les menaces par nom. Vous pouvez utiliser le volet de gauche pour sélectionner l’attribut que vous souhaitez filtrer. Par exemple, pour afficher uniquement les menaces qui sont actuellement connectées aux machines, dans **SESSIONSTATE**, sélectionnez **Connecté** > **Appliquer**.

![État de la session](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Pour les machines virtuelles Azure, le tableau de bord **Informations sur les menaces** n’affiche que les données de réseau filtrées par l’agent. Les types de données suivants sont également utilisés par les informations sur les menaces :

- Données CEF (Type = CommonSecurityLog)
- WireData (Type = WireData)
- Journaux IIS (Type = W3CIISLog)
- Pare-feu Windows (Type = WindowsFirewall)
- Événements DNS (Type = DnsEvents)


## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez découvert comment utiliser les informations sur les menaces dans Security Center pour vous aider à identifier une activité suspecte. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Comprendre les alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
