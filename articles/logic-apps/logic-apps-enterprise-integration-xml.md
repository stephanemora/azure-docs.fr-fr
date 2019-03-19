---
title: Messages XML pour l’intégration d’entreprise B2B - Azure Logic Apps | Microsoft Docs
description: Traiter, valider, transformer et enrichir des messages XML pour des solutions B2B dans Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873251"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Messages XML et fichiers plats dans Azure Logic Apps avec Enterprise Integration Pack

Les applications logiques vous permettent de traiter les messages XML envoyés et reçus. Cette fonctionnalité est incluse dans Enterprise Integration Pack. Pour les utilisateurs de BizTalk Server, Enterprise Integration Pack offre des capacités similaires pour transformer et valider des messages, utiliser des fichiers plats et même XPath pour enrichir ou extraire des propriétés spécifiques d’un message. 

Pour les utilisateurs qui découvrent cet environnement, ces fonctionnalités étendent les possibilités de traitement des messages au sein de votre flux de travail. Par exemple, dans un scénario d’entreprise à entreprise où vous travaillez avec des schémas XML spécifiques, vous pouvez utiliser Enterprise Integration Pack pour améliorer la façon dont votre entreprise traite ces messages. 

Enterprise Integration Pack inclut les éléments suivants : 

* [Validation XML](logic-apps-enterprise-integration-xml-validation.md "En savoir plus sur la validation de messages XML") : permet de valider un message XML entrant ou sortant par rapport à un schéma spécifique.
* [Transformation XML](../logic-apps/logic-apps-enterprise-integration-transform.md "En savoir plus sur les transformations et les mappages de message XML") : permet de convertir un message XML basé sur vos exigences ou celles d’un partenaire.
* [Encodage et décodage de fichier plat](logic-apps-enterprise-integration-flatfile.md "En savoir plus sur l’encodage/décodage de fichier plat") : permet de coder ou décoder un fichier plat. Par exemple, SAP accepte et envoie des fichiers IDOC dans un format de fichier plat. De nombreuses plates-formes d’intégration créent des messages XML, y compris Logic Apps. Vous pouvez donc créer une application logique qui utilise l’encodeur de fichier plat afin de « convertir » des fichiers XML en fichiers plats. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) : permet d’enrichir un message et d’extraire des propriétés spécifiques du message. Les propriétés extraites peuvent ensuite servir à acheminer le message vers une destination ou un point de terminaison intermédiaire.

## <a name="try-it-out"></a>Faites un essai
[Déployer une application logique entièrement fonctionnelle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (exemple GitHub) à l’aide des fonctionnalités XML dans Azure Logic Apps.

## <a name="learn-more"></a>En savoir plus
[En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")
