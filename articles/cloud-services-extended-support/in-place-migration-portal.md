---
title: Migrer à l’aide du portail
description: Découvrez comment migrer vers Azure Cloud Services (support étendu) à l’aide du portail Azure.
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286669"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migrer vers Azure Cloud Services (support étendu) à l’aide du portail Azure

Cet article explique comment utiliser le portail Azure pour migrer d’[Azure Cloud Services (classique)](../cloud-services/cloud-services-choose-me.md) vers [Azure Cloud Services (support étendu)](overview.md).

> [!IMPORTANT]
> La migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu) à l’aide de l’outil de migration est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Avant de commencer

**Vérifiez que vous êtes administrateur de l’abonnement.**

Pour effectuer cette migration, vous devez être ajouté en tant que coadministrateur de l’abonnement dans le [Portail Azure](https://portal.azure.com).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Abonnement**. Si vous ne le voyez pas, sélectionnez **Tous les services**.
3. Recherchez l’entrée d’abonnement appropriée, puis examinez le champ **MON RÔLE**. Pour un coadministrateur, la valeur doit être *Administrateur de compte*.

Si vous n'êtes pas en mesure d'ajouter un coadministrateur, contactez un administrateur de service ou un [coadministrateur](../role-based-access-control/classic-administrators.md) de l'abonnement afin qu'il vous ajoute.

**S’inscrire auprès du fournisseur de ressources de migration**

1. Inscrivez-vous auprès du fournisseur de ressources de migration `Microsoft.ClassicInfrastructureMigrate` et de la fonctionnalité d’évaluation `Cloud Services` sous l’espace de noms Microsoft.Compute à l’aide du [portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Attendez cinq minutes que l’inscription prenne effet, puis vérifiez l’état de l’approbation. 

## <a name="migrate-your-cloud-service-resources"></a>Migrer vos ressources Cloud Services

1. Accédez au [panneau du portail Cloud Services (classique)](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Sélectionnez ensuite le service cloud que vous souhaitez migrer.
3. Sélectionnez le panneau **Migrer vers ARM**.

    > [!NOTE]
    > Si vous migrez un service Cloud Services (classique) situé dans un réseau virtuel (classique), un message de bannière s’affiche pour vous inviter à déplacer le panneau du réseau virtuel (classique).
    > Vous êtes alors redirigé vers le panneau du réseau virtuel (classique) pour effectuer la migration des déploiements du réseau virtuel (classique) et des services Cloud Services (classique) qu’il contient.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Image illustrant le déplacement d’un réseau virtuel (classique) dans le portail Azure.":::
 

4. Vérifiez la migration. 

    Si la validation aboutit, tous les déploiements sont pris en charge et peuvent être préparés.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Image illustrant le panneau Migrer vers ARM dans le portail Azure.":::

    Si la validation échoue, la liste des scénarios non pris en charge s’affiche, et ceux-ci doivent être corrigés avant de poursuivre la migration. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Image illustrant une erreur de validation dans le portail Azure.":::

5. Préparez la migration.

    Si la préparation aboutit, la migration est prête pour la validation.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Image illustrant la réussite de la validation dans le portail Azure.":::

    Si la préparation échoue, examinez l’erreur, résolvez les problèmes éventuels, puis recommencez la préparation. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="Image illustrant une erreur de défaillance de validation.":::

      Au terme de la préparation, tous les services Cloud Services d’un réseau virtuel sont disponibles pour les opérations de lecture à l’aide du panneau Cloud Services (classique) et Cloud Services (support étendu) du portail Azure. Le déploiement de Cloud Services (support étendu) peut désormais être testé afin d’en garantir le bon fonctionnement avant de finaliser la migration. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="Image illustrant le test des API dans le panneau du portail.":::

6.  **(Facultatif)** Abandonner la migration. 
    
    Si vous choisissez d’interrompre la migration, utilisez le bouton **Abandonner** pour annuler les étapes précédentes. Le déploiement de Cloud Services (classique) est alors déverrouillé pour toutes les opérations CRUD.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="Image illustrant la réussite de la validation.":::

    Si l’abandon échoue, sélectionnez **Réessayer l’abandon**. Une nouvelle tentative doit permettre de résoudre le problème. Si ce n’est pas le cas, contactez le support. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Image illustrant un message d’erreur de défaillance de validation.":::

7.  Validez la migration.

    >[!IMPORTANT]
    > Une fois la migration validée, il n’est plus possible de la restaurer. 
    
    Entrez « Oui » pour confirmer et valider la migration. La migration est à présent terminée. Le déploiement des services Cloud Servies (support étendu) migrés est déverrouillé pour toutes les opérations. 

## <a name="next-steps"></a>Étapes suivantes
Consultez la section [Modifications après la migration](in-place-migration-overview.md#post-migration-changes) pour voir les modifications apportées aux fichiers de déploiement, à l’automatisation et à d’autres attributs de votre nouveau déploiement d’Azure Cloud Services (support étendu). 
