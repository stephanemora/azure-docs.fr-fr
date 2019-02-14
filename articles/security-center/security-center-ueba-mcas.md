---
title: Détection des menaces dans Azure Security Center | Microsoft Docs
description: " Détectez les menaces et les activités malveillantes grâce à l’intégration de Microsoft Cloud App Security avec Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 7ffb9684045031c5bca7a79a15db7cb16fc99e9b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108005"
---
# <a name="ueba-for-azure-resources-and-users"></a>UEBA pour les utilisateurs et ressources Azure 

Azure Security Center s’associe à Microsoft Cloud App Security pour vous fournir des alertes basées sur une analytique du comportement d’entité et de l’utilisateur (UEBA) pour vos ressources et utilisateurs Azure (activité Azure). Ces alertes détectent les anomalies de comportement de l’utilisateur et sont basées sur l’analytique du comportement d’entité et sur Machine Learning (ML) pour vous permettre d’exécuter immédiatement une détection avancée des menaces sur les activités de vos abonnements. Étant donné qu’elles sont automatiquement activées, les nouvelles détections d’anomalies fournissent des résultats immédiats en fournissant des détections immédiates, ciblant de nombreuses anomalies comportementales sur les utilisateurs et les ressources associés à votre abonnement. En outre, ces alertes tirent parti des données supplémentaires qui existent déjà dans le moteur de détection de Microsoft Cloud App Security, pour vous aider à accélérer le processus d’investigation et à contenir les menaces actives. 

> [!NOTE]
> Azure Security Center, qui peut stocker une copie des données client liées à la sécurité, collectées à partir de ou associées à une ressource client (par exemple, machine virtuelle ou locataire Azure Active Directory) : (a) dans la même zone géographique que cette ressource, sauf dans ces zones géographiques où Microsoft n’a pas encore déployé Azure Security Center, auquel cas une copie de ces données sera stockée aux États-Unis ; et (b) où Azure Security Center utilise un autre service en ligne Microsoft pour traiter ces données, ce dernier peut stocker ces données conformément aux règles de géolocalisation de cet autre service en ligne.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prérequis

- Une [licence Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security) valide et activée
- [Niveau Standard de Security Center](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>Alertes de détection des menaces

Security Center prend en charge les alertes de détection d’anomalies de Cloud App Security, telles que les suivantes :

**Voyage impossible**
-  Cette détection identifie deux activités de l’utilisateur (dans une seule ou plusieurs sessions) provenant d’emplacements éloignés sur le plan géographique au cours d’une période plus courte que la durée nécessaire à l’utilisateur pour aller du premier emplacement au second, indiquant qu’un autre utilisateur utilise les mêmes informations d’identification. Cette détection utilise un algorithme Machine Learning qui ignore les « faux positifs » évidents contribuant à la condition de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. La détection présente une période d’apprentissage initiale de 7 jours lui servant à assimiler le modèle d’activité d’un nouvel utilisateur.

**Activité à partir de pays peu fréquents**
- Cette détection prend en compte les emplacements d’activité précédents pour déterminer les emplacements nouveaux et peu fréquents. Le moteur de détection d’anomalies stocke des informations sur les emplacements précédents utilisés par les utilisateurs de l’organisation. Une alerte est déclenchée lorsqu’une activité se produit à partir d’un emplacement qui n’a pas été récemment ou qui n’a jamais été visité par un utilisateur de l’organisation. 

**Activité depuis des adresses IP anonymes**
- Cette détection identifie que les utilisateurs étaient actifs depuis une adresse IP qui a été identifiée comme une adresse IP de proxy anonyme. Ces proxys sont utilisés par des individus souhaitant masquer l’adresse IP de leur appareil et peuvent être utilisés dans un but malveillant. Cette détection utilise un algorithme Machine Learning qui réduit les « faux positifs », tels que les adresses IP mal étiquetées qui sont largement utilisées par d’autres utilisateurs de l’organisation.
 
  ![alerte de détection des menaces](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>Désactivation des alertes de détection des menaces

Ces alertes sont activées par défaut, mais vous pouvez les désactiver :

1. Dans le panneau Security Center, sélectionnez **Stratégie de sécurité**. Pour l’abonnement que vous souhaitez modifier, cliquez sur **Modifier les paramètres**.
2.  Cliquez sur **Détection des menaces**.
3. Sous **Activer les intégrations**, décochez la case **Autoriser Microsoft Cloud App Security à accéder à mes données**, puis cliquez sur **Enregistrer**.

   ![alerte de détection des menaces](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> Il existe une période d’apprentissage initiale de 7 jours, pendant laquelle toutes les alertes de détection d’anomalies ne sont pas déclenchées. Ensuite, chaque session est comparée à l’activité (quand les utilisateurs étaient actifs, les adresses IP, les appareils, etc.) détectée au cours du mois précédent et au score de risque de ces activités. Ces détections font partie du moteur de détection des anomalies Machine Learning que dresse le profil de votre environnement et déclenche des alertes par rapport à une ligne de base qui a été apprise sur l’activité de votre organisation. Ces détections utilisent également des algorithmes Machine Learning conçus pour dresser le profil des utilisateurs et le modèle de connexion afin de réduire les faux positifs.
>
  
## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment utiliser la détection des menaces dans Azure Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
