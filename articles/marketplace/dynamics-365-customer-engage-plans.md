---
title: Créer des plans Dynamics 365 pour Customer Engagement et Power Apps sur Microsoft AppSource (Place de marché Azure)
description: Configurez les plans de l’offre Dynamics 365 pour Customer Engagement et Power Apps si vous avez choisi d’activer votre offre pour la gestion des applications tierces.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 6c6863c8f044d1354fa1ed6ebea8b679994fc6d2
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108322263"
---
# <a name="create-dynamics-365-for-customer-engagement--power-apps-plans"></a>Créer des plans Dynamics 365 pour Customer Engagement et Power Apps

Si vous avez activé la gestion des licences d’application pour votre offre, l’onglet **Plans** apparaît comme illustré dans la capture d’écran suivante. Sinon, accédez à [Définir la configuration technique d’une offre Dynamics 365 pour Customer Engagement et Power Apps](dynamics-365-customer-engage-technical-configuration.md).

:::image type="content" source="./media/third-party-license/plan-tab.png" alt-text="Capture d’écran de l’onglet Vue d’ensemble du plan pour une offre Dynamics 365 pour Customer Engagement et Power Apps qui a été activée pour les licences d’applications tierces.":::

Vous devez définir au moins un plan si la gestion des licences d’application est activée pour votre offre. Vous pouvez créer divers plans avec différentes options pour la même offre. Ces plans (parfois appelés SKU) peuvent différer en termes de monétisation ou de niveaux de service. Plus tard, vous mapperez les ID de service de ces plans dans votre package de solution pour permettre à la plateforme Dynamics de vérifier la licence de ces plans au moment de l’exécution. Vous allez mapper l’ID de service de chaque plan dans votre package de solution. Cela permet à la plateforme Dynamics d’exécuter une vérification de licence par rapport à ces plans.

## <a name="create-a-plan"></a>Créer un plan

1. Vers le haut de la page **Vue d’ensemble du plan**, sélectionnez **+ Créer un plan**.
1. Dans la boîte de dialogue qui s’affiche, dans la zone **ID du plan**, entrez un ID de plan unique. Utilisez jusqu’à 50 caractères alphanumériques en minuscules, tirets ou traits de soulignement. Vous ne pouvez pas modifier l’ID de plan une fois que vous avez sélectionné **Créer**.
1. Dans la zone **Nom du plan**, entrez un nom unique pour ce plan. Utilisez un maximum de 50 caractères.
1. Sélectionnez **Create** (Créer).

## <a name="define-the-plan-listing"></a>Définir la liste des plans

Dans l’onglet **Liste des plans**, vous pouvez définir le nom et la description du plan comme vous souhaitez qu’ils apparaissent dans la Place de marché commerciale. Ces informations s’affichent dans la page de la liste Microsoft AppSource.

1. Dans la zone **Nom du plan**, le nom que vous avez fourni précédemment pour ce plan s’affiche. Vous pouvez le modifier à tout moment. Ce nom apparaîtra dans la Place de marché commerciale comme titre du plan logiciel de votre offre.
1. La zone **Description du plan** permet d’expliquer ce qui rend ce plan logiciel unique et de pointer les différences par rapport aux autres plans dans votre offre. Cette description peut contenir jusqu’à 500 caractères.
1. Sélectionnez **Enregistrer le brouillon**, puis, dans l’angle supérieur gauche, sélectionnez **Vue d’ensemble du plan**.

    :::image type="content" source="./media/third-party-license/bronze-plan.png" alt-text="La capture d’écran affiche le lien Vue d’ensemble du plan sur la page Liste des plans d’une offre dans Espace partenaires.":::

1. Pour créer un autre plan pour cette offre, en haut de la page **Vue d’ensemble du plan**, sélectionnez **+ Créer un plan**. Répétez ensuite les étapes de la section [Créer un plan](#create-a-plan). Sinon, si vous avez fini de créer des plans, passez à la section suivante : Copier les ID de service.

## <a name="copy-the-service-ids"></a>Copier les ID de service

Vous devez copier l’ID de service de chaque plan que vous avez créé afin de pouvoir le mapper à votre package de solution à l’étape suivante.

- Pour chaque plan que vous avez créé, copiez l’ID de service dans un emplacement sécurisé. Vous allez les ajouter à votre package de solution à l’étape suivante. L’ID de service est indiqué sur la page **Vue d’ensemble du plan** sous la forme `ISV name.offer name.plan ID`. Par exemple, Fabrikam.F365.bronze.

    :::image type="content" source="./media/third-party-license/service-id.png" alt-text="Capture d’écran de la page Vue d’ensemble du plan. L’ID de service du plan est mis en évidence.":::

## <a name="add-service-ids-to-your-solution-package"></a>Ajouter des ID de service à votre package de solution

1. Ajoutez les ID de service que vous avez copiés à l’étape précédente à votre package de solution. Pour en savoir plus, consultez [Ajout de métadonnées de licence à votre solution](https://go.microsoft.com/fwlink/?linkid=2162161&clcid=0x409) et [Créer un package AppSource de votre application](/powerapps/developer/data-platform/create-package-app-appsource).
1. Après avoir créé le fichier .zip du package CRM, chargez-le dans Stockage Blob Azure. Vous devrez fournir l’URL SAP du compte Stockage Blob Azure qui contient le fichier .zip du package CRM chargé.

## <a name="next-steps"></a>Étapes suivantes

- Accédez à [Définir la configuration technique d’une offre Dynamics 365 pour Customer Engagement et Power Apps](dynamics-365-customer-engage-technical-configuration.md) pour charger le package de la solution dans votre offre.
