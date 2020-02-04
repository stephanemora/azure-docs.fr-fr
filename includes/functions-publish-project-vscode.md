---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842147"
---
## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Dans cette section, vous créez une application de fonction et les ressources associées dans votre abonnement Azure, puis vous déployez votre code. 

1. Choisissez l’icône Azure dans la barre d’activité, puis dans la zone **Azure : Fonctions**, choisissez le bouton **Déployer sur une application de fonction**.

    ![Publier votre projet sur Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Indiquez les informations suivantes aux invites :

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Prompt | Valeur | Description |
    | ------ | ----- | ----- |
    | Sélectionner un abonnement | Votre abonnement | S’affiche quand vous avez plusieurs abonnements. |
    | Sélectionner une application de fonction dans Azure | + Créer une application de fonction | La publication sur une application de fonction existante remplace le contenu de cette application dans Azure. |
    | Entrer un nom global unique pour l’application de fonction | Nom unique | Les caractères valides pour un nom d’application de fonction sont `a-z`, `0-9` et `-`. |
    | Sélectionner l’emplacement des nouvelles ressources | Région | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Prompt | Valeur | Description |
    | ------ | ----- | ----- |
    | Sélectionner un abonnement | Votre abonnement | S’affiche quand vous avez plusieurs abonnements. |
    | Sélectionner une application de fonction dans Azure | + Créer une application de fonction | La publication sur une application de fonction existante remplace le contenu de cette application dans Azure. |
    | Entrer un nom global unique pour l’application de fonction | Nom unique | Les caractères valides pour un nom d’application de fonction sont `a-z`, `0-9` et `-`. |
    | Sélectionner un runtime | Votre version | Choisissez la version du langage que vous avez exécutée localement. |
    | Sélectionner l’emplacement des nouvelles ressources | Région | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. | 

    ::: zone-end

    
1.  Quand vous avez terminé, les ressources Azure suivantes sont créées dans votre abonnement :

    + **[Groupe de ressources](../articles/azure-resource-manager/management/overview.md)**  : contient toutes les ressources Azure créées. Le nom est basé sur le nom de votre application de fonction.
    + **[Compte de stockage](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)**  : un compte de stockage standard est créé avec un nom unique, basé sur le nom de votre application de fonction.
    + **[Plan d'hébergement](../articles/azure-functions/functions-scale.md)**  : un plan de consommation est créé dans la région USA Ouest pour héberger votre application de fonction serverless.
    + **Application de fonction** : votre projet est déployé dans cette nouvelle application de fonction avant d'y être exécuté.
    + **[Application Insights]()**  : Une instance, connectée à votre application de fonction, est créée en fonction du nom de votre fonction.

    Une notification s’affiche après que votre application de fonction a été créée et que le package de déploiement a été appliqué. 
    
1. Sélectionnez **Afficher la sortie de** dans cette notification pour afficher les résultats de la création et du déploiement, y compris les ressources Azure que vous avez créées.

    ![Créer une notification de fin](media/functions-publish-project-vscode/function-create-notifications.png)

1. Dans la zone **Azure : Fonctions** de la barre latérale, développez la nouvelle application de fonction sous votre abonnement. Développez **Fonctions**, cliquez avec le bouton droit (Windows) ou appuyez sur Ctrl + clic (MacOS) sur **HttpExample**, puis choisissez **Copier l’URL de la fonction**.

    ![Copier l'URL de fonction du nouveau déclencheur HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
