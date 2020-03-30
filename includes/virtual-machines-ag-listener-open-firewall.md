---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177009"
---
Au cours de cette étape, vous allez créer une règle de pare-feu pour ouvrir le port de la sonde du point de terminaison à charge équilibrée (59999, comme indiqué précédemment) et une autre règle pour ouvrir le port de l’écouteur de groupe de disponibilité. Comme vous avez créé le point de terminaison à charge équilibrée sur les machines virtuelles qui contiennent des réplicas du groupe de disponibilité, vous devez ouvrir le port de la sonde et le port de l’écouteur sur les machines virtuelles respectives.

1. Sur les machines virtuelles hébergeant les réplicas, démarrez le **Pare-feu Windows avec fonctions avancées de sécurité**.

2. Cliquez avec le bouton droit sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle**.

3. Dans la page **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.

4. Dans la page **Protocole et ports**, sélectionnez **TCP**, tapez **59999** dans la zone **Ports locaux spécifiques**, puis cliquez sur **Suivant**.

5. Dans la page **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

6. Dans la page **Profil**, acceptez les paramètres par défaut puis cliquez sur **Suivant**.

7. Dans la zone de texte **Nom** de la page **Nom**, spécifiez un nom pour la règle, comme **Port de la sonde d’écouteur toujours actif**, puis cliquez sur **Terminer**.

8. Répétez les étapes précédentes pour le port de l’écouteur de groupe de disponibilité (comme spécifié précédemment à l’aide du paramètre $EndpointPort du script), puis indiquez un nom de règle approprié, tel que **Port de l’écouteur toujours actif**.

