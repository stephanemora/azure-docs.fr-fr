---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77029163"
---
## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Dans cette section, vous créez une application de fonction et les ressources associées dans votre abonnement Azure, puis vous déployez votre code. 

1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, choisissez le bouton **Déployer sur une application de fonction**.

    ![Publier votre projet sur Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quand vous y êtes invité, indiquez les informations suivantes :

    + **Sélectionnez l’abonnement** : choisissez l’abonnement à utiliser. Vous ne verrez pas ceci si vous n’avez qu’un seul abonnement.

    + **Sélectionnez une application de fonction dans Azure** : Choisissez `+ Create new Function App` (et non pas `Advanced`). Cet article ne prend pas en charge le [flux de publication avancé](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > La publication sur une application de fonction existante remplace le contenu de cette application dans Azure. 
    
    + **Entrer un nom global unique pour l’application de fonction** : Tapez un nom valide dans un chemin d’URL. Le système vérifie que le nom que vous tapez est unique dans Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Sélectionnez un runtime** : Choisissez la version de Python que vous avez exécutée localement. Vous pouvez utiliser la commande `python --version` pour vérifier votre version.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Sélectionnez un runtime** : Choisissez la version de Node.js que vous avez exécutée localement. Vous pouvez utiliser la commande `node --version` pour vérifier votre version.
    ::: zone-end

    + **Sélectionnez un emplacement pour les nouvelles ressources** :  Pour de meilleures performances, choisissez une [région](https://azure.microsoft.com/regions/) proche de vous. 
    
1.  Quand vous avez terminé, les ressources Azure suivantes sont créées dans votre abonnement :

    + **[Groupe de ressources](../articles/azure-resource-manager/management/overview.md)**  : contient toutes les ressources Azure créées. Le nom est basé sur le nom de votre application de fonction.
    + **[Compte de stockage](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)**  : un compte de stockage standard est créé avec un nom unique, basé sur le nom de votre application de fonction.
    + **[Plan d'hébergement](../articles/azure-functions/functions-scale.md)**  : un plan de consommation est créé dans la région USA Ouest pour héberger votre application de fonction serverless.
    + **Application de fonction** : votre projet est déployé dans cette nouvelle application de fonction avant d'y être exécuté.
    + **Application Insights** : Une instance, connectée à votre application de fonction, est créée en fonction du nom de votre fonction.

    Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué. 
    
1. Sélectionnez **Afficher la sortie de** dans cette notification pour afficher les résultats de la création et du déploiement, y compris les ressources Azure que vous avez créées. Si vous manquez la notification, sélectionnez l’icône de cloche dans le coin inférieur droit pour la voir de nouveau.

    ![Créer une notification de fin](media/functions-publish-project-vscode/function-create-notifications.png)
