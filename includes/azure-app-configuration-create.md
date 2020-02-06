---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 017833403beca6fb8689bd6f90cc8f460d56c65e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961564"
---
1. Pour créer un magasin App Configuration, connectez-vous au [portail Azure](https://portal.azure.com). En haut à gauche de la page d’accueil, sélectionnez **Créer une ressource**. Dans la zone **Rechercher dans la Place de marché**, entrez **App Configuration** et sélectionnez Entrée.

    ![Rechercher App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Sélectionnez **Configuration d’application** dans les résultats de la recherche, puis **Créer**.

    ![Sélectionner Créer](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Dans le volet **App Configuration** > **Créer**, entrez les paramètres suivants :

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom de la ressource** | Nom globalement unique | Entrez un nom de ressource unique à utiliser pour la ressource du magasin App Configuration. Le nom doit être une chaîne de 5 à 50 caractères. Il doit contenir uniquement des chiffres, des lettres et le caractère `-`. Le nom ne peut ni commencer ni finir par le caractère `-`.  |
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour tester App Configuration. Si votre compte n’a qu’un seul abonnement, il est automatiquement sélectionné et la liste **Abonnement** n’est pas affichée. |
    | **Groupe de ressources** | *AppConfigTestResources* | Sélectionnez ou créez un groupe de ressources pour votre ressource du magasin App Configuration. Ce groupe est utile pour organiser plusieurs ressources que vous souhaitez supprimer en même temps que vous supprimez ce groupe de ressources. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lieu** | *USA Centre* | Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre magasin de configuration d’application. Pour des performances optimales, créez la ressource dans la même région que les autres composants de votre application. |

    ![Créer une ressource de magasin App Configuration](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Sélectionnez **Create** (Créer). Le déploiement peut prendre quelques minutes.

1. Une fois le déploiement terminé, sélectionnez **Paramètres** > **Clés d’accès**. Notez la chaîne de connexion de la clé primaire en lecture seule. Vous utilisez cette chaîne de connexion plus tard pour configurer votre application, afin qu’elle communique avec le magasin App Configuration que vous avez créé.
