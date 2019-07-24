---
title: Corrélation des alertes intelligentes cloud dans Azure Security Center (incidents) | Microsoft Docs
description: Cette rubrique explique comment la fusion utilise la corrélation des alertes intelligentes cloud pour générer des incidents de sécurité dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295858"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Corrélation des alertes intelligentes cloud dans Azure Security Center (incidents)

Security Center analyse continuellement les charges de travail de cloud hybride à l’aide d’une analytique avancée et d’informations sur les menaces pour vous avertir des activités malveillantes.

L’augmentation de l’étendue de la couverture contre les menaces et la nécessité croissante de détecter la moindre indication d’une compromission compliquent pour les analystes de sécurité le tri des différentes alertes et l’identification d’une attaque réelle. Security Center aide les analystes à faire face à la charge des alertes et à diagnostiquer les attaques à mesure qu’elles se produisent en mettant en corrélation les différentes alertes et les signaux faibles dans des incidents de sécurité.

La fusion constitue le back-end technologique et analytique sur lequel reposent les incidents Security Center, permettant la mise en corrélation des différentes alertes et signaux contextuels. La fusion examine les différents signaux signalés sur un abonnement entre les ressources et trouve des modèles prédominants qui montrent la progression d’une attaque ou des signaux avec des informations contextuelles partagées indiquant qu’une procédure de réponse unifiée doit être entreprise.

L’analytique de fusion combine les connaissances dans le domaine de la sécurité avec l’intelligence artificielle pour analyser les alertes, découvrant progressivement de nouveaux modèles d’attaque. 

Security Center s’appuie sur MITRE Attack Matrix pour associer les alertes à leur intention perçue, facilitant la formalisation des connaissances dans le domaine de la sécurité. De plus, en utilisant les informations collectées pour chaque étape d’une attaque, Security Center peut écarter les activités qui semblent être des étapes d’une attaque, mais ne le sont pas.  

Les attaques se produisant souvent dans différents locataires, Security Center peut combiner des algorithmes d’intelligence artificielle pour analyser les séquences d’attaque signalées dans chaque abonnement afin de les identifier en tant que modèles d’alerte prédominants au lieu de simplement les associer les unes aux autres incidemment.

Dans le cadre d’une investigation d’un incident, les analystes ont souvent besoin de plus de contexte pour aboutir à un verdict sur la nature de la menace et sur les moyens de l’atténuer. Par exemple, même si une anomalie de réseau a été détectée, sans connaître les autres événements qui se produisent sur le réseau ou en ce qui concerne la ressource ciblée, il est difficile de déterminer les mesures à prendre. Pour vous aider, un incident de sécurité peut inclure des artefacts, des événements connexes et des informations. Les informations supplémentaires disponibles pour les incidents de sécurité varient en fonction du type de menace détecté et de la configuration de votre environnement. 

![Détails d’un incident de sécurité](./media/security-center-alerts-cloud-smart/security-incident.png)

Pour mieux comprendre les incidents de sécurité, consultez [Gestion des incidents de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

