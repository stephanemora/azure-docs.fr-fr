---
author: baanders
description: Fichier include pour la configuration de l’authentification locale pour DefaultAzureCredential dans les exemples Azure Digital Twins - Sans introduction
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: fb148551db798207a52bd7aef629da79dd3341e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102212810"
---
Avec `DefaultAzureCredential`, l’exemple recherche les informations d’identification dans votre environnement local, comme une connexion Azure dans une [interface de ligne de commande Azure](/cli/azure/install-azure-cli) locale ou dans Visual Studio ou Visual Studio Code. C’est la raison pour laquelle vous devez *vous connecter à Azure localement* via l’un de ces mécanismes afin de configurer les informations d’identification pour l’exemple.

Si vous utilisez Visual Studio ou Visual Studio Code pour exécuter l’exemple de code, assurez-vous que vous êtes connecté à cet éditeur avec les mêmes informations d’identification Azure que celles que vous souhaitez utiliser pour accéder à votre instance Azure Digital Twins.

Sinon, vous pouvez [installer l’interface de ligne de commande Azure locale](/cli/azure/install-azure-cli), démarrer une invite de commandes sur votre machine et exécuter la commande `az login` pour vous connecter à votre compte Azure. Une fois cette connexion effectuée, quand vous exécutez votre exemple de code, vous devez vous connecter automatiquement.