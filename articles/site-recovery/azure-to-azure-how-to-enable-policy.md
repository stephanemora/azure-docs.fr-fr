---
title: Activer Azure Site Recovery pour vos machines virtuelles à l’aide d’Azure Policy
description: Découvrez comment activer la prise en charge de Policy pour protéger vos machines virtuelles à l’aide d’Azure Site Recovery.
author: rishjai-msft
ms.author: rishjai
ms.topic: how-to
ms.date: 04/27/2021
ms.custom: template-how-to
ms.openlocfilehash: 7707fb0688a10c1791556f0605b40cacc54b5a85
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961023"
---
# <a name="using-policy-with-azure-site-recovery-public-preview"></a>Utilisation de Policy avec Azure Site Recovery (Préversion publique)

Cet article explique comment configurer [Azure Site Recovery](./site-recovery-overview.md) pour vos ressources en utilisant Azure Policy. [Azure Policy](../governance/policy/overview.md) permet d’appliquer certaines règles d’entreprise à vos ressources Azure et d’évaluer la conformité de ces ressources.

## <a name="disaster-recovery-with-azure-policy"></a>Récupération d’urgence avec Azure Policy
Site Recovery vous aide à maintenir vos applications opérationnelles en cas de pannes zonales/régionales planifiées ou non. L’activation de Site Recovery sur vos machines à grande échelle via le portail Azure peut s’avérer difficile. Désormais, vous avez la possibilité d’activer Site Recovery en masse sur des groupes de ressources spécifiques (_étendue_ de la stratégie) par le biais du portail.

Azure Policy résout ce problème. Une fois que vous avez créé une stratégie de récupération d’urgence pour un groupe de ressources, Site Recovery sera activé automatiquement pour toutes les nouvelles machines virtuelles ajoutées au groupe de ressources. En outre, pour toutes les machines virtuelles déjà présentes dans le groupe de ressources, vous pouvez activer Site Recovery par le biais d’un processus appelé _Correction_ (détails ci-dessous).

>[!NOTE]
>L’_étendue_ de cette stratégie doit être au niveau du groupe de ressources.

## <a name="prerequisites"></a>Prérequis

- Comprenez comment attribuer une stratégie [ici](../governance/policy/assign-policy-portal.md).
- Apprenez-en plus sur l’architecture d’Azure pour la récupération d’urgence Azure [ici](./azure-to-azure-architecture.md).
- Consultez la matrice de prise en charge pour connaître la prise en charge de Policy par Azure Site Recovery :

**Scénario** | **Déclaration de prise en charge**
--- | ---
Disques managés | Prise en charge
Disques non managés  | Non pris en charge
Disques multiples | Prise en charge
Groupes à haute disponibilité | Prise en charge
Zones de disponibilité | Prise en charge
Machines virtuelles avec Azure Disk Encryption (ADE) | Non pris en charge
Groupes de placement de proximité (PPG) | Prise en charge
Disques avec clés gérées par le client (CMK) | Non pris en charge
Clusters d’espaces de stockage direct (S2D) | Non pris en charge
Modèle de déploiement Azure Resource Manager | Prise en charge
Modèle de déploiement classique | Non pris en charge
Récupération d’urgence zone à zone  | Prise en charge
Interopérabilité avec d’autres stratégies appliquées par défaut par Azure (le cas échéant) | Prise en charge

>[!NOTE]
>Dans les cas suivants, la récupération de site ne sera pas activée. Toutefois, elle apparaîtra comme _Non conforme_ dans Conformité des ressources : 
>1. Si une machine virtuelle non prise en charge est créée dans le cadre de la stratégie.
>1. Si une machine virtuelle fait partie d’un groupe à haute disponibilité et de PPG.

## <a name="create-a-policy-assignment"></a>Créer une attribution de stratégie
Dans cette section, vous allez créer une attribution de stratégie qui active Azure Site Recovery pour toutes les ressources nouvellement créées.
1. Accédez au **portail Azure**, puis à **Azure Policy**.
1. Sélectionnez **Affectations** du côté gauche de la page Azure Policy. Une affectation est une stratégie qui a été assignée pour être exécutée sur une étendue spécifique.
   :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assignments.png" alt-text="Capture d’écran de la sélection de la page Affectations dans la page Vue d’ensemble de la stratégie." border="false":::

1. Sélectionnez **Assigner une stratégie** en haut de la page **Stratégie - Affectations**.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assign-policy.png" alt-text="Capture d’écran de la sélection de l’option « Assigner une stratégie » dans la page Affectations." border="false":::

