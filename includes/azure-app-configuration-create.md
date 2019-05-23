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
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133064"
---
1. Pour créer un magasin de configuration d’application, connectez-vous au [portail Azure](https://aka.ms/azconfig/portal). En haut à gauche de la page, sélectionnez **+ Créer une ressource**. Dans la zone **Rechercher dans la Place de marché**, entrez **App Configuration** et appuyez sur Entrée.

    ![Rechercher App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Sélectionnez **Configuration d’application** dans les résultats de la recherche, puis **Créer**.

3. Dans la page **Configuration d’application** > **Créer**, entrez les paramètres suivants.

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nom de la ressource** | Nom globalement unique | Entrez un nom de ressource unique à utiliser pour la ressource du magasin de configuration d’application. Le nom doit être une chaîne de 1 à 63 caractères et contenir uniquement des chiffres, des lettres et le caractère `-`. Le nom ne peut ni commencer ni se terminer par le caractère `-`, et n’accepte pas les caractères `-` consécutifs.  |
    | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser pour tester App Configuration. Si votre compte a un seul abonnement, il est automatiquement sélectionné et la liste déroulante **Abonnement** ne s’affiche pas. |
    | **Groupe de ressources** | *AppConfigTestResources* | Sélectionnez ou créez un groupe de ressources pour votre ressource du magasin de configuration d’application. Ce groupe est utile pour organiser plusieurs ressources que vous souhaitez supprimer en même temps que vous supprimez ce groupe de ressources. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Lieu** | *USA Centre* | Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre magasin de configuration d’application. Pour des performances optimales, créez la ressource dans la même région que les autres composants de votre application. |

    ![Créer une ressource de magasin de configuration d’application](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Sélectionnez **Créer**. Le déploiement peut prendre quelques minutes.

5. À la fin du déploiement, sélectionnez **Paramètres** > **Clés d’accès**. Prenez note de la chaîne de connexion de la clé primaire, en lecture seule ou en lecture-écriture. Vous utilisez cette chaîne de connexion plus tard pour configurer votre application, afin qu’elle communique avec le magasin de configuration d’application que vous avez créé.
