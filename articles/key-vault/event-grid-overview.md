---
title: 'Didacticiel : Supervision de Key Vault avec Azure Event Grid'
description: 'Didacticiel : Utilisez Azure Event Grid pour vous abonner aux événements Key Vault'
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5771af365b763d2152eea4ef4f662e08769b378c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133350"
---
# <a name="tutorial-monitoring-key-vault-with-azure-event-grid-preview"></a>Didacticiel : Supervision de Key Vault avec Azure Event Grid (préversion)

L’intégration de Key Vault avec Event Grid est actuellement en préversion. Cela permet aux utilisateurs d’être notifiés en cas de changement de l’état d’un secret stocké dans le coffre de clés. Un changement d’état se définit comme étant un secret sur le point d’expirer (dans les 30 jours suivant l’expiration), un secret ayant expiré ou un secret ayant une nouvelle version disponible. Les notifications relatives aux trois types de secret (clé, certificat et secret) sont prises en charge.

Les applications peuvent réagir à ces événements à l’aide d’architectures modernes serverless, sans qu’il soit nécessaire de recourir à du code compliqué ou à des services d’interrogation coûteux et inefficaces. Les événements sont envoyés (push) via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) à des gestionnaires d’événements tels qu’[Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) ou même votre propre Webhook. Vous payez uniquement pour ce que vous utilisez. Pour plus d’informations sur la tarification, consultez la page [Tarification Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Événements et schémas Key Vault

Event Grid utilise les [abonnements aux événements](../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Les événements Key Vault contiennent toutes les informations dont vous avez besoin pour répondre aux changements apportés à vos données. Vous pouvez facilement identifier un événement Key Vault, car la propriété eventType commence par « Microsoft.KeyVault ».

Pour plus d’informations, consultez le [schéma d’événement Key Vault](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Les événements se déclenchent uniquement pour les versions des secrets (les trois types) créées après la définition de l’abonnement.
>
> Pour les secrets existants, vous devez générer de nouvelles versions.

## <a name="practices-for-consuming-events"></a>Pratiques pour la consommation d’événements

Les applications qui gèrent les événements Key Vault doivent suivre certaines pratiques recommandées :

* Plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d'événements. Il est important de ne pas considérer que les événements proviennent d’une source particulière, mais de vérifier le sujet du message pour avoir la certitude qu’il provient du coffre de clés attendu.
* De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
* Ignorez les champs que vous ne comprenez pas.  Cette pratique vous aidera à prendre en charge les nouvelles fonctionnalités qui peuvent être ajoutées à l’avenir.
* Utilisez le préfixe « subject « et les correspondances de suffixe pour limiter les événements à un événement particulier.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Key Vault](key-vault-overview.md)
- [Vue d’ensemble d’Azure Event Grid](../event-grid/overview.md)
- Activation [Routez les événements Key Vault vers le runbook Automation (préversion)](event-grid-tutorial.md).
- Activation [Recevoir un e-mail en cas de changement d’un secret de coffre de clés](event-grid-logicapps.md)
- [Schéma des événements Azure Event Grid pour Azure Key Vault (préversion)](../event-grid/event-schema-key-vault.md)
- [Vue d’ensemble d’Azure Automation](../automation/index.yml)
