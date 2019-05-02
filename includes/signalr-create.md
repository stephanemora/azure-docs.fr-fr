---
title: Fichier Include
description: Fichier Include
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 57407606214d8d3a305476cfbfdabca9eee937e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690294"
---
1. Pour créer une nouvelle ressource de SignalR Service Azure, commencez par vous connecter au [portail Azure](https://portal.azure.com). Dans le coin supérieur gauche de la page, cliquez sur **+ Créer une ressource**. Dans la zone de texte **Search the Marketplace** (Rechercher sur la Place de marché), tapez **SignalR Service** et appuyez sur la touche **Entrée**.

2. Cliquez sur **SignalR Service** dans les résultats, puis sur **Créer**.

3. Dans la nouvelle page de paramètres **SignalR**, ajoutez les paramètres suivants pour votre nouvelle ressource SignalR :

    | Nom | Valeur recommandée | Description |
    | ---- | ----------------- | ----------- |
    | Nom de la ressource | *testsignalr* | Entrez un nom de ressource unique à utiliser pour la ressource SignalR. Le nom doit être une chaîne de 1 à 63 caractères et contenir uniquement des chiffres, des lettres et le caractère `-`. Le nom ne peut ni commencer ni se terminer par le caractère `-` et il n’accepte pas de caractères `-` consécutifs.|
    | Abonnement | Choisir votre abonnement |  Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour tester SignalR. Si votre compte a un seul abonnement, il est automatiquement sélectionné et la liste déroulante **Abonnement** ne s’affiche pas.|
    | Groupe de ressources | Créer un groupe de ressources nommé *SignalRTestResources*| Sélectionnez ou créez un groupe de ressources pour votre ressource SignalR. Ce groupe est utile pour organiser plusieurs ressources que vous pouvez supprimer en même temps en supprimant le groupe de ressources. Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Emplacement | *USA Est* | Utilisez **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre ressource SignalR. Pour des performances optimales, nous vous recommandons de créer la ressource dans la même région que les autres composants de votre application. |
    | Niveau tarifaire | *Gratuit* | Actuellement, seules les options **Gratuit** et **Standard** sont disponibles. |
    | Épingler au tableau de bord | ✔ | Cochez cette case pour que la ressource soit épinglée au tableau de bord afin de pouvoir la retrouver plus facilement. |

4. Cliquez sur **Créer**. Le déploiement peut prendre quelques minutes.

5. Une fois le déploiement terminé, cliquez sur **Clés** sous **PARAMÈTRES**. Copiez la chaîne de connexion de la clé primaire. Vous l’utiliserez ultérieurement pour configurer votre application de manière à utiliser la ressource Azure SignalR Service.

    La chaîne de connexion suit ce format :
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
