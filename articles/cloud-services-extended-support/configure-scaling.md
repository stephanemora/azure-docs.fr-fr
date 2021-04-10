---
title: Configuration de la mise à l’échelle pour Azure Cloud Services (support étendu)
description: Guide pratique d’activation des options de mise à l’échelle pour Azure Cloud Services (support étendu).
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: autoscale
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: dab3411319f40a111144a3e1c1f02f1b67d011b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935649"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Configuration des options de mise à l’échelle avec Azure Cloud Services (support étendu) 

Les conditions peuvent être configurées pour permettre le scale-in et le scale-out des déploiements Cloud Services (support étendu). Elles se basent sur l’utilisation du processeur, la charge de disque ou la charge réseau. 

Tenez compte des informations suivantes lorsque vous configurez la mise à l’échelle de vos déploiements Cloud Services :
- La mise à l’échelle a un impact sur l’utilisation des cœurs. Les grandes instances de rôle consomment plus de cœurs. De plus, la mise à l’échelle doit s’effectuer dans la limite de cœurs de l’abonnement. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md).
- La mise à l’échelle basée sur le seuil de messages de file d’attente est prise en charge. Pour plus d’informations, consultez [Bien démarrer avec Stockage File d’attente Azure](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Pour garantir la haute disponibilité de vos applications Cloud Service (support étendu), veillez à les déployer avec au moins deux instances de rôle.
- La mise à l’échelle automatique personnalisée ne peut se produire que si tous les rôles ont l’état **Prêt**.

## <a name="configure-and-manage-scaling"></a>Configuration et gestion de la mise à l’échelle

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
2. Sélectionnez le déploiement Cloud Service (support étendu) sur lequel vous souhaitez configurer la mise à l’échelle. 
3. Sélectionnez le panneau **Mise à l’échelle**. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Image illustrant la sélection de l’option Mise à l’échelle sur le Portail Azure":::

4. Une page affiche la liste de tous les rôles dans lesquels la mise à l’échelle peut être configurée. Sélectionnez le rôle à configurer. 
5. Sélectionnez le type d’échelle à configurer :
    - La **Mise à l’échelle manuelle** définit le nombre absolu d’instances.
        1. Sélectionnez **Mise à l’échelle manuelle**.
        2. Entrez le nombre d’instances visé par le scale-up ou le scale-down.
        3. Sélectionnez **Enregistrer**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Image illustrant la configuration de la mise à l’échelle manuelle sur le Portail Azure":::

        4. L’opération de mise à l’échelle commence immédiatement. 
        
    - La **Mise à l’échelle automatique personnalisée** permet de définir des règles qui régissent le volume à mettre à l’échelle. 
        1. Sélectionner **Mise à l’échelle automatique personnalisée**.
        2. Choisissez une métrique ou un nombre d’instances visé par la mise à l’échelle.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Image illustrant la configuration de la mise à l’échelle automatique personnalisée sur le Portail Azure":::

        3. En cas de mise à l’échelle basée sur une métrique, ajoutez les règles nécessaires pour obtenir les résultats souhaités.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Image illustrant la configuration des règles de mise à l’échelle automatique personnalisée sur le Portail Azure":::

        4. Sélectionnez **Enregistrer**.
        5. Les opérations de mise à l’échelle commencent dès qu’une règle est déclenchée.
        
6. Pour afficher ou ajuster des règles de mise à l’échelle appliquées à vos déploiements, sélectionnez l’onglet **Mettre à l’échelle**.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Image illustrant l’ajustement d’une règle de mise à l’échelle existante sur le Portail Azure":::

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
- Déployez une instance Cloud Services (support étendu) avec le [Portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).