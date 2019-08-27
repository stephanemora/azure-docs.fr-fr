---
title: Gérer les alertes de sécurité dans le centre de sécurité Azure | Microsoft Docs
description: Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516094"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à utiliser Azure Security Center afin de gérer et résoudre les alertes de sécurité.

> [!NOTE]
> Pour activer la détection avancée, effectuez une mise à niveau vers Azure Security Center Standard. Un essai gratuit est disponible. Pour mettre à niveau, sous [Stratégie de sécurité](tutorial-security-policy.md), sélectionnez Niveau tarifaire. Consultez [Tarification d’Azure Security Center](security-center-pricing.md) pour en savoir plus.
>
>

## <a name="what-are-security-alerts"></a>Que sont les alertes de sécurité ?
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.


> [!NOTE]
> Pour plus d’informations sur le fonctionnement des fonctionnalités de détection de Security Center, consultez [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Gestion des alertes de sécurité
Vous pouvez connaître vos alertes actuelles en consultant la vignette **Alertes de sécurité** . Suivez les étapes ci-après pour obtenir plus d’informations sur chaque alerte :

1. La mosaïque **Alertes de sécurité** s’affiche dans le tableau de bord de Security Center.

    ![Vignette Alertes de sécurité dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Cliquez sur la vignette pour ouvrir le panneau **Alertes de sécurité** et afficher des détails supplémentaires sur les alertes.

   ![Panneau Alertes de sécurité dans Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Les détails de chaque alerte sont affichés au bas de la page. Pour les organiser à votre convenance, cliquez sur la colonne que vous voulez trier. La définition de chaque colonne est indiquée ci-dessous :

* **Description** : brève explication de l’alerte.
* **Nombre** : liste de toutes les alertes d’un type spécifique qui ont été détectées un jour précis.
* **Détectée par** : service à l’origine du déclenchement de l’alerte.
* **Date** : date à laquelle l’événement s’est produit.
* **État** : état actuel de l’alerte. Il existe deux types d’état :
  * **Active** : l’alerte de sécurité a été détectée.
  * **Ignorée** : l’alerte de sécurité a été ignorée par l’utilisateur. Cet état est généralement utilisé pour des alertes qui ont été examinées et atténuées, ou qui ne correspondent pas à une attaque réelle.
* **Gravité** : niveau de gravité (Haute, Moyenne ou Faible).

> [!NOTE]
> Les alertes de sécurité générés par Security Center seront également affichées dans le journal d’activité Azure. Pour plus d’informations sur l’accès au journal d’activité Azure, consultez [Afficher les journaux d’activité pour auditer les actions sur les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Gravité des alertes

-   **Haute** : il est fort probable que votre ressource ait été compromise. Vous devez étudier le problème immédiatement. Security Center est très confiant quant à l’intention malveillante et aux constats à l’origine de l’alerte. Par exemple, une alerte qui détecte l’exécution d’un outil malveillant connu, tel que Mimikatz, un outil couramment utilisé pour dérober des informations d’identification. 
-   **Moyenne** : il s’agit probablement d’une activité suspecte pouvant indiquer qu’une ressource est compromise.
La confiance de Security Center dans l’analyse et le constat est moyenne, et la confiance quant à l’intention malveillante est moyenne à élevée. Il s’agit généralement de détections basées sur des anomalies ou l’apprentissage automatique. Par exemple, une tentative de connexion depuis un emplacement anormal.
-   **Faible** : cela peut être un positif sans gravité ou une attaque bloquée. 
    - Security Center n’est pas suffisamment confiant sur le fait que l’intention soit malveillante, et l’activité peut être innocente. Par exemple, l’effacement des journaux est une action qui peut se produire lorsqu’un pirate tente de masquer ses traces mais, dans de nombreux cas, il s’agit d’une opération de routine effectuée par les administrateurs.
    - Security Center ne vous indique généralement pas les blocages d’attaques, sauf s’il s’agit d’un cas intéressant que nous vous suggérons d’examiner. 
-   **Informations** : vous voyez les alertes informatives seulement lorsque vous explorez en profondeur un incident de sécurité, ou si vous utilisez l’API REST avec un ID d’alerte spécifique. Un incident est généralement constitué de plusieurs d’alertes, dont certaines peuvent apparaître séparément comme informatives uniquement mais, dans le contexte des autres alertes, mériter un examen plus approfondi.  

> [!NOTE]
> Si vous utilisez la version d’API **2015-06-01-preview**, il existe des différences d’application des types de gravité des alarmes en fonction des scénarios, par rapport à ceux qui sont indiqués ci-dessus.  

### <a name="filtering-alerts"></a>Filtrage des alertes
Vous pouvez filtrer les alertes en fonction de la date, de l’état et du niveau de gravité. Le filtrage des alertes peut être utile quand vous avez besoin de restreindre le nombre d’alertes de sécurité qui s’affichent. Supposons que vous souhaitiez vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.

1. Cliquez sur **Filtrer** dans le panneau **Alertes de sécurité**. Dans le panneau **Filtrer** qui s’ouvre, vous pouvez sélectionner la date, l’état et les niveaux de gravité que vous souhaitez visualiser.

    ![Filtrage des alertes dans le Centre de sécurité](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Répondre à des alertes de sécurité
Sélectionnez une alerte de sécurité pour en savoir plus sur les événements qui l’ont déclenchée et, le cas échéant, les étapes à suivre pour y remédier. Les alertes de sécurité sont regroupées par type et date d’apparition. Le fait de cliquer sur une alerte de sécurité ouvre une page contenant une liste des alertes groupées.

![Répondre aux alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Dans ce cas, les alertes qui ont été déclenchées concernent une activité suspecte utilisant le protocole RDP. Les première, deuxième, troisième, quatrième et cinquième colonnes affichent respectivement les ressources qui ont été attaquées, le nombre de fois où la ressource a été attaquée, l’heure de l’attaque, l’état de l’alerte et le niveau de gravité de l’attaque. Après avoir examiné ces informations, cliquez sur la ressource qui a été attaquée.

![Suggestions sur la façon de traiter les alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

Vous trouverez plus d’informations sur l’événement dans le champ **Description**. Ces informations permettent d’en savoir plus sur ce qui a déclenché l’alerte de sécurité, sur la ressource cible, sur l’adresse IP source (le cas échéant) et sur la manière de remédier au problème.  Dans certains cas, l’adresse IP source est vide (non disponible), car certains journaux d’activité d’événements Windows de la sécurité n’incluent pas l’adresse IP.

La correction suggérée par Security Center dépend de l’alerte de sécurité. Dans certains cas, vous pouvez être amené à utiliser d’autres fonctionnalités Azure pour appliquer la correction recommandée. Par exemple, pour cette attaque, la correction consiste à ne pas autoriser l’adresse IP à l’origine de l’attaque à l’aide d’une règle de [liste de contrôle d’accès (ACL) réseau](../virtual-network/virtual-networks-acl.md) ou de [groupe de sécurité réseau](../virtual-network/security-overview.md#security-rules). Pour plus d’informations sur les différents types d’alertes, consultez l’article [Types d’alertes de sécurité](security-center-alerts-overview.md#security-alert-types).

> [!NOTE]
> Security Center a sorti avec les versions préliminaires limitées un nouvel ensemble de moyens de détection qui exploitent les enregistrements d’audit, une infrastructure d’audit courante, pour détecter des comportements malveillants sur les machines Linux. [Envoyez-nous](mailto:ASC_linuxdetections@microsoft.com) un e-mail avec vos ID d’abonnement pour joindre la version préliminaire.


## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Gestion des incidents de sécurité dans Azure Security Center](security-center-incident.md)
* [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md)
* [Guide des opérations et de planification du Centre de sécurité Azure](security-center-planning-and-operations-guide.md)
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
