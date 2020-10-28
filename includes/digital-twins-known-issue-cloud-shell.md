---
author: baanders
description: 'Inclure un fichier pour Azure Digital Twins : cite le problème connu avec l’authentification Cloud Shell'
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325161"
---
>[!NOTE]
>Il existe actuellement un **problème connu** dans Cloud Shell qui affecte les groupes de commandes suivants lors de leur exécution à partir de *https://shell.azure.com*  : `az dt route`, `az dt model`, `az dt twin`.
>
>Pour le résoudre, vous pouvez effectuer l’une des actions suivantes :
> * Exécuter `az login` dans Cloud Shell avant d’exécuter la commande.
> * Ouvrir le volet Cloud Shell dans le portail Azure et effectuer votre travail Cloud Shell à partir de là.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Vue du portail Azure avec l’icône « Cloud Shell » mise en évidence, et l’éditeur Cloud Shell s’affichant dans la partie inférieure de la fenêtre du portail":::
> * Utiliser l’interface[CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) locale au lieu de Cloud Shell.
>
>Pour plus de détails sur ce problème, consultez [*Résolution des problèmes : Problèmes connus dans Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).