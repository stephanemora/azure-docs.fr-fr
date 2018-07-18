---
title: Fichier Include
description: Fichier Include
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138158"
---
Vérifiez que vous avez déjà créé un espace de noms Service Bus, comme illustré [ici][namespace-how-to].

1. Connectez-vous au [portail Azure][azure-portal].
2. Dans le volet de navigation gauche du portail, cliquez sur **Service Bus** (si vous ne voyez pas **Service Bus**, cliquez sur **Tous les services**).
3. Cliquez sur l’espace de noms dans lequel vous souhaitez créer la file d’attente. Dans ce cas, il s’agit de **sbnstest1**.
   
    ![Créer une file d’attente][createqueue1]
4. Dans la fenêtre de l’espace de noms, cliquez sur **Files d’attente**, puis dans la fenêtre **Files d’attente**, cliquez sur **File d’attente**.
   
    ![Sélectionner Files d’attente][createqueue2]
5. Entrez le **Nom** de la file d’attente et laissez les valeurs par défaut des autres valeurs.
   
    ![Sélectionner Nouveau][createqueue3]
6. En bas de la fenêtre, cliquez sur **Créer**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
