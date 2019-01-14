---
title: Superviser l’intégrité des machines virtuelles avec Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Cet article décrit comment comprendre l’intégrité de la machine virtuelle et le système d’exploitation sous-jacent grâce à Azure Monitor pour les machines virtuelles.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 737e05f3d936481e06acfc0604ff739b9f01d5db
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191647"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms-preview"></a>Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles (préversion)
Azure comprend plusieurs services qui effectuent individuellement un rôle spécifique ou une tâche dans l’espace de supervision, mais en fournissant une perspective approfondie de l’intégrité du système d’exploitation hébergé sur les machines virtuelles Azure qui n’était pas disponible auparavant.  Bien que vous puissiez surveiller différentes conditions à l’aide de Log Analytics ou d’Azure Monitor, ils n’ont pas été conçus pour modéliser et représenter l’intégrité des composants de base ou l’intégrité globale de la machine virtuelle.  La fonctionnalité de contrôle d’intégrité Azure Monitor pour les machines virtuelles surveille proactivement la disponibilité et les performances du système d'exploitation invité Windows ou Linux avec un modèle qui représente les composants clés et leurs relations (critères spécifiant comment mesurer l’intégrité de ces composants) et vous avertit lorsqu’un problème d’intégrité est détecté.  

L’affichage de l’état d’intégrité global de la machine virtuelle Azure et du système d’exploitation sous-jacente peut être observé sous deux perspectives avec Azure Monitor pour l’intégrité des machines virtuelles, directement à partir de la machine virtuelle ou sur toutes les machines virtuelles dans un groupe de ressources à partir d’Azure Monitor.

Cet article vous aide à évaluer, examiner et résoudre rapidement les problèmes d’intégrité détectés.

Pour plus d’informations sur la configuration d’Azure Monitor pour les machines virtuelles, consultez [Enable Azure Monitor for VMs ](vminsights-onboard.md)(Activer Azure Monitor pour les machines virtuelles).

## <a name="monitoring-configuration-details"></a>Supervision des détails de configuration
Cette section décrit les critères d’intégrité par défaut définis pour surveiller les machines virtuelles Azure Windows et Linux. Tous les critères d’intégrité sont préconfigurés pour générer une alerte quand la condition de défectuosité est remplie. 

### <a name="windows-vms"></a>Machines virtuelles Windows

- Mégaoctets de mémoire disponibles 
- Moyenne disque en secondes par écriture (disque logique)
- Moyenne disque en secondes par écriture (disque)
- Moyenne disque logique en secondes par lecture
- Moyenne disque logique en secondes par transfert
- Moyenne disque en secondes par lecture
- Moyenne disque en secondes par transfert
- Longueur de la file d’attente du disque actuel (disque logique)
- Longueur de la file d’attente du disque actuel (disque)
- Temps d’inactivité du disque en pourcentage
- Erreur ou corruption du système de fichiers
- Espace libre du disque logique (%) faible
- Espace libre du disque logique (Mo) faible
- Temps d’inactivité du disque logique en pourcentage
- Pages mémoire par seconde
- Pourcentage de bande passante utilisée en lecture
- Pourcentage de bande passante utilisée au total
- Pourcentage de bande passante utilisée en écriture
- Pourcentage de mémoire allouée en cours d’utilisation
- Temps d’inactivité du disque physique en pourcentage
- Service Health du client DHCP
- Service Health du client DNS
- Service Health du RPC
- Service Health du serveur
- Pourcentage d’utilisation du processeur total
- Service Health du journal des événements Windows
- Service Health du pare-feu Windows
- Service Health Windows Remote Management

