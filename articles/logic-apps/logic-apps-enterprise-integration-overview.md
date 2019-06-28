---
title: Vue d’ensemble de l’intégration d’entreprise B2B - Azure Logic Apps | Microsoft Docs
description: Générer des workflows B2B automatisés pour les solutions d’intégration d’entreprise avec Azure Logic Apps et Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.date: 09/08/2016
ms.openlocfilehash: 8c0e47f5bed0799b8536cecb38a85ed76185d0cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845821"
---
# <a name="overview-b2b-enterprise-integration-scenarios-in-azure-logic-apps-with-enterprise-integration-pack"></a>Vue d’ensemble : Scénarios d’intégration d’entreprise B2B dans Azure Logic Apps avec Enterprise Integration Pack

Dans le cadre des workflows entreprise-entreprise (B2B) et d’une communication transparente avec Azure Logic Apps, vous pouvez activer des scénarios d’intégration d’entreprise à l’aide d’une solution Microsoft basée sur le cloud : Enterprise Integration Pack. Les entreprises peuvent échanger des messages électroniques, même si elles utilisent des formats et des protocoles différents. Le pack convertit les différents formats dans un format que les systèmes des entreprises peuvent interpréter et traiter. Les entreprises peuvent échanger des messages via des protocoles standard, notamment [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) et [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Vous pouvez également sécuriser les messages grâce au chiffrement et aux signatures numériques.

Si vous êtes familiarisé avec BizTalk Server ou Microsoft Azure BizTalk Services, vous saurez facilement utiliser les fonctionnalités Enterprise Integration, car la plupart des concepts sont similaires. La principale différence réside dans le fait qu'Enterprise Integration utilise les comptes d’intégration pour simplifier le stockage et la gestion des artefacts utilisés dans les communications B2B. 

D’un point de vue architectural, Enterprise Integration Pack est basé sur des « comptes d’intégration ». Ces comptes sont des conteneurs basés sur le cloud qui stockent tous vos artefacts, tels que les schémas, les partenaires, les certificats, les mappages et les contrats. Vous pouvez utiliser ces artefacts pour concevoir, déployer et gérer vos applications B2B, mais aussi créer des workflows B2B pour les applications logiques. Toutefois, avant de pouvoir utiliser ces artefacts, vous devez d’abord lier votre compte d’intégration à votre application logique. Ainsi, votre application logique pourra accéder aux artefacts de votre compte d’intégration.

## <a name="why-should-you-use-enterprise-integration"></a>Pourquoi utiliser l'intégration d’entreprise ?

* Grâce à l’intégration d’entreprise, vous pouvez stocker tous vos artefacts dans un même endroit : votre compte d’intégration.
* Vous pouvez créer des workflows B2B et les intégrer dans des applications SaaS tierces, des applications locales et des applications personnalisées à l’aide du moteur Azure Logic Apps et de tous ses connecteurs.
* Vous pouvez créer un code personnalisé pour vos applications logiques avec Azure Functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>Comment prendre en main Enterprise Integration Pack ?

Vous pouvez créer et gérer des applications B2B à l’aide d’Enterprise Integration Pack via le concepteur Logic Apps du **portail Azure**. Vous pouvez également gérer vos applications logiques avec [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp).

Voici les étapes de niveau supérieur permettant de créer des applications dans le portail Azure :

![image de présentation](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Quels sont les scénarios courants ?

Enterprise Integration prend en charge les normes suivantes :

* EDI - Electronic Data Interchange
* EAI - Enterprise Application Integration

## <a name="heres-what-you-need-to-get-started"></a>Voici ce dont vous avez besoin pour commencer

* Un abonnement Azure avec un compte d’intégration
* Visual Studio 2015 pour créer des mappages et des schémas
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Essayez-le dès maintenant

[Déployez votre propre exemple d’application d’envoi et de réception AS2 entièrement fonctionnelle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) utilisant les fonctionnalités B2B d’Azure Logic Apps.

## <a name="learn-more"></a>En savoir plus
* [Accords](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")
* [Scénarios Business to Business (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "Apprenez à créer des applications logiques avec fonctionnalités B2B")  
* [Certificats](logic-apps-enterprise-integration-certificates.md "Découvrez les certificats d’intégration d’entreprise")
* [Codage/décodage de fichier plat](logic-apps-enterprise-integration-flatfile.md "Découvrez comment encoder et décoder le contenu d’un fichier plat")  
* [Comptes d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md "En savoir plus sur les comptes d’intégration")
* [Mappages](../logic-apps/logic-apps-enterprise-integration-maps.md "Découvrez les mappages d’intégration d’entreprise")
* [Partenaires](logic-apps-enterprise-integration-partners.md "Découvrez les partenaires d’intégration d’entreprise")
* [Schémas](logic-apps-enterprise-integration-schemas.md "Découvrez les schémas d’intégration d’entreprise")
* [Validation de message XML](logic-apps-enterprise-integration-xml.md "Découvrez comment valider des messages XML avec vos applications logiques")
* [Transformation XML](logic-apps-enterprise-integration-transform.md "Découvrez les mappages d’intégration d’entreprise")
* [Connecteurs d'intégration d’entreprise](../connectors/apis-list.md "En savoir plus sur les connecteurs Enterprise Integration Pack")
* [Métadonnées du compte d'intégration](../logic-apps/logic-apps-enterprise-integration-metadata.md "En savoir plus sur les métadonnées du compte d’intégration")
* [Suivi des messages B2B](logic-apps-monitor-b2b-message.md "En savoir plus sur le suivi des messages B2B")
* [Suivre les messages B2B dans les journaux Azure Monitor](logic-apps-track-b2b-messages-omsportal.md "En savoir plus sur le suivi des messages B2B dans les journaux Azure Monitor")

