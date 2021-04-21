---
title: Mesures utilisateur réelles avec des pages web - Azure Traffic Manager
description: Cet article vous aide à configurer vos pages web pour envoyer les mesures utilisateur réelles à Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580348"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Comment envoyer les mesures des utilisateurs réels à Azure Traffic Manager avec des pages web

Vous pouvez configurer vos pages web pour envoyer des mesures d’utilisateurs réels à Traffic Manager en obtenant une clé de mesures des utilisateurs réels et en incorporant le code généré dans la page web.

## <a name="obtain-a-real-user-measurements-key"></a>Obtenir une clé de mesures des utilisateurs réels

Les mesures que vous prenez et envoyez à Traffic Manager à partir de votre application cliente sont identifiées par le service à l’aide d’une chaîne unique, que l’on nomme **clé Mesures utilisateur réelles**. Vous pouvez obtenir une clé Mesures utilisateur réelles via le portail Azure, une API REST, PowerShell ou Azure CLI.

Pour obtenir la clé de mesures des utilisateurs réels à l’aide du portail Azure :
1. Dans un navigateur, connectez-vous au portail Azure. Si vous n’avez pas encore de compte, vous pouvez vous inscrire pour bénéficier d’un essai gratuit d’un mois.

1. Dans la barre de recherche du portail, recherchez le nom du profil Traffic Manager que vous souhaitez modifier, puis sélectionnez le profil Traffic Manager dans les résultats affichés.

1. Sur la page du profil Traffic Manager, cliquez sur **Mesures utilisateur réelles** sous **Paramètres**.

1. Cliquez sur **Générer une clé** pour créer une clé Mesures utilisateur réelles.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Capture d’écran de la procédure de génération d’une clé."::: 

   **Figure 1 : Génération de la clé de mesures des utilisateurs réels**

1. La page affiche maintenant la clé Mesures utilisateur réelles générée et un extrait de code JavaScript qui doit être incorporé à votre page HTML.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Capture d’écran du code JavaScript généré."::: 

    **Figure 2 : Clé de mesures des utilisateurs réels et code JavaScript de mesure**
 
1. Sélectionnez le bouton **Copier** pour copier le code JavaScript. 

> [!IMPORTANT]
> Utilisez le code JavaScript généré pour que la fonctionnalité Mesures des utilisateurs réels fonctionne correctement. Toute modification apportée à ce script ou aux scripts utilisés par la fonctionnalité Mesures des utilisateurs réels peut entraîner un comportement imprévisible.

## <a name="embed-the-code-to-an-html-web-page"></a>Incorporer le code à une page web HTML

Une fois que vous avez obtenu la clé Mesures utilisateur réelles, l’étape suivante consiste à incorporer le code JavaScript copié dans une page HTML que vos utilisateurs consultent. La modification du code HTML peut être effectuée de nombreuses façons, à l’aide de différents outils et flux de travail. Cet exemple montre comment mettre à jour une page HTML pour ajouter ce script. Vous pouvez utiliser ces instructions pour l’adapter à votre flux de travail de gestion du code source HTML.

1. Ouvrez la page HTML dans un éditeur de texte.

1. Collez le code JavaScript que vous avez copié dans la dernière section dans la section de CORPS du code HTML. Le code copié se trouve sur les lignes 8 et 9, voir figure 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Capture d’écran de JavaScript incorporé à une page web."::: 

    **Figure 3 : Code HTML simple auquel est incorporé du code JavaScript pour les mesures des utilisateurs réels**

1. Enregistrez le fichier HTML et hébergez-le sur un serveur Web connecté à Internet.

1. La prochaine fois que cette page sera affichée dans un navigateur web, le code JavaScript référencé sera téléchargé et le script exécutera les opérations de mesure et de création de rapports.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [mesures des utilisateurs réels](traffic-manager-rum-overview.md).
- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-overview.md)
- En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager
- En savoir plus sur la [création d’un profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)
