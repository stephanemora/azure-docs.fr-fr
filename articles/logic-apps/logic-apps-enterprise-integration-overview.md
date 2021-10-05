---
title: Workflows d’intégration d’entreprise B2B
description: Découvrez comment générer des workflows B2B automatisés pour l’intégration d’entreprise avec Azure Logic Apps et Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: overview
ms.date: 09/14/2021
ms.openlocfilehash: 91ea9eb37e7c4e2e97513a1496a52d2521e9f4b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652133"
---
# <a name="b2b-enterprise-integration-workflows-with-azure-logic-apps-and-enterprise-integration-pack"></a>Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack

Pour les solutions B2B (Business-to-Business) et une communication fluide entre les organisations, vous pouvez générer des workflows d’intégration d’entreprise automatisés et scalables en utilisant [Azure Logic Apps](logic-apps-overview.md) avec Enterprise Integration Pack (EIP).

## <a name="what-is-the-enterprise-integration-pack"></a>Qu'est-ce que Enterprise Integration Pack ?

Le pack EIP utilise des concepts similaires à ceux de Microsoft BizTalk Server et de Azure BizTalk Services, et rend les fonctionnalités B2B faciles à utiliser. Toutefois, l’une des principales différences réside dans le fait que le pack EIP est basé sur l’architecture des *comptes d’intégration*. Ces comptes sont des conteneurs cloud situés dans Azure qui simplifient le stockage, la gestion et l’utilisation des artefacts B2B pour la communication B2B, tels que les [partenaires commerciaux](logic-apps-enterprise-integration-partners.md), les [contrats](logic-apps-enterprise-integration-agreements.md), les [mappages](logic-apps-enterprise-integration-maps.md), les [schémas](logic-apps-enterprise-integration-schemas.md), les [certificats](logic-apps-enterprise-integration-certificates.md), etc.

Grâce à ces artefacts, vous pouvez créer des workflows B2B et des solutions d’intégration qui incluent des services cloud comme Azure, Microsoft et d’autres applications SaaS, des systèmes locaux et des applications personnalisées, en utilisant Azure Logic Apps avec [plus de 400 opérations intégrées et connecteurs managés](/connectors/connector-reference/connector-reference-logicapps-connectors). Par exemple, vous pouvez créer et exécuter du code personnalisé à partir de vos workflows à l’aide d’opérations d’exécution de code intégrées et d’Azure Functions. Vous pouvez également utiliser des [connecteurs d’intégration d’entreprise](../connectors/managed.md#enterprise-connectors) qui prennent en charge les standards industriels suivants :

* Échange de données informatisé (EDI)
* Intégration des applications de l’entreprise (EAI)

Même si les organisations utilisent différents protocoles et formats pour la communication B2B, vous pouvez toujours échanger des messages par voie électronique avec d’autres organisations. Vous pouvez convertir ces différents formats en un format que les systèmes de votre organisation pourront traiter à l’aide du pack EIP, qui prend en charge les protocoles standard, notamment [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) et [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Vous pouvez également améliorer la sécurité des messages en utilisant à la fois le chiffrement et les signatures numériques.

## <a name="what-do-i-need-to-get-started"></a>Ce dont vous avez besoin pour commencer

* Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) pour stocker les artefacts B2B que vous définissez et souhaitez utiliser.

* Vos artefacts B2B tels que les [partenaires commerciaux](logic-apps-enterprise-integration-partners.md), les [contrats](logic-apps-enterprise-integration-agreements.md), les [mappages](logic-apps-enterprise-integration-maps.md), les [schémas](logic-apps-enterprise-integration-schemas.md), les [certificats](logic-apps-enterprise-integration-certificates.md), et ainsi de suite.

* Pour créer des mappages et des schémas, vous pouvez utiliser l’[extension Microsoft Azure Logic Apps Enterprise Integration Tools ](https://aka.ms/vsenterpriseintegrationtools) et Visual Studio 2019. Si vous utilisez Visual Studio 2015, vous pouvez utiliser l’[extension Microsoft Azure Logic Apps Enterprise Integration Tools pour Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas).

   > [!IMPORTANT]
   > N’installez pas cette extension de concert avec l’extension BizTalk Server. Ces deux extensions installées côte à côte risquent d’entraîner un comportement inattendu. Vérifiez que seule l’une de ces extensions est installée.

   > [!NOTE]
   > Sur les moniteurs à haute résolution, vous risquez de rencontrer un [problème d’affichage avec le concepteur de mappages](/visualstudio/designers/disable-dpi-awareness) dans Visual Studio. Pour résoudre ce problème d’affichage, [redémarrez Visual Studio en mode sans prise en charge DPI](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process) ou ajoutez la [valeur de Registre DPIUNAWARE](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry).

Après avoir créé un compte d’intégration et ajouté vos artefacts, vous pouvez commencer à générer des workflows B2B en créant une ressource d’application logique. Si vous débutez avec les applications logiques, essayez de [créer un exemple de workflow d’application logique de base](quickstart-create-first-logic-app-workflow.md). Vous pouvez également créer, gérer et déployer des applications logiques à l’aide de [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md), [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) ou [PowerShell](/powershell/module/az.logicapp).

> [!IMPORTANT]
> Si vous utilisez le type de ressource **Application logique (Consommation)** , vous devez lier votre compte d’intégration à votre ressource d’application logique avant de pouvoir sélectionner les artefacts B2B à utiliser dans votre workflow. Toutefois, pour définir et ajouter ces artefacts à votre compte d’intégration, vous n’avez pas encore besoin d’une ressource d’application logique.
>
> Si vous utilisez le type de ressource **Application logique (Standard)** , vous pouvez ajouter des schémas et des mappages directement à votre ressource d’application logique et utiliser ces artefacts dans plusieurs workflows au sein d’une *même ressource d’application logique*. 
> Vous avez toujours besoin d’un compte d’intégration pour stocker d’autres artefacts comme les partenaires et les contrats. Toutefois, la liaison n’étant plus nécessaire, cette fonctionnalité a donc été supprimée. Pour plus d’informations sur ces types de ressources, consultez [En quoi consiste Azure Logic Apps - Type de ressource et environnements d’hôte ?](logic-apps-overview.md#resource-type-and-host-environment-differences).

Le diagramme suivant montre les principales étapes de création d’un workflow d’application logique B2B :

![Diagramme conceptuel montrant les étapes nécessaires pour créer des workflows d’application logique B2B.](media/logic-apps-enterprise-integration-overview/overview.png)

## <a name="try-now"></a>Essayer dès maintenant

[Déployer un exemple d’application logique entièrement opérationnelle qui envoie et reçoit des messages AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-as2-send-receive)

## <a name="next-steps"></a>Étapes suivantes

* [Créer des partenaires commerciaux](logic-apps-enterprise-integration-partners.md)
* [Créer des accords](logic-apps-enterprise-integration-agreements.md)
* [Ajouter des schémas](logic-apps-enterprise-integration-schemas.md)
* [Ajouter des mappages](logic-apps-enterprise-integration-maps.md)
* [Migrer depuis des Services BizTalk](logic-apps-move-from-mabs.md)
