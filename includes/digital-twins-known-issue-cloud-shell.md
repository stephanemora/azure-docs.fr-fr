---
author: baanders
description: 'Inclure un fichier pour Azure Digital Twins : cite le problème connu avec l’authentification Cloud Shell'
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290093"
---
>[!NOTE]
>Il existe actuellement un **problème connu** dans Cloud Shell affectant les groupes de commandes suivants : `az dt route`, `az dt model`, `az dt twin`.
>
>Pour le résoudre, exécutez `az login` dans Cloud Shell avant d’exécuter la commande, ou utilisez la [CLI locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) au lieu de Cloud Shell. Pour plus d’informations, consultez [*résolution des problèmes : Problèmes connus dans Azure Digital Twins*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).