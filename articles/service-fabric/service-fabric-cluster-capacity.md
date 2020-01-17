---
title: Planification de la capacité du cluster Service Fabric
description: Considérations en matière de planification de la capacité du cluster Service Fabric. Types de nœuds, opérations, durabilité et niveaux de fiabilité
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463307"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Considérations en matière de planification de la capacité du cluster Service Fabric
Pour un déploiement de production, la planification de la capacité est une étape importante. Voici certains éléments que vous devez prendre en compte dans ce processus.

* Nombre de types de nœuds avec lesquels votre cluster doit démarrer
* Propriétés de chaque type de nœud (taille, principal ou non, accessibilité sur Internet, nombre de machines virtuelles, etc.)
* Caractéristiques de fiabilité et de durabilité du cluster

> [!NOTE]
> Vous devez au minimum passer en revue toutes les valeurs de la stratégie de mise à niveau **Non autorisé** lors de la planification. Cela permet de s’assurer de la bonne définition des valeurs et de réduire le risque de perdre votre cluster plus tard en raison de paramètres de configuration système non modifiables. 
> 

Nous allons présenter brièvement chacun de ces éléments.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Nombre de types de nœuds avec lesquels votre cluster doit démarrer
Vous devez d’abord déterminer l’utilisation du cluster que vous créez.  Quels types d’applications planifiez-vous de déployer dans ce cluster ? Si vous n’êtes pas sûr de ce à quoi va servir le cluster, vous n’êtes probablement pas encore prêt pour le processus de planification de capacité.

Établissez le nombre de types de nœuds avec lesquels votre cluster doit démarrer.  Chaque type de nœud est mappé à un groupe de machines virtuelles identiques. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Par conséquent, pour savoir combien de types de nœud vous avez besoin, vous devez vous poser les questions suivantes :

* Votre application inclut-elle plusieurs services ? Si oui, ces services doivent-ils être publics ou accessibles sur Internet ? Les applications standard contiennent un service de passerelle frontale qui reçoit des entrées d’un client, et un ou plusieurs services principaux qui communiquent avec les services frontaux. Par conséquent, dans ce cas, vous avez besoin d’au moins deux types de nœuds.
* Vos services (qui composent votre application) ont-ils des besoins d’infrastructure différents tels qu’une RAM plus volumineuse ou des cycles processeur plus élevés ? Supposons, par exemple, que l’application que vous voulez déployer contient un service frontal et un service principal. Le service frontal peut s’exécuter sur des machines virtuelles plus petites (par exemple, de taille D2) ayant des ports ouverts sur Internet.  Toutefois, le service principal est gourmand en calculs et doit s’exécuter sur des machines virtuelles plus grandes (par exemple, de taille D4, D6, D15) qui ne sont pas connectées à Internet.
  
  Dans cet exemple, même si vous pouvez décider de placer tous les services sur un seul type de nœud, nous vous recommandons de les placer dans un cluster avec deux types de nœuds.  Ainsi, chaque type de nœud a des propriétés distinctes comme la connectivité Internet ou la taille de machine virtuelle. Le nombre de machines virtuelles peut être mis à l’échelle indépendamment, également.  
* Étant donné que vous ne pouvez pas prédire l’avenir, prenez les informations que vous connaissez et choisissez le nombre de types de nœuds dont vos applications ont besoin pour démarrer. Vous pourrez toujours ajouter ou supprimer des types de nœuds ultérieurement. Un cluster Service Fabric doit comprendre au moins un type de nœud.

