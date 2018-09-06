---
title: Contrats pour la communication B2B - Azure Logic Apps | Microsoft Docs
description: Créer des contrats de partenariat pour la communication avec un partenaire commercial B2B avec Azure Logic Apps et Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 06/29/2016
ms.openlocfilehash: 09bee10649e2bc0d745e42b8aa13ae9c21df35aa
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128825"
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Contrats de partenariat pour la communication B2B avec Azure Logic Apps et Enterprise Integration Pack

Les contrats permettent à des entités métier de communiquer en toute transparence à l’aide de protocoles standard et constituent la pierre angulaire des communications Business-to-Business (B2B). Lorsque des scénarios B2B sont activés pour des applications logiques avec Enterprise Integration Pack, un contrat est une organisation de communications conclue entre des partenaires commerciaux B2B. Ce contrat est basé sur le type de communication que les partenaires souhaitent établir. Il est propre au protocole ou au transport.

L’intégration d’entreprise prend en charge ces normes de protocole/transport :

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Pourquoi utiliser des contrats ?

Voici quelques avantages communs lors de l’utilisation des contrats :

* Permet à différentes organisations et entreprises d’échanger des informations dans un format reconnu.
* Améliore l’efficacité des transactions B2B
* Les accords sont faciles à créer, gérer et utiliser lors de la création d’applications d’intégration d’entreprise.

## <a name="how-to-create-agreements"></a>Comment créer des contrats ?

* [Créer un contrat AS2](logic-apps-enterprise-integration-as2.md)
* [Créer un contrat X12](logic-apps-enterprise-integration-x12.md)
* [Créer un contrat EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>Comment utiliser un contrat ?

Vous pouvez créer des [applications logiques](logic-apps-overview.md "En savoir plus sur les applications logiques") dotées de fonctionnalités B2B à l’aide d’un contrat que vous avez créé.

## <a name="how-to-edit-an-agreement"></a>Comment modifier un contrat ?

Vous pouvez modifier n’importe quel contrat en procédant comme suit :

1. Sélectionnez le compte d’intégration contenant le contrat que vous souhaitez mettre à jour.

2. Choisissez la mosaïque **Contrats**.

3. Dans le panneau **Contrats**, sélectionnez le contrat.

4. Choisissez **Modifier**. Apportez vos modifications.

5. Pour enregistrer vos modifications, cliquez sur **OK**.

## <a name="how-to-delete-an-agreement"></a>Suppression d'un contrat

Vous pouvez supprimer n’importe quel contrat en procédant comme suit :

1. Sélectionnez le compte d’intégration contenant le contrat que vous souhaitez supprimer.
2. Choisissez la mosaïque **Contrats**.
3. Dans le panneau **Contrats**, sélectionnez le contrat.
4. Choisissez **Supprimer**.
5. Confirmez que vous souhaitez supprimer le contrat sélectionné.

    Le panneau Contrats n’affiche plus le contrat supprimé.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un contrat AS2](logic-apps-enterprise-integration-as2.md)