1. Dans la page **Assigner une stratégie**, définissez l’**étendue** en sélectionnant le bouton de sélection, puis en sélectionnant un abonnement et un groupe de ressources. Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Ensuite, appuyez sur le bouton **Sélectionner** en bas de la page **Étendue**.

1. Lancez le _Sélecteur de définition de stratégie_ en sélectionnant le bouton de sélection à côté de **Définition de stratégie**. _Recherchez « Configurer la récupération d’urgence sur des machines virtuelles en activant la réplication »_ , puis sélectionnez la stratégie.
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-policy-definition.png" alt-text="Capture d’écran de la sélection de « Définition de stratégie » dans la page Informations de base." border="true":::

1. Le **Nom de l’attribution** est automatiquement rempli avec le nom de stratégie que vous avez sélectionné, mais vous pouvez le modifier. Cela peut être utile si vous envisagez d’attribuer plusieurs stratégies Azure Site Recovery à la même étendue.

1. Sélectionnez **Suivant** pour configurer les propriétés Azure Site Recovery de la stratégie.

## <a name="configure-target-settings-and-properties"></a>Configurer les paramètres et les propriétés cibles
Vous êtes sur le point de créer une stratégie pour activer Azure Site Recovery. Configurons maintenant les paramètres et les propriétés cibles :
1. Vous vous trouvez dans la section _Paramètres_ du workflow _Assigner une stratégie_, qui ressemble à ceci : :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/specify-parameters.png" alt-text="Capture d’écran du réglage des paramètres dans la page Paramètres." border="true":::
1. Sélectionnez les valeurs appropriées pour ces paramètres :
    - **Région source** : Région source des machines virtuelles pour lesquelles la stratégie s’appliquera.
    >[!NOTE]
    >La stratégie s’appliquera à toutes les machines virtuelles appartenant à la région source dans l’étendue de la stratégie. Les machines virtuelles qui ne sont pas présentes dans la région source ne sont pas incluses dans _Conformité des ressources_.
    - **Région cible** : Emplacement où vos données de machines virtuelles sources sont répliquées. Site Recovery fournit la liste des régions cibles appropriées selon l’emplacement de la machine sélectionnée. Nous vous recommandons d’utiliser le même emplacement que celui du coffre Recovery Services.
    - **Groupe de ressources cible** : Groupe de ressources auquel appartiennent toutes vos machines virtuelles répliquées. Par défaut, Site Recovery crée un groupe de ressources dans la région cible.
    - **Groupe de ressources du coffre** : Groupe de ressources dans lequel se trouve le coffre Recovery Services.
    - **Coffre Recovery Services** : Coffre sur lequel toutes les machines virtuelles de l’étendue seront protégées.
    - **Réseau virtuel de récupération** : Choisissez un réseau virtuel dans la région cible à utiliser pour la machine virtuelle de récupération.
    - **Zone de disponibilité cible** : Entrez la zone de disponibilité de la région cible dans laquelle la machine virtuelle sera basculée.
    >[!NOTE]
    >Pour un scénario zone à zone, vous devez choisir la même région cible que la région source et choisir une zone de disponibilité différente dans _Zone de disponibilité cible_.     
    >Si certaines des machines virtuelles de votre groupe de ressources se trouvent déjà dans la zone de disponibilité cible, la stratégie ne sera pas appliquée à celles-ci si vous configurez la récupération d’urgence zone à zone.
    - **Effet** : Activez ou désactivez l’exécution de la stratégie. Sélectionnez _DeployIfNotExists_ pour activer la stratégie dès qu’elle est créée.

1. Sélectionnez **Suivant** pour décider la tâche de correction.

## <a name="remediation-and-other-properties"></a>Correction et autres propriétés
1. Les propriétés cibles pour Azure Site Recovery ont été configurées. Toutefois, cette stratégie ne prendra effet que pour les machines virtuelles nouvellement créées dans l’étendue de la stratégie. Elle peut être appliquée à des ressources existantes par le biais d’une tâche de correction une fois la stratégie attribuée. Vous pouvez créer une tâche de correction ici en cochant la case _Créer une tâche de correction_.

1. Azure Policy créera une [identité managée](../governance/policy/how-to/remediate-resources.md) qui aura des autorisations de propriétaire pour activer Azure Site Recovery pour les ressources de l’étendue.

1. Vous pouvez configurer un message de non-conformité personnalisé pour la stratégie dans l’onglet _Messages de non-conformité_.

1. Sélectionnez Suivant en bas de la page ou l’onglet _Vérifier + créer_ en haut de la page pour passer au segment suivant de l’Assistant Affectation.

1. Passez en revue les options sélectionnées, puis sélectionnez _Créer_ au bas de la page.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.