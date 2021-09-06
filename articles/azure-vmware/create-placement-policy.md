---
title: Créer une stratégie de placement (préversion)
description: Découvrez comment créer une stratégie de positionnement dans une solution VMware Azure pour contrôler le placement des machines virtuelles sur les ordinateurs hôtes au sein d’un cluster par le biais du Portail Azure.
ms.topic: how-to
ms.date: 8/18/2021
ms.openlocfilehash: b7a29abfc0a59da445323c0aa651928c02e65a9b
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527917"
---
# <a name="create-a-placement-policy-in-azure-vmware-solution-preview"></a>Créer une stratégie de positionnement dans une solution VMware Azure (préversion)

>[!IMPORTANT]
>La stratégie de positionnement d’une solution VMware Azure (préversion) est actuellement en version préliminaire. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour utiliser la fonction de prévisualisation, [vous devez enregistrer à la fois _la politique de placement DRS_ et les _fonctions_ d'accès anticipé](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).  Recherchez et sélectionnez les fonctions, puis sélectionnez **Enregistrer**.

Dans la solution VMware Azure, les clusters d’un Cloud privé sont des ressources gérées. Par conséquent, le rôle cloudadmin ne peut pas apporter certaines modifications au cluster à partir du client vSphere, y compris la gestion des règles DRS (Distributed Resource Scheduler).

La fonctionnalité de stratégie de positionnement est disponible dans toutes les régions de solution VMware Azure.  Les stratégies de positionnement vous permettent de contrôler le placement des machines virtuelles sur les ordinateurs hôtes au sein d’un cluster par le biais de l’Portail Azure. Lorsque vous créez une stratégie de placement, elle comprend une règle DRS dans le cluster vSphere spécifié. Il comprend également une logique supplémentaire pour l’interopérabilité avec les opérations de solution VMware Azure.

Une stratégie de positionnement comporte au moins cinq composants requis : 

- **Nom** - Définit le nom de la politique et est soumis aux contraintes de dénomination des [ressources Azure](../azure-resource-manager/management/resource-name-rules.md).

- **Type** : définit le type de contrôle que vous souhaitez appliquer aux ressources contenues dans la stratégie.

- **Cluster** : Définit le cluster pour la politique. L’étendue d’une stratégie de positionnement est un cluster vSphere, donc seules les ressources du même cluster peuvent faire partie de la même stratégie de placement.

- **État** : Indique si la stratégie est activée ou non. Dans certains scénarios, une stratégie peut être désactivée automatiquement lorsqu’une règle en conflit est créée. Pour plus d’informations, consultez [Considérations](#considerations) ci-dessous.

- **Machine virtuelle** : Définit les machines virtuelles et les hôtes pour la politique. Selon le type de règle que vous créez, votre stratégie peut vous obliger à spécifier un nombre de machines virtuelles et d’ordinateurs hôtes. Pour plus d'informations, voir [Types de politiques de placement](#placement-policy-types) ci-dessous.


## <a name="prerequisites"></a>Configuration requise

- Vous devez avoir un accès de niveau _Contributeur_ au cloud privé pour gérer les politiques de placement.

- Les fonctions de _Stratégie de placement Data Replication Service_ et _d'accès anticipé_ [ sont enregistrées](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).


## <a name="placement-policy-types"></a>Types de stratégie de positionnement

### <a name="vm-vm-policies"></a>Stratégies Machine virtuelle - Machine virtuelle

**Les Stratégies Machine virtuelle - Machine virtuelle** spécifient si les machines virtuelles sélectionnées doivent s’exécuter sur le même ordinateur hôte ou être conservées sur des hôtes distincts.  En plus de choisir un nom et un cluster pour la stratégie, **les Stratégies Machine virtuelle - Machine virtuelle** nécessitent la sélection d’au moins deux machines virtuelles à affecter. L’attribution d’ordinateurs hôtes n’est pas obligatoire ou autorisée pour ce type de stratégie.

- **Les stratégies d’affinité Machine virtuelle - Machine virtuelle** indiquent à Data Replication Service d’essayer de conserver les machines virtuelles spécifiées ensemble sur le même hôte. Elle est utile pour des raisons de performances, par exemple.

- Les stratégies **Machine virtuelle-Anti-affinité de machine virtuelle** recommandent à Data Replication Service d’essayer de conserver les machines virtuelles spécifiées indépendamment les unes des autres sur des hôtes distincts. Cela est utile dans les scénarios où un problème avec un ordinateur hôte n’affecte pas plusieurs machines virtuelles au sein de la même stratégie.


### <a name="vm-host-policies"></a>Stratégies Machine virtuelle - Hôte

**Les stratégies Machine virtuelle - Hôte** spécifient si les machines virtuelles sélectionnées peuvent s’exécuter sur les ordinateurs hôtes sélectionnés.  Pour éviter les interférences avec les opérations gérées par la plateforme, telles que le mode de maintenance de l’ordinateur hôte et le remplacement de l’hôte, les stratégies **Machine virtuelle-Hôte** dans la solution VMware Azure sont toujours des règles préférentielles (également appelées règles « SHOULD »). Par conséquent, les politiques **Machine virtuelle-Hôte** [peuvent ne pas être honorées dans certains scénarios](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-793013E2-0976-43B7-9A00-340FA76859D0.html). Pour plus d'informations, consultez la section [Surveiller le fonctionnement d'une stratégie](#monitor-the-operation-of-a-policy) ci-dessous.

Certaines opérations de la plate-forme mettent dynamiquement à jour la liste des hôtes définis dans les stratégies **Machine virtuelle-Hôte**. Par exemple, lorsque vous supprimez un ordinateur hôte qui est membre d’une stratégie de placement, l’hôte est supprimé si plusieurs hôtes font partie de cette stratégie. En outre, si un ordinateur hôte fait partie d’une stratégie et doit être remplacé dans le cadre d’une opération gérée par la plateforme, la stratégie est mise à jour de manière dynamique avec le nouvel hôte.

En plus de choisir un nom et un cluster pour la stratégie, une stratégie **Machine virtuelle-hôte** nécessite de sélectionner au moins une machine virtuelle et un hôte à affecter à la stratégie.

- Les stratégies d'**affinité Hôte-Machine virtuelle** indiquent à Data Replication Service d’essayer d’exécuter les machines virtuelles spécifiées sur les ordinateurs hôtes définis.

- Les stratégies d'**affinité Hôte-Machine virtuelle** indiquent à Data Replication Service d’essayer d’exécuter les machines virtuelles spécifiées sur les ordinateurs hôtes définis.


## <a name="considerations"></a>Considérations

### <a name="cluster-scale-in"></a>Montée en puissance des clusters

La solution VMware Azure tente d’empêcher certaines violations de règle DRS lors de l’exécution d’opérations de mise à l’échelle de cluster.

Vous ne pouvez pas supprimer le dernier hôte d’une stratégie Machine virtuelle-hôte. Toutefois, si vous devez supprimer le dernier hôte de la stratégie, vous pouvez y remédier en ajoutant un autre hôte à la stratégie avant de supprimer l’ordinateur hôte du cluster. Vous pouvez également supprimer la stratégie de placement avant de supprimer l’ordinateur hôte.

Vous ne pouvez pas avoir une stratégie anti-affinité Machine virtuelle- Machine virtuelle avec plus de machines virtuelles que le nombre d’ordinateurs hôtes dans un cluster. Si la suppression d’un ordinateur hôte se traduirait par la réduction du nombre d’ordinateurs hôtes dans le cluster par rapport aux machines virtuelles, vous recevrez une erreur qui empêche l’opération. Vous pouvez y remédier en supprimant d’abord les machines virtuelles de la règle, puis en supprimant l’hôte du cluster.


### <a name="rule-conflicts"></a>Conflits de règles

Si des conflits de règle Data Replication Service sont détectés lorsque vous créez une stratégie Machine virtuelle-Machine virtuelle, cela entraîne la création de cette stratégie dans un état désactivé suivant le [comportement standard de la règle VMware DRS](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html). Pour plus d'informations sur l’affichage des conflits de règle, consultez la section [Surveiller le fonctionnement d'une stratégie](#monitor-the-operation-of-a-policy) ci-dessous.



## <a name="create-a-placement-policy"></a>Créer une stratégie de placement

Il n'y a pas de limite définie au nombre de stratégies que vous pouvez créer. Toutefois, plus vous créez de contraintes de placement, plus il est difficile pour vSphere DRS de déplacer efficacement les machines virtuelles au sein du cluster et de fournir les ressources requises par les charges de travail.      

Veillez à passer d’abord en revue les exigences de ce [type de stratégie](#placement-policy-types).

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Stratégies de placement** >  **+Créer**.

   >[!TIP]
   >Vous pouvez également sélectionner le cluster dans le volet vue d’ensemble de la stratégie de placement, puis sélectionner **Créer**.
   >
   >:::image type="content" source="media/placement-policies/create-placement-policy-cluster.png" alt-text="Capture d’écran montrant une autre option pour créer une stratégie de positionnement.":::



   :::image type="content" source="media/placement-policies/create-placement-policy.png" alt-text="Capture d’écran montrant comment démarrer le processus de création d’une stratégie de placement Machine virtuelle-Machine virtuelle." lightbox="media/placement-policies/create-placement-policy.png":::


1. Indiquez un nom descriptif, sélectionnez le type de stratégie, puis sélectionnez le cluster dans lequel la stratégie est créée. Ensuite, sélectionnez **Activer**.

   >[!WARNING]
   >Si vous désactivez la stratégie, la stratégie et la règle DRS sous-jacente sont créées, mais les actions de stratégie sont ignorées jusqu’à ce que vous activiez la stratégie. 

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-host-affinity-1.png" alt-text="Capture d’écran montrant les options de stratégie de positionnement." lightbox="media/placement-policies/create-placement-policy-vm-host-affinity-1.png":::   

1. Si vous avez sélectionné **l'affinité Machine virtuelle-Hôte** ou **l'anti-affinité Machine virtuelle-Hôte** comme type, sélectionnez **+ Ajouter un hôte** et les hôtes à inclure dans la stratégie. Vous pouvez sélectionner plusieurs hôtes.

   >[!NOTE]
   >Le volet Sélectionner les ordinateurs hôtes affiche le nombre de stratégies Machine virtuelle-Hôte associées à l’hôte et le nombre total de machines virtuelles contenues dans les stratégies associées.
   >
   >:::image type="content" source="media/placement-policies/hosts-associated-policies-vms.png" alt-text="Capture d’écran montrant le nombre de VM-Host stratégies associées à l’hôte, ainsi que le nombre de machines virtuelles contenues dans les stratégies associées.":::


1. Sélectionnez **+ Ajouter une machine virtuelle** et les machines virtuelles à inclure dans la stratégies. Vous pouvez sélectionner plusieurs machines virtuelles.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-2.png" alt-text="Capture d’écran montrant la liste des machines virtuelles sélectionnées.":::
   
   >[!NOTE]
   >Le volet Sélectionner les ordinateurs hôtes affiche le nombre de stratégies Machine virtuelle-Hôte associées à l’hôte et le nombre total de machines virtuelles contenues dans les stratégies associées. 

1. Une fois que vous avez terminé d’ajouter les machines virtuelles de votre choix, sélectionnez **Ajouter des machines virtuelles**. 

1. Sélectionnez **Suivant : Réviser et créer** pour réviser votre stratégie. 

1. Sélectionnez **Créer une stratégie**. Si vous souhaitez apporter des modifications, sélectionnez **Retour : Principes de base**.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-3.png" alt-text="Capture d’écran montrant les paramètres de stratégie de positionnement avant sa création.":::

1. Après la création de la stratégie de placement, sélectionnez **Actualiser** pour la voir dans la liste.

   :::image type="content" source="media/placement-policies/create-placement-policy-8.png" alt-text="Capture d’écran montrant la stratégie de positionnement activée après sa création." lightbox="media/placement-policies/create-placement-policy-8.png":::



## <a name="edit-a-placement-policy"></a>Modifier une stratégie de placement

Vous pouvez modifier l’état d’une stratégie, ajouter une nouvelle ressource ou annuler l’affectation d’une ressource existante.

### <a name="change-the-policy-state"></a>Modifier l’état de la stratégie

Vous pouvez changer l'état d'une stratégie en **Activé** ou **Désactivé**. 

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Stratégies de placement**.

1. Pour la stratégie que vous souhaitez modifier, sélectionnez **Plus** (...), puis sélectionnez **Modifier**.

   >[!TIP]
   >Vous pouvez désactiver une stratégie à partir de la vue d'ensemble des stratégies de placement en sélectionnant **Désactiver** dans le menu déroulant Paramètres. Vous ne pouvez pas activer une stratégie à partir de la liste déroulante Paramètres.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="Capture d’écran montrant comment modifier une stratégie de positionnement." lightbox="media/placement-policies/edit-placement-policy.png":::

1.  Si la stratégie est activée mais que vous souhaitez la désactiver, sélectionnez **Désactivé** puis **Désactivé** dans le message de confirmation. Dans le cas contraire, si la stratégie est désactivée et que vous souhaitez l’activer, sélectionnez **Activer**.

1.  Sélectionner **Évaluation+mise à jour**. 
 
1.  Vérifiez les changements et sélectionnez **Mettre à jour la politique**. Si vous souhaitez apporter des modifications, sélectionnez **Retour : Principes de base**.


### <a name="update-the-resources-in-a-policy"></a>Mettre à jour les ressources d’une stratégie

Vous pouvez ajouter de nouvelles ressources, telles qu’une machine virtuelle ou un ordinateur hôte, à une stratégie ou supprimer des ressources existantes. 

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Stratégies de placement**.

1. Pour la stratégie que vous souhaitez modifier, sélectionnez **Plus** (...), puis sélectionnez **Modifier**.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="Capture d’écran montrant comment modifier les ressources dans une stratégie de placement." lightbox="media/placement-policies/edit-placement-policy.png":::

   - Pour supprimer une ressource existante, sélectionnez une ou plusieurs ressources que vous souhaitez supprimer et sélectionnez **Annuler l’affectation**. 

      :::image type="content" source="media/placement-policies/edit-placement-policy-unassign.png" alt-text="Capture d’écran montrant comment supprimer une ressource existante d’une stratégie de placement.":::

   - Pour ajouter une nouvelle ressource, sélectionnez **Modifier la machine virtuelle** ou **Modifier l'hôte**, sélectionnez la ressource que vous souhaitez ajouter, puis sélectionnez **Enregistrer**. 

1. Sélectionnez **Suivant : Vérifier et créer**. 

1. Vérifiez les changements et sélectionnez **Mettre à jour la politique**.  Si vous souhaitez apporter des modifications, sélectionnez **Retour : Principes de base**.



## <a name="delete-a-policy"></a>Supprimer une stratégie

Vous pouvez supprimer une stratégie de placement et sa règle DRS correspondante. 

1. Dans votre cloud privé Azure VMware Solution, sous **Gérer**, sélectionnez **Stratégies de placement**.

1. Pour la stratégie que vous souhaitez modifier, sélectionnez **Plus** (...), puis sélectionnez **Modifier**.

   :::image type="content" source="media/placement-policies/delete-placement-policy.png" alt-text="Capture d’écran montrant comment modifier une stratégie de positionnement." lightbox="media/placement-policies/delete-placement-policy.png":::

1. Sélectionnez **Supprimer** sur le message de confirmation.



## <a name="monitor-the-operation-of-a-policy"></a>Surveiller le fonctionnement d’une stratégie

Utilisez le client vSphere pour surveiller le fonctionnement de la règle DRS correspondante d’une stratégie de positionnement. 

En tant que titulaire du rôle cloudadmin, vous pouvez afficher, mais pas modifier, les règles DRS créées par une politique de placement dans l'onglet Configure du cluster sous les règles Machines virtuelles/Hôte. Elle vous permet d’afficher des informations supplémentaires, par exemple si les règles DRS sont dans un état de conflit.

En outre, vous pouvez surveiller diverses opérations de règle DRS, telles que des recommandations et des erreurs, à partir de l’onglet moniteur du cluster.


## <a name="faqs"></a>Foire aux questions

### <a name="are-these-the-same-as-drs-affinity-rules"></a>Sont-elles identiques aux règles d’affinité DRS ?
Oui, et Non. Bien que vSphere DRS implémente l’ensemble actuel de stratégies, nous avons simplifié l’expérience. La modification des groupes de machines virtuelles et des groupes d’hôtes est une opération fastidieuse, surtout lorsque les hôtes sont éphémères par nature et peuvent être remplacés dans un environnement Cloud. À mesure que les ordinateurs hôtes sont remplacés dans l’inventaire vSphere dans un environnement local, l’administrateur vSphere doit modifier le groupe hôte pour s’assurer que les contraintes de placement Machine virtuelle-Hôte souhaitées continuent de rester en vigueur. Les stratégies de positionnement dans la solution VMware Azure mettent à jour les groupes hôtes quand un ordinateur hôte est pivoté ou modifié. De même, si vous mettez à l’échelle dans un cluster, le groupe hôte est automatiquement mis à jour, le cas échéant. Cela élimine la surcharge liée à la gestion des groupes hôtes pour le client.


### <a name="as-this-is-an-existing-functionality-available-in-vcenter-why-cant-i-use-it-directly"></a>Étant donné qu’il s’agit d’une fonctionnalité existante disponible dans vCenter, pourquoi ne puis-je pas l’utiliser directement ? 

Azure VMware Solution fournit des clouds privés VMware dans Azure. Dans cette infrastructure VMware gérée, Microsoft gère les clusters, les hôtes, les banques de serveurs et les commutateurs virtuels distribués dans le cloud privé. En même temps, le locataire est responsable de la gestion des charges de travail déployées sur le cloud privé. Par conséquent, le client qui administre le cloud privé [n’a pas le même jeu de privilèges](concepts-identity.md) que celui disponible pour l’administrateur VMware dans un déploiement local. 

En outre, l’absence de granularité souhaitée dans les privilèges vSphere présente des difficultés lors de la gestion de l’emplacement des charges de travail sur le cloud privé. Par exemple, les règles vSphere DRS couramment utilisées localement pour définir des règles d’affinité et d’anti-affinité ne peuvent pas être utilisées telles quelles dans un environnement de Cloud VMware, car certaines d’entre elles peuvent bloquer l’exploitation quotidienne du Cloud privé. Les stratégies de positionnement vous permettent de définir ces règles via le portail AVS, ce qui évite d’avoir à utiliser des règles DRS. Couplées à une expérience simplifiée, elles garantissent également que les règles n’ont pas d’impact sur les activités de maintenance et d’exploitation de l’infrastructure quotidienne. 


###  <a name="what-caveats-should-i-be-aware-of"></a>Quels sont les avertissements à connaître ?

Les règles de mise en place du VM-Host doivent être des règles de gestion et ne sont pas prises en charge par les stratégies de positionnement. 

Les règles SHOULD Machines Virtuelles-Hôte doivent être des règles préférentielles, où vSphere DRS tente d’adapter les règles dans la mesure du possible. Le Data Replication Service de vSphere peut avoir recours à des machines virtuelles vMotion soumises au VM-Host doit occasionnellement s’assurer que les charges de travail obtiennent les ressources dont ils ont besoin. Il s’agit d’un comportement vSphere DRS standard, et la fonctionnalité des stratégies de positionnement ne change pas le comportement DRS vSphere sous-jacent.

Si vous créez des règles en conflit, ces conflits peuvent apparaître sur le vCenter et les règles nouvellement définies peuvent ne pas être prises en compte. Il s’agit d’un comportement DRS standard vSphere, les journaux qui peuvent être observés dans le vCenter.
