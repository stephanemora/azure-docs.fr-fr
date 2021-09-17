---
title: Créer des machines virtuelles dans un groupe identique Flexible à l’aide du portail Azure
description: Découvrez comment créer un groupe de machines virtuelles identiques en mode d’orchestration Flexible dans le portail Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: cef23894759b0b2dca7098ef1c430548d1720406
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699209"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Préversion : Créer des machines virtuelles dans un groupe identique Flexible à l’aide du portaiI Azure

**S’applique à :** :heavy_check_mark: Groupes identiques flexibles

Cet article décrit l’utilisation du portail Azure pour créer un groupe identique de machines virtuelles mode d’orchestration Flexible. Pour plus d’informations sur les groupes identiques Flexibles, consultez  [mode d’orchestration Flexible pour les groupes identiques de machines virtuelles](flexible-virtual-machine-scale-sets.md). 


> [!IMPORTANT]
> Les groupes de machines virtuelles identiques en mode d’orchestration Flexible sont actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité en préversion publique décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="register-for-flexible-orchestration-mode"></a>S’inscrire au mode d’orchestration Flexible

Avant de pouvoir déployer des groupes de machines virtuelles identiques en mode d’orchestration Flexible, vous devez inscrire la fonctionnalité d’évaluation pour votre abonnement. L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes.

Pendant la préversion du mode d’orchestration flexible pour les groupes identiques, utilisez le portail Azure *préversion* lié dans les étapes ci-dessous. 

1. Connectez-vous au portail Azure à l’adresse https://preview.portal.azure.com.
1. Accédez à vos **Abonnements**.
1. Accédez à la page des informations de l’abonnement pour lequel vous souhaitez créer un groupe identique en mode d’orchestration Flexible en sélectionnant le nom de l’abonnement.
1. Dans le menu, sous **Paramètres**, sélectionnez **Fonctionnalités en préversion**.
1. Sélectionnez les quatre fonctionnalités d’orchestrateur à activer : *VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview* et *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*.
1. Sélectionnez **Inscription**.

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul. 

1. Dans le menu, sous **Paramètres**, sélectionnez **Fournisseurs de ressources**.
1. Sélectionnez `Microsoft.compute`.
1. Sélectionnez **Ré-inscrire**.


## <a name="get-started-with-flexible-orchestration-mode"></a>Démarrer avec le mode d’orchestration Flexible

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Créez un groupe de machines virtuelles identiques en mode d’orchestration Flexible avec le portail Azure.

Pendant la préversion du mode d’orchestration flexible pour les groupes identiques, utilisez le portail Azure *préversion* lié dans les étapes ci-dessous. 

1. Connectez-vous au portail Azure à l’adresse https://preview.portal.azure.com.
1. Dans la barre de recherche, recherchez et sélectionnez **Groupes de machines virtuelles identiques**.
1. Sélectionnez **Créer** dans la page **Groupes de machines virtuelles identiques**.
1. Dans la page **Créer un groupe de machines virtuelles identiques**, consultez la section **Orchestration**.
1. Sous **Mode d’orchestration**, sélectionnez l’option **Flexible**.
1. Spécifiez la valeur **Nombre de domaines d’erreur**.
1. Terminez la création de votre groupe identique. Pour plus d’informations sur la création d’un groupe identique, consultez [Créer un groupe identique dans le portail Azure](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set).


### <a name="next-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>Ensuite, ajoutez une machine virtuelle au groupe identique en mode d’orchestration Flexible.

1. Dans la barre de recherche, recherchez et sélectionnez **Machines virtuelles**.
1. Sélectionnez **Ajouter** dans la page **Machines virtuelles**.
1. Dans l’onglet **Informations de base**, consultez la section **Détails de l’instance**.
1. Ajoutez votre machine virtuelle au groupe identique en mode d’orchestration Flexible en sélectionnant le groupe identique sous **Options de disponibilité**. Vous pouvez ajouter la machine virtuelle à un groupe identique dans la même région, la même zone et le même groupe de ressources.
1. Accédez à l’onglet **Mise en réseau** et définissez votre connectivité sortante de manière explicite.

    > [!IMPORTANT]
    > Une connectivité sortante définie de manière explicite est requise pour les groupes de machines virtuelles identiques avec une orchestration flexible. Consultez la [connectivité réseau sortante explicite ](flexible-virtual-machine-scale-sets.md#explicit-network-outbound-connectivity-required) pour plus d’informations.

1. Terminez la création de votre machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez comment créer un groupe identique Flexible avec l’interface CLI Azure.](flexible-virtual-machine-scale-sets-cli.md)