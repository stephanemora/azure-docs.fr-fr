---
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: include
ms.date: 05/03/2021
ms.openlocfilehash: 7b35a3c4b26640b872b161bd8130db728645f1d0
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748269"
---
Une [connexion de service](/azure/devops/pipelines/library/service-endpoints) vous permet d’accéder aux ressources de votre abonnement Azure à partir de votre projet Azure DevOps.

1. Dans Azure DevOps, accédez au projet qui contient votre pipeline cible. Dans l’angle inférieur gauche, sélectionnez **Paramètres du projet**.
1. Sous **Pipelines**, sélectionnez **Connexions de service**. Dans l’angle supérieur droit, sélectionnez **Nouvelle connexion de service**.
1. Dans **Nouvelle connexion de service**, sélectionnez **Azure Resource Manager**.

    :::image type="content" source="./media/azure-app-configuration-service-connection/new-service-connection.png" alt-text="La capture d’écran montre la sélection d’Azure Resource Manager dans la liste déroulante Nouvelle connexion de service.":::
1. Dans la boîte de dialogue **Méthode d’authentification**, sélectionnez **Principal du service (automatique)** pour créer un principal du service ou sélectionnez **Principal du service (manuel)** pour [utiliser un principal de service existant](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#use-spn&preserve-view=true).
1. Entrez votre abonnement, votre ressource et un nom pour votre connexion de service.

Si vous avez créé un principal de service, recherchez le nom du principal de service affecté à la connexion de service. Vous ajouterez une nouvelle attribution de rôle à ce principal de service à l’étape suivante.

1. Accédez à **Paramètres du projet** > **Connexions de service**.
1. Sélectionnez la nouvelle connexion de service.
1. Sélectionnez **Gérer le principal du service**.
1. Notez la valeur dans **Nom d’affichage**.

    :::image type="content" source="./media/azure-app-configuration-service-connection/service-principal-display-name.png" alt-text="Capture d’écran montrant le nom complet du principal de service.":::
