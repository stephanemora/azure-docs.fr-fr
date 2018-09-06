---
title: Fichier Include
description: Fichier Include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702630"
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