### <a name="linux-vms"></a>Machines virtuelles Linux
- Disque moy. Disk sec/Transfer 
- Disque moy. Disk sec/Read 
- Disque moy. Disk sec/Write 
- Intégrité du disque
- Espace libre du disque logique
- Espace libre du disque logique (%)
- Inodes libres du disque logique (%)
- Intégrité de la carte réseau
- Pourcentage du temps DPC du processeur
- Pourcentage du temps processeur du processeur
- Pourcentage du temps processeur total
- Pourcentage du temps DPC total
- Mégaoctets de mémoire du système d’exploitation disponibles

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [Portail Azure](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Présentation de l’expérience d’intégrité
Avant de passer au cas pratique d’utilisation de la fonctionnalité de contrôle d’intégrité pour une machine virtuelle unique ou un groupe de machines virtuelles, il nous paraît important de procéder à une brève présentation pour que vous compreniez comment les informations sont présentées et ce que représentent les visualisations.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Afficher l’intégrité directement à partir d’une machine virtuelle 
Pour afficher l’intégrité d’une machine virtuelle Azure, sélectionnez **Insights (préversion)** dans le volet gauche de la machine virtuelle. Sur la page d’informations de machine virtuelle, **Intégrité** est ouvert par défaut et affiche la vue d’intégrité de la machine virtuelle.  

![Vue d’ensemble de l’intégrité des machines virtuelles Azure Monitor pour une machine virtuelle Azure donnée](./media/vminsights-health/vminsights-directvm-health.png)

Dans l’onglet **Intégrité**, sous la section **Intégrité de la machine virtuelle invitée**, le tableau affiche l’état d’intégrité actuel de votre machine virtuelle et le nombre total d’alertes d’intégrité de la machine virtuelle déclenchées par un composant défectueux. Reportez-vous à [Alertes et gestion des alertes](#alerting-and-alert-management) pour plus d’informations.  

Les états d’intégrité définis pour une machine virtuelle sont les suivants : 

* **Sain** : aucun problème détecté pour la machine virtuelle et le fonctionnement répond aux attentes.  
* **Critique** : un ou plusieurs problèmes critiques ont été détectés et doivent être corrigés afin de restaurer le fonctionnement normal attendu. 
* **Avertissement** : un ou plusieurs problèmes ont été détectés et doivent être corrigés pour éviter que la condition d’intégrité ne devienne critique.  
* **Inconnu** : si le service n’a pas pu établir de connexion avec la machine virtuelle, l’état passe à Inconnu.  

La sélection du lien **Afficher les diagnostics d’intégrité** ouvre une page qui présente tous les composants de la machine virtuelle, les critères d’intégrité associés, les changements d’état et les autres problèmes importants rencontrés par les composants de supervision liés à la machine virtuelle. Pour plus d’informations, consultez la section [Diagnostics d’intégrité](#health-diagnostics). 

Dans la section **Intégrité des composants**, le tableau présente un état cumulatif de l’intégrité des principales catégories de performance surveillées par les critères d’intégrité pour ces zones, en particulier le **processeur**, la **mémoire**, le **disque** et le **réseau**.  Sélectionner l’un des composants ouvre une page répertoriant tous les aspects de supervision des critères d’intégrité individuels de ce composant, ainsi que l’état d’intégrité respectif de chacun d’eux.  

Lors de l’accès à l’intégrité à partir d’une machine virtuelle Azure exécutant le système d’exploitation Windows, l’état d’intégrité des 5 principaux services Windows est affiché sous la section **Intégrité des services principaux**.  Sélectionner l’un de ces services ouvre une page répertoriant les critères d’intégrité de supervision de ce composant ainsi que son état d’intégrité.  Cliquez sur le nom des critères d’intégrité pour ouvrir le volet des propriétés. À partir de là, vous pouvez consulter les détails de configuration, y compris si une alerte Azure Monitor est définie pour chaque critère d’intégrité. Pour plus d’informations, consultez les sections concernant [les diagnostics d’intégrité et l’utilisation des critères d’intégrité](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Agréger la perspective de la machine virtuelle
Pour afficher la collection d’intégrité pour l’ensemble de vos machines virtuelles dans un groupe de ressources, à partir de la liste de navigation dans le portail, sélectionnez **Azure Monitor**, puis sélectionnez **Machines virtuelles (préversion)**.  

![Affichage de supervision des insights de machine virtuelle à partir d’Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Dans les listes déroulantes **Abonnement** et **Groupe de ressources**, sélectionnez le groupe de ressources approprié qui comprend les machines virtuelles apparentées au groupe de façon à consulter leur état d’intégrité.  Votre sélection s’applique uniquement à la fonctionnalité Intégrité et n’est pas transférée à Performances ou Carte.

Dans l’onglet **Intégrité**, vous découvrez les informations suivantes :

* Combien de machines virtuelles se trouvent dans un état critique ou non intègre, et combien sont saines ou ne soumettent aucune donnée (état inconnu).
* Combien de machines virtuelles par système d’exploitation (OS) signalent un état non sain et lesquelles.
* Combien de machines virtuelles, classées par état d’intégrité, sont non saines en raison d’un problème détecté avec un processeur, un disque, une mémoire ou une carte réseau.  
* Combien de machines virtuelles, classées par état d’intégrité, sont non saines en raison d’un problème détecté avec un service du système d’exploitation principal.

Ici, vous pouvez rapidement identifier les principaux problèmes critiques détectés par les critères d’intégrité qui surveillent la machine virtuelle de façon proactive, et passer en revue les détails des alertes d’intégrité de machine virtuelle ainsi que l’article de la base de connaissances associé destiné à aider au diagnostic et à la correction du problème.  Sélectionnez l’un des niveaux de gravité pour ouvrir la page [Toutes les alertes](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrée sur ce niveau de gravité.

La liste **Distribution de machine virtuelle par système d’exploitation** affiche les machines virtuelles répertoriées par édition Windows ou distribution Linux, ainsi que leur version. Dans chaque catégorie de système d’exploitation, les machines virtuelles sont ventilées selon l’intégrité de la machine virtuelle. 

![Perspective de distribution de machine virtuelle VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Les états d’intégrité définis pour une machine virtuelle sont les suivants : 

* **Sain** : aucun problème détecté pour la machine virtuelle et le fonctionnement répond aux attentes.  
* **Critique** : un ou plusieurs problèmes critiques ont été détectés et doivent être corrigés afin de restaurer le fonctionnement normal attendu. 
* **Avertissement** : un ou plusieurs problèmes ont été détectés et doivent être corrigés pour éviter que la condition d’intégrité ne devienne critique.  
* **Inconnu** : si le service n’a pas pu établir de connexion avec la machine virtuelle, l’état passe à Inconnu.  

Vous pouvez cliquer sur n’importe quel élément de la colonne - **Nombre de machines virtuelles**, **Critique**, **Avertissement**, **Sain** ou **Inconnu** pour éclater la page des **Machines virtuelles** selon une liste de résultats filtrés correspondant à la colonne sélectionnée. Par exemple, si nous souhaitons examiner toutes les machines virtuelles exécutant **Red Hat Enterprise Linux version 7.5**, cliquez sur la valeur **Nombre de machines virtuelles** pour ce système d’exploitation et la page suivante s’ouvre. Elle répertorie les machines virtuelles correspondant à ce filtre et leur état d’intégrité actuellement connu.  

![Exemple de rollup des machines virtuelles Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Sur la page **Machines virtuelles**, si vous sélectionnez le nom d’une machine virtuelle dans la colonne **Nom de machine virtuelle**, vous êtes redirigé vers la page de l’instance de machine virtuelle avec plus de détails sur les alertes et les problèmes de critères d’intégrité identifiés qui affectent la machine virtuelle sélectionnée.  À partir de là, vous pouvez filtrer les détails de l’état d’intégrité en cliquant sur l’icône **État d’intégrité** dans le coin supérieur gauche de la page pour connaître les composants défectueux, ou vous pouvez visualiser les alertes d’intégrité de la machine virtuelle déclenchées par un composant défectueux et classées par gravité d’alerte.    

Dans la vue liste de la machine virtuelle, cliquez sur le nom d’une machine virtuelle pour ouvrir la page d’**intégrité** correspondant à cette machine virtuelle sélectionnée, de la même façon que si vous aviez sélectionné **Insights (préversion)** directement depuis la machine virtuelle.

![VM Insights d’une machine virtuelle Azure sélectionnée](./media/vminsights-health/vminsights-directvm-health.png)

Ici, il affiche un rollup de **l’état d’intégrité** pour la machine virtuelle et des **alertes**, classées par niveau de gravité, qui représentent des alertes d’intégrité de machine virtuelle déclenchées lorsque l’état d’un critère d’intégrité passe de sain à non sain.  Sélectionner **Machines virtuelles en condition critique** ouvre une page avec une liste d’une ou plusieurs machines virtuelles qui se trouvent dans un état critique.  Cliquez sur l’état d’intégrité pour l’une des machines virtuelles dans la liste pour afficher la vue **Diagnostics d’intégrité** de la machine virtuelle.  Vous trouverez ici les critères d’intégrité reflétant un problème d’état d’intégrité. Lorsque la page **Diagnostics d’intégrité** s’ouvre, elle affiche tous les composants de la machine virtuelle et leurs critères d’intégrité associés ainsi que l’état d’intégrité actuel.  Reportez-vous à la section [Diagnostics d’intégrité](#health-diagnostics) pour plus d’informations.  

Sélectionnez **Afficher tous les critères d’intégrité** pour ouvrir une page affichant une liste de tous les critères d’intégrité disponibles avec cette fonctionnalité.  Les informations peuvent être filtrées plus précisément selon les options suivantes :

* **Type** : il existe trois types de critères d’intégrité servant à évaluer les conditions et le regrouper les états d’intégrité généraux de la machine virtuelle surveillée.  
    a. **Unité** – mesure certains aspects de la machine virtuelle. Ce type de critère d’intégrité peut vérifier un compteur de performances pour déterminer les performances du composant, exécuter un script pour effectuer une transaction synthétique ou guetter un événement qui indique une erreur.  Par défaut, le filtre est défini sur unité.  
    b. **Dépendance** - fournit le rollup des intégrités entre les différentes entités. Ce critère d’intégrité permet à l’intégrité d’une entité de dépendre de l’intégrité d’un autre type d’entité sur laquelle elle s’appuie pour un fonctionnement réussi.  
    c. **Agrégation** - fournit un état d’intégrité combiné de critères d’intégrité similaires. Les critères d’intégrité Unité et Dépendance sont généralement configurés sous un critère d’intégrité agrégé. En plus de fournir une meilleure organisation générale des nombreux critères d’intégrité différents ciblant une entité, le critère d’intégrité agrégé fournit un état d’intégrité unique pour différentes catégories d’entités.

* **Catégorie** - Type de critère d’intégrité utilisé pour grouper des critères de type similaire à des fins de création de rapports.  Il s’agit de la **Disponibilité** ou des **Performances**.

Vous pouvez descendre encore plus dans la hiérarchie pour afficher les instances défectueuses en cliquant sur une valeur de la colonne **Composant défectueux**.  Sur la page, un tableau répertorie les composants qui présentent un état d’intégrité critique.    

## <a name="health-diagnostics"></a>Diagnostics d'intégrité
La page **Diagnostics d’intégrité** vous permet de voir tous les composants de la machine virtuelle, les critères d’intégrité associé, les changements d’état et autres problèmes importants rencontrés par la supervision des objets liés à la machine virtuelle. 

![Exemple de page de Diagnostics d’intégrité pour une machine virtuelle](./media/vminsights-health/health-diagnostics-page-01.png)

Vous pouvez lancer les diagnostics d’intégrité comme suit :

* Par rollup d’état d’intégrité pour toutes les machines virtuelles à partir de la perspective d’agrégation des machines virtuelles dans Azure Monitor.  Sur la page **intégrité**, cliquez sur l’icône des états d’intégrité **Critique**, **Avertissement**, **Sain**, ou **Inconnu** dans la section **Intégrité de la machine virtuelle invitée** et accédez à la page qui répertorie toutes les machines virtuelles correspondant à cette catégorie filtrée.  Cliquez sur la valeur dans la colonne **État d’intégrité** pour ouvrir les Diagnostics d’intégrité étendus à cette machine virtuelle spécifique.      

* Par système d’exploitation à partir de la perspective d’agrégation de la machine virtuelle dans Azure Monitor. Sous **Distribution de machine virtuelle**, sélectionnez l’une des valeurs de colonne pour ouvrir la page **Machines virtuelles** page et retourner une liste dans la table correspondant à la catégorie filtrée.  Cliquez sur la valeur sous la colonne **État d’intégrité** pour ouvrir les Diagnostics d’intégrité pour la machine virtuelle sélectionnée.    
 
* Depuis la machine virtuelle invitée, dans l’onglet **intégrité** Azure Monitor pour les machines virtuelles, en sélectionnant **Afficher les diagnostics d’intégrité** 

Les diagnostics d’intégrité classent les informations d’intégrité dans les catégories suivantes : 

* Disponibilité
* Performances
 
Tous les critères d’intégrité définis pour une cible sélectionnée s’affichent dans la catégorie appropriée. 

L’état d’intégrité relatif aux critères d’intégrité peut prendre l’une des trois valeurs suivantes : *Critique*, *Avertissement* ou *Sain*. Il existe un autre état, *Inconnu*, qui n’est pas associé à l’état d’intégrité, mais correspond à son état de supervision connu par la fonctionnalité.  

Le tableau suivant fournit des détails sur les États d’intégrité représentés dans les diagnostics d’intégrité.

|Icône |État d’intégrité |Signification |
|-----|-------------|------------|
| |Healthy |L’état d’intégrité est sain s’il correspond aux conditions d’intégrité définies. Dans le cas d’un moniteur Rollup parent, l’intégrité est regroupée et reflète le meilleur ou pire état de l’enfant.|
| |Critique |L’état d’intégrité est critique s’il ne correspond pas aux conditions d’intégrité définies. Dans le cas d’un moniteur Rollup parent, l’intégrité est regroupée et reflète le meilleur ou pire état de l’enfant.|
| |Avertissement |L’état d’intégrité présente un avertissement s’il se situe entre un seuil d’état d’*avertissement* et un seuil d’état *critique* pour une condition d’intégrité définie. Dans le cas d’un moniteur Rollup parent, si un ou plusieurs des enfants sont dans un état d’avertissement, le parent reflète l’état d’*avertissement*. Si un enfant se trouve dans un état *Critique* et un autre enfant dans un état d’*Avertissement*, le rollup parent affiche un état d’intégrité *Critique*.|
| |Unknown |L’état d’intégrité est *Inconnu* lorsqu’il ne peut pas être calculé pour plusieurs raisons, par exemple l’impossibilité de collecter des données, service non initialisé, etc.| 

La page de diagnostics d’intégrité comporte trois sections principales :

* Modèle de composant 
* Critères d’intégrité
* Changements d'état 

![Sections d’une page de Diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modèle de composant
La colonne la plus à gauche dans la page de diagnostics d’Intégrité est le modèle de composant. Tous les composants ainsi que ses instances détectées associées à la machine virtuelle sont affichés dans cette colonne. 

Dans l’exemple suivant, les composants détectés sont le disque, le disque logique, le processeur, la mémoire et le système d’exploitation. Plusieurs instances de ces composants sont découvertes et affichées dans cette colonne, avec deux instances de disque logique **/**, **/boot** et **/mnt/resource**, une instance de la carte réseau **eth0**, deux instances de disque **sda** et **sdb**, deux instances de processeur **0 et 1**, ainsi qu’un **Serveur Red Hat Enterprise Linux version 7.4 (Maipo) (système d’exploitation)**. 

![Exemple de modèle de composant présenté dans les diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critères d’intégrité
La colonne centrale de la page de diagnostics d’intégrité est la colonne **Critères d’intégrité**. Le modèle d’intégrité défini pour la machine virtuelle s’affiche dans une arborescence hiérarchique. Il se compose des critères d’intégrité Unité, Dépendance et Agrégat.  

![Exemples de critères d’intégrité présentés dans les diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Un critère d’intégrité mesure l’intégrité de l’instance surveillée selon certains critères, par exemple une valeur de seuil ou l’état d’une entité, etc. Un critère d’intégrité comporte deux ou trois états d’intégrité, comme décrit dans la section ci-dessus. À un moment donné, le critère d’intégrité ne peut être que dans l’un de ses états potentiels. 

L’intégrité globale d’une cible est déterminée à partir de l’intégrité de chacune de ses critères d’intégrité définis dans le modèle d’intégrité. Il s’agit d’une combinaison des critères d’intégrité concernant directement la cible, les critères d’intégrité concernant les composants se regroupant au niveau de la cible via un critère de d’intégrité de dépendance. Cette hiérarchie est illustrée dans la section **Critères d’intégrité** de la page des Diagnostics d’intégrité. La stratégie de Rollup d’intégrité fait partie intégrante de la configuration des critères d’intégrité d’agrégat et de dépendance. Vous trouverez une liste de l’ensemble par défaut des critères d’intégrité exécutés dans le cadre de cette fonctionnalité dans la section [Surveillance des détails de configuration](#monitoring-configuration-details).  

Dans l’exemple suivant, le critère d’intégrité d’agrégat **Rollup des services Windows principaux** pour une machine virtuelle basée sur Windows, évalue l’intégrité des services Windows les plus importants en fonction des critères d’intégrité de service individuels. L’état de chaque service (DNS, DHCP, etc.) est évalué et l’intégrité est regroupée au niveau du critère d’intégrité du rollup correspondant (comme indiqué ci-dessous).  

![Exemple de rollup d’intégrité](./media/vminsights-health/health-diagnostics-windows-svc-rollup.png)

L’intégrité du **rollup des Services Windows principaux** est injectée dans l’intégrité de la **disponibilité du système d’exploitation**, qui est regroupée dans la **disponibilité** de la machine virtuelle. 

La configuration du type de critère d’intégrité **Unité** peut être modifiée en cliquant sur le lien points de suspension à l’extrême droite et en sélectionnant **Afficher les détails** pour ouvrir le volet de configuration. 

![Exemple de configuration de critères d’intégrité](./media/vminsights-health/health-diagnostics-linuxvm-example-03.png)

Dans le panneau de configuration pour les critères d’intégrité sélectionnés, dans cet exemple le **Pourcentage d’espace libre du disque logique** peut être configuré avec une valeur numérique différente pour son seuil, car il s’agit d’un moniteur à deux états, ce qui signifie qu’il peut prendre uniquement les valeurs Sain et Critique.  D’autres critères d’intégrité peuvent comprendre trois états, pour lesquels vous pouvez configurer une valeur pour le seuil d’état d’intégrité Avertissement et une pour le seuil Critique.  

>[!NOTE]
>L’application des modifications de configuration des critères d’intégrité à une instance se transmet à toutes les instances surveillées.  Par exemple, si vous sélectionnez **/mnt/resource** et modifiez le seuil **Pourcentage d’espace libre disque logique**, cela s’applique non seulement à cette instance, mais aussi à toutes les autres instances de disques logiques découvertes et surveillées sur la machine virtuelle.
>

![Exemple de configuration d’un critère d’intégrité d’un moniteur d'unités](./media/vminsights-health/health-diagnostics-linuxvm-example-04.png)

Pour plus d’informations sur l’indicateur d’intégrité, vous pouvez consulter les articles de la base de connaissances qui sont inclus pour vous aider à identifier les problèmes, les causes et les mesures de résolution.  Il vous suffit de cliquer sur le lien **Afficher les informations** figurant sur la page pour que votre navigateur affiche un nouvel onglet vous présentant l’article de la base de connaissances correspondant.  Vous pouvez à tout moment consulter l’ensemble des articles de la base de connaissances relatifs aux critères d’intégrité qui sont disponibles pour la fonctionnalité de contrôle d’intégrité d’Azure Monitor pour les machines virtuelles en cliquant [ici](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Changements d'état
La colonne la plus à droite dans la page de diagnostics d’intégrité est la colonne **Changements d’état**. Elle répertorie toutes les modifications d’état associées aux critères d’intégrité sélectionnés dans la section **Critères d’intégrité**, ou les modifications d’état de la machine virtuelle si une machine virtuelle était sélectionnée dans le **Modèle de composant** ou la colonne **Critères d’intégrité** de la table. 

![Exemples de changements d’état présentés dans les diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Cette section comprend l’état des critères d’intégrité et l’heure associée triés selon l’état le plus récent en premier.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Association des colonnes Modèle de composant, Critères d’intégrité et Changements d’état 
Les trois colonnes sont liées entre elles. Lorsqu’un utilisateur sélectionne une instance détectée dans le Modèle de composant, la section **Critères d’intégrité** est filtrée pour afficher les informations relatives à ce composant et en conséquence le **Changement d’état** est mis à jour en fonction des critères d’intégrité sélectionnés. 

![Exemple de sélection d’instance et de résultats surveillés](./media/vminsights-health/health-diagnostics-linuxvm-example-02.png)

Dans l’exemple ci-dessus, en sélectionnant **/mnt (Logical Disk)**, le filtre de l’arborescence des critères d’intégrité prend la valeur **/mnt (Logical Disk)**, ainsi que les onglets **Disponibilité** et **Performances**. La colonne de **Changement d’état** montre les changements d’état en fonction de la disponibilité de **/mnt (Logical Disk)**. 

Pour afficher l’état d’intégrité mis à jour, vous pouvez actualiser la page de diagnostics d’intégrité en cliquant sur le lien **Actualiser**.  S’il existe une mise à jour de l’état d’intégrité des critères d’intégrité en fonction de l’intervalle d’interrogation prédéfini, cette tâche vous permet d’éviter l’attente en reflétant l’état d’intégrité le plus récent.  L’**état des critères d’intégrité** est un filtre qui vous permet de limiter les résultats en fonction de l’état d’intégrité sélectionné - Sain, Avertissement, Critique, Inconnu et Tous.  L’heure de **Dernière mise à jour** dans le coin en haut à droite représente la dernière fois que la page de diagnostics d’intégrité a été actualisée.  

## <a name="alerting-and-alert-management"></a>Alertes et gestion des alertes 
La fonctionnalité Azure Monitor pour l’intégrité des machines virtuelles s’intègre aux [Alertes Azure](../../azure-monitor/platform/alerts-overview.md) et déclenche une alerte lorsque les critères d’intégrité prédéfinis passent d’un état sain à un état non intègre lorsque la condition est détectée. Les alertes sont classées par niveau de gravité : de 0 à 4, la gravité 0 représentant le niveau de gravité le plus élevé.  

Le nombre total d’alertes d’intégrité de machine virtuelle classées par niveau de gravité est disponible au niveau du tableau de bord **Intégrité** dans la section **Alertes**. Lorsque vous sélectionnez le nombre total d’alertes ou le numéro correspondant à un niveau de gravité, la page **Alertes** s’ouvre et affiche toutes les alertes correspondant à votre sélection.  Par exemple, si vous avez sélectionné la ligne correspondant au **Niveau de gravité 1**, vous voyez l’écran suivant :

![Exemple de toutes les alertes de niveau de gravité 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Les alertes affichées dans la page **Alertes** ne se limitent pas à celles correspondant à votre sélection : elles sont aussi filtrées en fonction du **type de ressource**. Ainsi, seules sont affichées les alertes d’intégrité déclenchées par les ressources Machines virtuelles.  Cela apparaît dans la liste des alertes, sous la colonne **Ressource cible**, qui indique que l’alerte de machine virtuelle Azure a été déclenchée quand la condition de défectuosité des critères d’intégrité a été remplie.  

Les alertes relatives aux autres types de ressources ou services ne sont pas censées être incluse dans cette vue. Tel est le cas des alertes de journal basées sur des requêtes Log Analytics ou des alertes de métrique qui figurent normalement dans la page par défaut [Toutes les alertes](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) d’Azure Monitor. 

Vous pouvez filtrer cet affichage en sélectionnant des valeurs dans les menus déroulants en haut de la page.

|Colonne |Description | 
|-------|------------| 
|Abonnement |Sélectionnez un abonnement Azure. Seules les alertes dans l’abonnement sélectionné sont incluses dans la vue. | 
|Groupe de ressources |Sélectionnez un seul groupe de ressources. Seules les alertes avec des cibles dans le groupe de ressources sélectionné sont incluses dans la vue. | 
|Type de ressource |Sélectionnez un ou plusieurs types de ressources. Par défaut, seules les alertes des **machines virtuelles** cibles sont sélectionnées et incluses dans cette vue. Cette colonne n’est disponible qu’après qu’un groupe de ressources a été spécifié. | 
|Ressource |Sélectionnez une ressource. Seules les alertes ayant ces ressources pour cible sont incluses dans l’affichage. Cette colonne n’est disponible qu’après qu’un type de ressource a été spécifié. | 
|Severity |Sélectionnez un niveau de gravité d’alerte, ou sélectionnez *Tous* pour inclure les alertes quel que soit le niveau de gravité. | 
|Condition d’analyse |Sélectionnez une condition de surveillance pour filtrer les alertes selon qu’elles soient *déclenchées* par le système ou *résolues* par le système (si la condition n’est plus active). Ou bien sélectionnez *Tous* pour inclure les alertes de toutes les conditions. | 
|État d’alerte |Sélectionnez un état d’alerte *Nouveau*, *Reconnu*, *Fermé*, ou sélectionnez *Tous* pour inclure les alertes de tous les états. | 
|Service de surveillance |Sélectionnez un service ou *Tous* pour inclure tous les services. Seules les alertes de *VM Insights* sont prises en charge pour cette fonctionnalité.| 
|Période| Seules les alertes déclenchées dans la fenêtre de temps sélectionnée seront incluses dans l’affichage. Les valeurs prises en charge sont : dernière heure, dernières 24 heures, 7 derniers jours et 30 derniers jours. | 

La page **Détails de l’alerte** s’affiche lorsque vous sélectionnez une alerte, vous fournissant des détails sur l’alerte et vous permettant de modifier son état. Pour plus d’informations sur la gestion des alertes, consultez [Créer, afficher et gérer des alertes à l’aide d’Azure Monitor](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>Pour l’heure, il n’est pas possible de créer des alertes basées sur des critères d’intégrité ni de modifier les règles d’alertes d’intégrité existantes dans Azure Monitor à partir du portail.  
>

![Volet des détails de l’alerte pour une alerte sélectionnée](./media/vminsights-health/alert-details-pane-01.png)

Vous pouvez également modifier l’état de l’alerte pour une ou plusieurs alertes en les sélectionnant puis en sélectionnant **Modifier l’état** depuis la page **Toutes les alertes**, dans le coin supérieur gauche. Dans le volet **Modifier l’état de l’alerte**, sélectionnez l’un des états, ajoutez une description de la modification dans le champ **Commentaire**, puis cliquez sur **Ok** pour valider vos modifications. Pendant que les informations sont vérifiées et les modifications appliquées, vous pouvez suivre la progression sous **Notifications** dans le menu.  

## <a name="next-steps"></a>Étapes suivantes
Pour identifier les goulots d’étranglement et les performances d’utilisation globale avec vos machines virtuelles, consultez l’article indiquant comment [afficher les performances avec Azure Monitor pour les machines virtuelles](vminsights-performance.md). Pour visualiser les dépendances d’application découvertes, consultez l’article expliquant comment [afficher la fonctionnalité Map d’Azure Monitor pour les machines virtuelles](vminsights-maps.md). 
