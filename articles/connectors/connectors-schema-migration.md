---
title: Migrer des applications vers le schéma le plus récent - Azure Logic Apps | Microsoft Docs
description: Comment migrer vos applications logiques vers la dernière version du schéma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: bf27739bd42106550c18e3bbc27a1ff8b3770747
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321124"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrer des applications logiques vers la dernière version du schéma

Pour migrer vos applications logiques existantes vers le schéma le plus récent, procédez comme suit : 

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

2. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Mettre à jour le schéma**.

   > [!NOTE]
   > Lorsque vous choisissez **Mettre à jour le schéma**, Azure Logic Apps exécute automatiquement les étapes de migration avec le code de sortie correspondant. Vous pouvez utiliser cette sortie pour mettre à jour la définition de votre application logique. Toutefois, veillez à respecter les meilleures pratiques comme décrit dans la section **Meilleures pratiques** suivante.

   ![Mettre à jour le schéma](./media/connectors-schema-migration/update-schema.png)

   La page Mettre à jour le schéma apparaît et affiche un lien vers un document qui décrit les améliorations incluses dans le nouveau schéma.

## <a name="best-practices"></a>Bonnes pratiques

Voici quelques-unes des meilleures pratiques pour la migration de vos applications logiques vers la dernière version du schéma :

* Copiez le script migré vers une nouvelle application logique. Ne remplacez pas l’ancienne version avant d’avoir terminé vos tests et vérifiez que votre application migrée fonctionne comme prévu.

* Testez votre application logique **avant** sa mise en production.

* Une fois la migration terminée, commencez à mettre à jour vos applications logiques pour utiliser les [API managées](../connectors/apis-list.md) lorsque cela est possible. Par exemple, commencez à utiliser Dropbox v2 partout où vous utilisez DropBox v1.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez à [migrer manuellement vos applications logiques](../logic-apps/logic-apps-schema-2015-08-01.md)
