---
title: Inscrire votre contrat Entreprise Azure auprès de Cloudyn | Microsoft Docs
description: Utilisez votre contrat Entreprise pour l’inscrire auprès de Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 36f53203a7d75fc06f872e9cc9ef9379944df108
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52273561"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Inscrire un Accord Entreprise Azure et afficher les données de coût

Utilisez votre contrat Entreprise Azure pour l’inscrire auprès de Cloudyn. Votre inscription vous donne accès au portail Cloudyn. Ce guide de démarrage rapide décrit le processus d’inscription nécessaire pour créer un abonnement d’évaluation Cloudyn et vous connecter au portail Cloudyn. Il explique également comment afficher immédiatement des données de coût.

Azure Cost Management offre des fonctionnalités similaires à Cloudyn. Azure Cost Management est une solution native de gestion des coûts Azure. Il vous permet d’analyser les coûts, de créer et de gérer des budgets, d’exporter des données, et de consulter des recommandations d’optimisation et d’agir en conséquence pour dépenser moins. Pour plus d’informations, consultez [Azure Cost Management](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="register-with-cloudyn"></a>S’inscrire à Cloudyn

1. Dans le portail Azure, cliquez sur **Gestion des coûts + Facturation** dans la liste des services.
2. Sous **Vue d’ensemble**, cliquez sur **Cloudyn**  
    ![Page de Cloudyn](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Dans la page **Cloudyn**, cliquez sur **Accéder à Cloudyn** pour ouvrir la page d’inscription de Cloudyn dans une nouvelle fenêtre.
4. Sur la page d’inscription à une évaluation du portail Cloudyn, tapez le nom de votre société, sélectionnez **Azure Individual Subscription Owner** (Propriétaire d’abonnement individuel Azure).  
    ![inscription à une évaluation](./media/quick-register-ea/trial-reg.png)
5. Entrez votre clé d’API d’inscription au portail d’entreprise. Si vous n’avez pas votre clé, cliquez sur le [Enterprise Portal](https://ea.azure.com) lier et procédez comme suit :
  1. Connectez-vous au site web Azure Enterprise, cliquez sur **Rapports**, sur **Clé d’accès API**, puis copiez votre clé primaire.  
    ![Clé API EA](./media/quick-register-ea/ea-key.png)
  3. Revenez à la page d’inscription et collez votre clé API.
6. Acceptez les conditions d’utilisation, puis validez votre clé. Cliquez sur **Next** (Suivant) pour autoriser Cloudyn à collecter des données de ressource Azure. Les données collectées incluent les données d’utilisation, de performances, de facturation et de balise de vos abonnements.  
    ![validation de clé](./media/quick-register-ea/ea-key-validated.png)
7. L’option pour **inviter d’autres parties prenantes** permet d’ajouter des utilisateurs en tapant leurs adresses e-mail. Lorsque vous avez terminé, cliquez sur **Suivant**. Selon la taille de votre inscription Azure, jusqu’à 24 heures peuvent être nécessaires pour que toutes vos données de facturation soient ajoutées à Cloudyn.
8. Cliquez sur le bouton d’**accès à Cloudyn** pour ouvrir le portail Cloudyn. La page de **gestion des comptes cloud** devrait alors afficher les informations de votre compte EA inscrit.

Pour regarder la vidéo d’un tutoriel sur l’inscription de votre contrat Entreprise, consultez [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé les informations de votre contrat Entreprise Azure pour vous inscrire auprès de Cloudyn. Vous vous êtes également connecté au portail Cloudyn et avez affiché des données de coût. Pour en savoir plus sur Cloudyn, suivez le tutoriel sur Cloudyn.

> [!div class="nextstepaction"]
> [Réviser l’utilisation et les coûts](./tutorial-review-usage.md)
