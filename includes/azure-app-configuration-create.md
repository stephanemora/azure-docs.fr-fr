---
title: Fichier Include
description: Fichier Include
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885122"
---
1. Pour créer un magasin de configuration d’application, commencez par vous connecter au [portail Azure](https://aka.ms/azconfig/portal). Dans le coin supérieur gauche de la page, cliquez sur **+ Créer une ressource**. Dans la zone de texte **Rechercher dans la Place de marché**, tapez **App Configuration** et appuyez sur **Entrée**.

    ![Rechercher App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Dans les résultats de la recherche, cliquez sur **App Configuration**, puis sur **Créer**.

3. Dans la page **App Configuration** > **Créer**, entrez les paramètres suivants :

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom de la ressource** | Nom globalement unique | Entrez un nom de ressource unique à utiliser pour la ressource du magasin de configuration d’application. Le nom doit être une chaîne de 1 à 63 caractères et contenir uniquement des chiffres, des lettres et le caractère `-`. Le nom ne peut ni commencer ni se terminer par le caractère `-` et il n’accepte pas de caractères `-` consécutifs.  |
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour tester App Configuration. Si votre compte a un seul abonnement, il est automatiquement sélectionné et la liste déroulante **Abonnement** ne s’affiche pas. |
    | **Groupe de ressources** | *AppConfigTestResources* | Sélectionnez ou créez un groupe de ressources pour votre ressource du magasin de configuration d’application. Ce groupe est utile pour organiser plusieurs ressources que vous pouvez supprimer en même temps en supprimant le groupe de ressources. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lieu** | *USA Centre* | Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre magasin de configuration d’application. Pour des performances optimales, nous vous recommandons de créer la ressource dans la même région que les autres composants de votre application. |

    ![Créer une ressource de magasin de configuration d’application](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Cliquez sur **Créer**. Le déploiement peut prendre quelques minutes.

5. À la fin du déploiement, cliquez sur **Paramètres** > **Clés d’accès**. Prenez note de la chaîne de connexion de la clé primaire, en lecture seule ou en lecture-écriture. Vous allez l’utiliser plus tard pour configurer votre application, afin qu’elle communique avec le magasin de configuration d’application que vous venez de créer.

6. Cliquez sur **Explorateur de paires clé/valeur** et sur **+ Créer** pour ajouter les paires clé/valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:BackgroundColor | white |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | black |
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.
