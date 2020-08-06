---
title: Comment intégrer Windows Admin Center à Azure Security Center | Microsoft Docs
description: Cet article explique comment intégrer Azure Security Center à Windows Admin Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9eae210818cf623078090503deefc6295dab7164
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076353"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Intégrer Azure Security Center à Windows Admin Center

Windows Admin Center est un outil de gestion pour vos serveurs Windows. Il s’agit d’un emplacement unique permettant aux administrateurs système d’accéder à la plupart des outils d’administration les plus couramment utilisés. Dans Windows Admin Center, vous pouvez directement intégrer vos serveurs locaux dans Azure Security Center. Vous pouvez ensuite afficher un résumé de vos recommandations et alertes de sécurité directement dans l’expérience Windows Admin Center.

> [!NOTE]
> Votre abonnement Azure et l’espace de travail Log Analytics associé doivent tous deux être activés pour le niveau Standard de Security Center afin d’activer l’intégration de Windows Admin Center.
> Le niveau Standard est gratuit pendant les 30 premiers jours si vous ne l’avez pas déjà utilisé sur l’abonnement et l’espace de travail. Pour plus d’informations, consultez la [page d’informations de tarification](security-center-pricing.md).
>

Une fois que vous avez correctement intégré un serveur de Windows Admin Center dans Azure Security Center, vous pouvez :

* Afficher les alertes de sécurité et les recommandations à l’intérieur de l’extension Security Center dans Windows Admin Center
* Afficher la situation de sécurité et récupérer des informations détaillées supplémentaires sur vos serveurs gérés par Windows Admin Center dans Security Center au sein du portail Azure (ou via une API)

En combinant ces deux outils, Security Center devient votre fenêtre unique pour afficher toutes vos informations de sécurité, quelle que soit la ressource : protection de vos serveurs locaux gérés par Windows Admin Center, de vos machines virtuelles et de toutes les charges de travail PaaS supplémentaires.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Intégration des serveurs gérés par Windows Admin Center dans Security Center

1. Dans Windows Admin Center, sélectionnez l’un de vos serveurs, puis dans le volet **Outils**, sélectionnez l’extension Azure Security Center :

    ![Extension Azure Security Center dans Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Si le serveur est déjà intégré à Security Center, la fenêtre de configuration n’apparaît pas.

1. Cliquez sur **Se connecter à Azure et configurer**.
    ![Intégration de l’extension Windows Admin Center à Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Suivez les instructions pour connecter votre serveur à Security Center. Une fois que vous avez entré et confirmé les informations nécessaires, Security Center effectue les modifications de configuration nécessaires pour s’assurer que toutes les conditions suivantes sont remplies :
    * Une passerelle Azure est inscrite.
    * Le serveur dispose d’un espace de travail pour les rapports et d’un abonnement associé.
    * La solution Log Analytics de niveau Standard de Security Center est activée sur l’espace de travail. Cette solution fournit les fonctionnalités de niveau Standard de Security Center pour *tous* les serveurs et toutes les machines virtuelles qui produisent des rapports sur cet espace de travail.
    * La tarification du niveau Standard de Security Center pour les machines virtuelles est activée sur l’abonnement.
    * L’agent Log Analytics est installé sur le serveur et configuré pour rendre compte à l’espace de travail sélectionné. Si le serveur est déjà dans un autre espace de travail, il est configuré pour créer des rapports sur l’espace de travail nouvellement sélectionné.

    > [!NOTE]
    > L’intégration des recommandations peut prendre un certain temps. En fait, en fonction de l’activité de votre serveur, vous risquez de ne recevoir *aucune* alerte. Pour générer des alertes de test pour vérifier que vos alertes fonctionnent correctement, suivez les instructions de [la procédure de validation d’alerte](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Affichage des recommandations et des alertes de sécurité dans Windows Admin Center

Une fois intégré, vous pouvez afficher vos alertes et vos recommandations directement dans la zone Azure Security Center de Windows Admin Center. Cliquez sur une recommandation ou une alerte pour l’afficher dans le portail Azure. Là, vous obtiendrez des informations supplémentaires et découvrirez comment corriger les problèmes.

[![Alertes et recommandations de Security Center comme elles sont affichées dans Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Affichage des recommandations et des alertes de sécurité pour les serveurs gérés par Windows Admin Center dans Security Center
À partir d’Azure Security Center :

* Pour afficher les recommandations de sécurité pour tous les serveurs Windows Admin Center, ouvrez **Calcul et applications** et cliquez sur l’onglet **Machines virtuelles et ordinateurs**. Filtrez la liste par « Serveur » comme indiqué ici :

    [![Afficher les recommandations de sécurité pour les serveurs gérés par Windows Admin Center](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Pour afficher les alertes de sécurité pour tous les serveurs Windows Admin Center, ouvrez **Alertes de sécurité**. Cliquez sur **Filtre** et assurez-vous que **seul** « Non Azure » est sélectionné :

    ![Filtrer les alertes de sécurité pour les serveurs gérés par Windows Admin Center](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Afficher les alertes de sécurité pour les serveurs gérés par Windows Admin Center](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)