## <a name="the-properties-of-each-node-type"></a>Propriétés de chaque type de nœud
Le **type de nœud** peut être considéré comme l’équivalent des rôles dans Cloud Services. Les types de nœuds définissent les tailles de machine virtuelle, le nombre de machines virtuelles et leurs propriétés. Chaque type de nœud qui est défini dans un cluster Service Fabric est mappé à un [groupe de machines virtuelles identiques distinct](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Chaque type de nœud est un groupe identique distinct et peut faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Pour plus d’informations sur les relations entre des types de nœuds et des groupes machines virtuelles identiques, sur la façon d’ouvrir une session RDP sur l’une des instances, sur la façon d’ouvrir de nouveaux ports, etc., consultez [Types de nœuds de cluster Service Fabric](service-fabric-cluster-nodetypes.md).

Un cluster Service Fabric peut se composer de plusieurs types de nœuds. Dans ce cas, le cluster se compose d’un type de nœud principal et d’un ou de plusieurs types de nœuds non principaux.

Un type de nœud unique ne peut pas mettre à l’échelle de façon fiable plus de 100 nœuds par groupe de machines virtuelles identiques pour des applications Service Fabric. Pour atteindre plus de 100 nœuds de façon fiable, vous devez ajouter des groupes de machines virtuelles identiques.

### <a name="primary-node-type"></a>Type de nœud principal

Les services système Service Fabric (par exemple, le service Cluster Manager ou le service de magasin d’images) sont placés sur le type de nœud principal. 

![Capture d’écran d’un cluster qui a deux types de nœuds][SystemServices]

* La **taille minimale des machines virtuelles** pour le type de nœud principal est déterminée par le **niveau de durabilité** que vous choisissez. Le niveau de durabilité par défaut est Bronze. Pour plus d’informations, consultez [Caractéristiques de durabilité du cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Le **nombre minimal de machines virtuelles** pour le type de nœud principal est déterminé par le **niveau de fiabilité** que vous choisissez. Le niveau de fiabilité par défaut est Silver. Pour plus d’informations, consultez [Caractéristiques de fiabilité du cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster).  

À partir du modèle Azure Resource Manager, le type de nœud principal est configuré avec l’attribut `isPrimary` sous la [définition du type de nœud](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Type de nœud non principal

Un cluster avec plusieurs types de nœuds comprend un type de nœud principal, les autres étant des types de nœuds non principaux.

* La **taille minimale des machines virtuelles** pour les types de nœuds non principaux est déterminée par le **niveau de durabilité** que vous choisissez. Le niveau de durabilité par défaut est Bronze. Pour plus d’informations, consultez [Caractéristiques de durabilité du cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Le **nombre minimal de machines virtuelles** pour les types de nœuds non principaux est un. Toutefois, vous devez choisir ce nombre en fonction du nombre de réplicas des applications/services que vous voulez exécuter dans ce type de nœud. Le nombre de machines virtuelles dans un type de nœud peut être augmenté une fois le cluster déployé.

## <a name="the-durability-characteristics-of-the-cluster"></a>Caractéristiques de durabilité du cluster
Le niveau de durabilité est utilisé pour indiquer au système les privilèges dont disposent vos machines virtuelles avec l’infrastructure Azure sous-jacente. Dans le type de nœud principal, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, un redémarrage de la machine virtuelle, une réinitialisation de la machine virtuelle ou une migration de machine virtuelle) qui influe sur la configuration requise du quorum pour les services système et vos services avec état. Dans les types de nœuds non principaux, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (comme le redémarrage de la machine virtuelle, la réinitialisation de la machine virtuelle, la migration de machine virtuelle, etc.) qui influe sur la configuration requise du quorum pour vos services avec état.

| Niveau de durabilité  | Nombre minimal de machines virtuelles exigées | Références SKU de machines virtuelles prises en charge                                                                  | Mises à jour que vous apportez à votre groupe de machines virtuelles identiques                               | Mises à jour et maintenance lancées par Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Or             | 5                              | Références (SKU) de nœuds complets dédiées à un seul client (par exemple, L32s, GS5, G5, DS15_v2, D15_v2) | Peuvent être différées jusqu’à ce qu’elles soient approuvées par le cluster Service Fabric | Peuvent être suspendues pendant 2 heures par domaine de mise à niveau (UD) pour accorder du temps supplémentaire pour la récupération de réplicas à partir d’échecs précédents |
| Argent           | 5                              | Machines virtuelles avec un seul cœur ou plus avec un disque SSD local d’au moins 50 Go                      | Peuvent être différées jusqu’à ce qu’elles soient approuvées par le cluster Service Fabric | Ne peuvent pas être différées pour quelque durée que ce soit                                                    |
| Bronze           | 1                              | Machines virtuelles avec un disque SSD local d’au moins 50 Go                                              | Ne sont pas différées par le cluster Service Fabric           | Ne peuvent pas être différées pour quelque durée que ce soit                                                    |

> [!WARNING]
> Les types de nœud s’exécutant avec un niveau de durabilité Bronze n’obtiennent _aucun privilège_. Cela signifie que les travaux d’infrastructure qui affectent vos charges de travail avec état ne seront ni arrêtés ni différés, ce qui risque d’avoir un impact sur vos charges de travail. Utilisez Bronze uniquement pour les types de nœuds qui exécutent seulement des charges de travail sans état. Pour les charges de travail de production, il est recommandé d’exécuter Silver ou un niveau supérieur. 
> 
> Quel que soit le niveau de durabilité, la [désallocation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) d’un groupe de machines virtuelles identiques a pour effet de détruire le cluster.

**Avantages de l’utilisation des niveaux de durabilité Silver ou Gold**
 
- Réduit le nombre d’étapes nécessaires dans une opération de diminution de la taille des instances (autrement dit, la désactivation du nœud et l’appel de la commande Remove-ServiceFabricNodeState sont effectués automatiquement).
- Réduit le risque de perte de données résultant d’une opération de changement de référence (SKU) de machine virtuelle initiée par l’utilisateur, ou d’opérations de l’infrastructure Azure.

**Inconvénients de l’utilisation des niveaux de durabilité Silver ou Gold**
 
- Des déploiements sur vos groupes de machines virtuelles identiques et d’autres ressources Azure associées peuvent être différés, dépasser le délai d’attente ou être entièrement bloqués par des problèmes au sein de votre cluster ou au niveau de l’infrastructure. 
- Augmente le nombre d’[événements du cycle de vie d’un réplica](service-fabric-reliable-services-lifecycle.md) (par exemple, les permutations principales) en raison de l’automatisation des désactivations de nœud durant les opérations de l’infrastructure Azure.
- Met des nœuds hors service pendant les mises à jour logicielles de la plateforme Azure ou la maintenance du matériel. Vous pouvez voir l’état En cours de désactivation/Désactivé des nœuds pendant ces activités. La capacité de votre cluster est temporairement réduite, mais cela ne devrait pas avoir d’impact sur sa disponibilité ou sur celle de vos applications.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Suggestions relatives au moment opportun pour utiliser les niveaux de durabilité Silver ou Gold

Utilisez les niveaux de durabilité Silver ou Gold pour tous les types de nœuds qui hébergent des services avec état dont vous prévoyez une diminution fréquente de la taille des instances (réduction du nombre d’instances de machine virtuelle), si vous préférez que les opérations de déploiement soient retardées et que la capacité soit réduite au profit d’une simplification de ces opérations de diminution de la taille des instances. Les scénarios d’augmentation de la taille des instances (ajout d’instances de machine virtuelle) n’ayant aucune incidence sur le choix du niveau de durabilité, seuls les scénarios de diminution de la taille des instances doivent être pris en considération.

### <a name="changing-durability-levels"></a>Modification des niveaux de durabilité
- Les types de nœuds avec des niveaux de durabilité Argent ou Or ne peuvent pas être rétrogradés à Bronze.
- La mise à niveau de Bronze vers Argent ou Or peut prendre quelques heures.
- Lorsque vous modifiez le niveau de durabilité, veillez à le mettre à jour à la fois dans la configuration d’extension Service Fabric de votre ressource de groupe de machines virtuelles identiques et dans la définition du type de nœud dans votre ressource de cluster Service Fabric. Ces valeurs doivent correspondre.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recommandations opérationnelles concernant le type de nœud que vous avez défini sur le niveau de durabilité Silver ou Gold.

- Veillez à la permanence de l’intégrité de votre cluster et de vos applications, et assurez-vous que les applications répondent à tous les [événements de cycle de vie de réplica de service](service-fabric-reliable-services-lifecycle.md) (par exemple, le blocage de la création d’un réplica) en temps opportun.
- Adoptez des méthodes plus sûres pour la modification des références (SKU) de machine virtuelle (augmentation ou réduction d’échelle) : La modification de la référence SKU des machines virtuelles d’un groupe de machines virtuelles identiques nécessite un certain nombre d’étapes et de considérations. Voici la procédure à suivre pour éviter les problèmes les plus courants.
    - **Pour les types de nœuds non principaux** : il est recommandé de créer un groupe de machines virtuelles identiques, de modifier la contrainte de positionnement de service pour inclure le nouveau groupe de machines virtuelles identiques/type de nœud, puis de réduire le nombre d’instances du groupe de machines virtuelles identiques à zéro, un nœud à la fois pour être certain que la suppression des nœuds n’affecte pas la fiabilité du cluster.
    - **Pour les types de nœuds principaux** : Si la référence SKU de machine virtuelle que vous avez sélectionnée a atteint sa capacité maximale et que vous souhaitez passer à une référence SKU de plus grande taille, suivez nos conseils de [mise à l’échelle verticale pour un type de nœud principal](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type). 

- Conservez au minimum cinq nœuds pour tout groupe de machines virtuelles identiques sur lequel le niveau de durabilité Gold ou Silver est activé.
- Chaque groupe de machines virtuelles identiques avec le niveau de durabilité Silver ou Gold doit être mappé à son propre type de nœud dans le cluster Service Fabric. Le mappage de plusieurs groupes de machines virtuelles identiques à un type de nœud unique empêche le fonctionnement correct de la coordination entre le cluster Service Fabric et l’infrastructure Azure.
- Ne supprimez pas d’instances de machine virtuelle aléatoires. Opérez toujours une descente en puissance du groupe de machines virtuelles identiques. La suppression d’instances de machine virtuelle aléatoires risque de créer des déséquilibres au sein de l’instance de machine virtuelle répartie sur UD et FD. Ce déséquilibre peut nuire à la capacité du système à équilibrer correctement la charge entre les instances de service/réplicas de service.
- Si vous utilisez la fonctionnalité Mise à l’échelle automatique, définissez les règles de façon à ce que la diminution de la taille des instances (suppression d’instances de machine virtuelle) soit effectuée nœud après nœud. La descente en puissance de plusieurs instances en même temps présente des risques.
- En cas de suppression ou de désallocation de machines virtuelles sur le type de nœud principal, vous ne devez jamais réduire le nombre de machines virtuelles d’allouées en-dessous de ce qu’exige le niveau de fiabilité. Ces opérations sont bloquées pour une durée indéterminée dans un groupe identique avec un niveau de durabilité Silver ou Gold.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Caractéristiques de fiabilité du cluster
Le niveau de fiabilité est utilisé pour définir le nombre de réplicas des services système que vous voulez exécuter dans ce cluster sur le type de nœud principal. Plus le nombre de réplicas est important, plus les services système sont fiables dans votre cluster.  

Le niveau de fiabilité peut avoir les valeurs suivantes :

* Platinum : Exécutez les services système avec un nombre de jeux de réplicas cible égal à neuf
* Gold : Exécutez les services système avec un nombre de jeux de réplicas cible égal à sept
* Silver : Exécutez les services système avec un nombre de jeux de réplicas cible égal à cinq 
* Bronze : Exécutez les services système avec un nombre de jeux de réplicas cible égal à trois

> [!NOTE]
> Le niveau de fiabilité que vous choisissez détermine le nombre minimal de nœuds que doit contenir votre type de nœud principal. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Recommandations pour le niveau de fiabilité

Lorsque vous augmentez ou réduisez la taille de votre cluster (la somme des instances de machine virtuelle dans tous les types de nœuds), vous devez mettre à jour la fiabilité de votre cluster d’un niveau à l’autre. Cette opération déclenche les mises à niveau de cluster nécessaires pour modifier le nombre de jeux de réplicas des services système. Attendez que la mise à niveau en cours se termine avant d’apporter d’autres modifications au cluster, comme l’ajout de nœuds.  Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou en exécutant [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Voici la recommandation sur le choix du niveau de fiabilité.  Le nombre de nœuds initiaux est également défini sur le nombre minimal de nœuds d’un niveau de fiabilité.  Par exemple, pour un cluster pourvu d’une fiabilité Gold, il existe 7 nœuds initiaux.

| **Nombre de nœuds de cluster** | **Niveau de fiabilité** |
| --- | --- |
| 1 |Ne spécifiez pas le paramètre de niveau de fiabilité. Le système le calcule. |
| 3 |Bronze |
| 5 ou 6|Argent |
| 7 ou 8 |Or |
| 9 et plus |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Type de nœud principal - Recommandations en matière de capacité

Voici nos recommandations pour planifier la capacité du type de nœud principal :

- **Nombre d’instances de machine virtuelle pour exécuter les charges de travail en production dans Azure :** Vous devez spécifier une taille minimale du type de nœud principal de 5 et un niveau de fiabilité Silver.  
- **Nombre d’instances de machine virtuelle pour exécuter des charges de travail de test dans Azure :** vous devez définir la taille minimale du type de nœud principal sur 1 ou 3. Le cluster à un nœud s’exécute avec une configuration spéciale. Par conséquent, l’augmentation de la taille des instances de ce cluster n’est pas prise en charge. Le cluster à un nœud n’est pas fiable. Par conséquent, dans votre modèle Resource Manager, vous devez supprimer / ne pas spécifier cette configuration (ne pas définir la valeur de configuration ne suffit pas). Si vous configurez le cluster à un nœud via le portail, la configuration est automatiquement prise en charge. Les clusters à un et trois nœuds ne sont pas pris en charge pour l’exécution des charges de travail de production. 
- **Référence de la machine virtuelle** : comme les services système s’exécutent sur le type de nœud principal, votre choix de référence de machine virtuelle pour celui-ci doit prendre en compte la charge maximale globale que vous prévoyez de placer dans le cluster. Voici une analogie pour illustrer mon propos ici : considérez le type de nœud principal comme vos « poumons », qui alimentent votre cerveau en oxygène ; si le cerveau ne reçoit pas assez d’oxygène, votre corps souffre. 

Les besoins en capacité d’un cluster sont déterminés par la charge de travail que vous prévoyez d’exécuter dans celui-ci. Par conséquent, nous ne pouvons pas fournir de recommandations qualitatives pour votre charge de travail spécifique. Néanmoins, vous trouverez ci-dessous des recommandations générales qui vous aideront à bien démarrer.

Pour les charges de travail de production : 

- Il est recommandé de dédier le type de nœud principal de vos clusters aux services système et d’utiliser des contraintes de placement pour déployer votre application sur des types de nœuds secondaires.
- La référence de machine virtuelle recommandée est Standard V2_V2 ou équivalent avec un minimum de 50 Go de mémoire sur disque SSD local.
- La référence de machine virtuelle minimale prise en charge est Standard_D2_V3 ou Standard D1_V2 ou équivalent avec un minimum de 50 Go de mémoire sur disque SSD local. 
- Notre recommandation minimale est de 50 Go. Pour vos charges de travail, en particulier lors de l’exécution de conteneurs Windows, des disques plus volumineux sont requis. 
- Les références de machine virtuelle à cœur partiel telles que Standard A0 ne sont pas prises en charge pour les charges de travail de production.
- Une série de références SKU de machines virtuelles n’est pas prise en charge pour les charges de production pour des raisons de performances.
- Les machines virtuelles de faible priorité ne sont pas prises en charge.

> [!WARNING]
> La modification de la taille d’une référence SKU de machine virtuelle de nœud principal sur un cluster en cours d’exécution, est une opération de mise à l’échelle qui est documentée dans [Augmenter la taille des instances d’un groupe de machines virtuelles identiques](virtual-machine-scale-set-scale-node-type-scale-out.md).

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Type de nœud non principal - Recommandations en matière de capacité pour les charges de travail avec état

Ces conseils sont donnés pour les charges de travail avec état utilisant des [collections fiables ou intervenant fiables](service-fabric-choose-framework.md) de structure de service qui sont en cours d’exécution dans le type de nœud principal.

**Nombre d’instances de machine virtuelle :** pour les charges de travail de production avec état, il est recommandé de les exécuter avec un nombre de réplicas minimal et cible de 5. Cela signifie qu’à l’état stationnaire, vous vous retrouvez avec un réplica (issu d’un jeu de réplicas) dans chaque domaine d’erreur et de mise à niveau. Le concept même de niveau de fiabilité pour le type de nœud principal permet de spécifier ce paramètre pour les services système. Par conséquent, il en va de même pour vos services avec état.

Pour les charges de travail de production, la taille minimale recommandée pour le type de nœud non principal est donc de 5 si vous exécutez des charges de travail avec état sur ce dernier.

**Référence de la machine virtuelle** : comme il s’agit du type de nœud sur lequel vos services d’application s’exécutent, votre choix de référence de machine virtuelle pour celui-ci doit prendre en compte la charge maximale que vous prévoyez de placer dans chaque nœud. Les besoins en capacité du type de nœud sont déterminés par la charge de travail que vous prévoyez d’exécuter dans le cluster. Par conséquent, nous ne pouvons pas fournir de recommandations qualitatives pour votre charge de travail spécifique. Néanmoins, vous trouverez ci-dessous des recommandations générales qui vous aideront à bien démarrer.

Pour les charges de travail de production 

- La référence de machine virtuelle recommandée est Standard V2_V2 ou équivalent avec un minimum de 50 Go de mémoire sur disque SSD local.
- La référence de machine virtuelle minimale prise en charge est Standard_D2_V3 ou Standard D1_V2 ou équivalent avec un minimum de 50 Go de mémoire sur disque SSD local. 
- Les références de machine virtuelle à cœur partiel telles que Standard A0 ne sont pas prises en charge pour les charges de travail de production.
- Une série de références SKU de machines virtuelles n’est pas prise en charge pour les charges de production pour des raisons de performances.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Type de nœud non principal - Recommandations en matière de capacité pour les charges de travail sans état

Ces conseils sont donnés pour les charges de travail sans état qui sont en cours d’exécution sur le type de nœud non principal.

**Nombre d’instances de machine virtuelle :** pour les charges de travail de production sans état, la taille minimale prise en charge pour le type de nœud non principal est égale à 2. Vous pouvez ainsi exécuter deux instances sans état de votre application, ce qui permet à votre service de continuer à fonctionner en cas de perte d’une instance de machine virtuelle. 

**Référence de la machine virtuelle** : comme il s’agit du type de nœud sur lequel vos services d’application s’exécutent, votre choix de référence de machine virtuelle pour celui-ci doit prendre en compte la charge maximale que vous prévoyez de placer dans chaque nœud. Les besoins en capacité du type de nœud sont déterminés par la charge de travail que vous projetez d’exécuter dans le cluster. Nous ne pouvons pas vous fournir de conseils qualitatifs pour votre charge de travail spécifique.  Toutefois, voici nos recommandations générales pour vous aider à bien démarrer.

Pour les charges de travail de production 

- La référence de machine virtuelle recommandée est Standard D2_V2 ou équivalent. 
- La référence de machine virtuelle minimale prise en charge est Standard D1 ou Standard D1_V2 ou équivalent. 
- Les références de machine virtuelle à cœur partiel telles que Standard A0 ne sont pas prises en charge pour les charges de travail de production.
- Une série de références SKU de machines virtuelles n’est pas prise en charge pour les charges de production pour des raisons de performances.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez terminé la planification de votre capacité et que vous avez configuré un cluster, lisez ce qui suit :

* [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
* [Mise à l’échelle d’un cluster Service Fabric](service-fabric-cluster-scaling.md)
* [Planification de la récupération d’urgence](service-fabric-disaster-recovery.md)
* [Relations des types de nœuds avec le groupe de machines virtuelles identiques](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
