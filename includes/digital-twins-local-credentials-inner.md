---
author: baanders
description: Fichier include pour la configuration de l’authentification locale pour DefaultAzureCredential dans les exemples Azure Digital Twins - Sans introduction
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494644"
---
Avec `DefaultAzureCredential`, l’exemple recherche les informations d’identification dans votre environnement local, comme une connexion Azure dans une [interface de ligne de commande Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) locale ou dans Visual Studio/Visual Studio Code. Cela signifie que vous devez **vous connecter à Azure localement** via l’un de ces mécanismes afin de configurer les informations d’identification pour l’exemple.

Si vous utilisez Visual Studio ou Visual Studio Code pour exécuter l’exemple de code, assurez-vous que vous êtes connecté à cet éditeur avec les mêmes informations d’identification Azure que celles que vous souhaitez utiliser pour accéder à votre instance Azure Digital Twins.

Sinon, vous pouvez [installer l’ **interface de ligne de commande Azure** locale](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), démarrer une invite de commandes sur votre machine et exécuter la commande `az login` pour vous connecter à votre compte Azure. Ensuite, quand vous exécutez votre exemple de code, vous devez vous connecter automatiquement.