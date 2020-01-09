---
title: Comprendre l’intégrité de vos machines virtuelles Azure | Microsoft Docs
description: Cet article décrit comment comprendre l’intégrité des machines virtuelles et des systèmes d’exploitation sous-jacents grâce à Azure Monitor pour machines virtuelles.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: e01223783130ea6b276db26bab709e2b51a8f76d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399787"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Comprendre l’intégrité de vos machines virtuelles Azure

Azure inclut des services pour des rôles ou tâches spécifiques dans l’espace de surveillance, mais n’offre pas de perspectives d’intégrité approfondies des systèmes d’exploitation hébergés sur des machines virtuelles Azure. Bien que vous puissiez utiliser Azure Monitor pour différentes conditions, il n’est pas conçu pour modéliser et représenter l’intégrité des principaux composants, ni l’intégrité globale des machines virtuelles.

Grâce à l’intégrité d’Azure Monitor pour machines virtuelles, vous pouvez surveiller activement la disponibilité et les performances d’un système d’exploitation invité Windows ou Linux. La fonctionnalité d’intégrité utilise un modèle qui représente les principaux composants et leurs relations, fournit des critères qui spécifient comment mesurer l’intégrité d’un composant et envoie une alerte lorsqu’il détecte une condition non saine.

L’affichage de l’état d’intégrité global d’une machine virtuelle Azure et du système d’exploitation sous-jacent peut être observé sous deux perspectives : directement à partir d’une machine virtuelle ou sur toutes les machines virtuelles dans un groupe de ressources à partir d’Azure Monitor.

Cet article explique comment évaluer, examiner et résoudre rapidement des problèmes d’intégrité lorsqu’ils sont détectés par la fonctionnalité d’intégrité d’Azure Monitor pour machines virtuelles.

Pour plus d’informations sur la configuration d’Azure Monitor pour les machines virtuelles, consultez [Enable Azure Monitor for VMs ](vminsights-enable-overview.md)(Activer Azure Monitor pour les machines virtuelles).

>[!NOTE]
>Nous avons récemment fait l’[annonce des changements](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que nous apportons à la fonctionnalité Intégrité suite aux commentaires que nous avons reçus de la part de nos clients ayant utilisé la préversion publique. En raison des multiples changements prévus, nous avons choisi de ne plus fournir la fonctionnalité Intégrité aux nouveaux clients. Les clients existants pourront continuer à utiliser la fonctionnalité Intégrité. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur la disponibilité générale](vminsights-ga-release-faq.md). 

## <a name="monitoring-configuration-details"></a>Supervision des détails de configuration

Cette section décrit les critères d’intégrité par défaut pour surveiller les machines virtuelles Azure Windows et Linux. Tous les critères d’intégrité sont préconfigurés pour envoyer une alerte lorsqu’une condition non saine est détectée.

| Nom du moniteur | Fréquence (min) | Durée de recherche en arrière (min) | Opérateur | Seuil | Alerte pour l’état | severity | Catégorie de charge de travail | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Disque logique en ligne | 5 | 15 | <> | 1 (true) | Critique | Gra1 | Linux | 
| Espace libre du disque logique | 5 | 15 | < | 200 Mo (avertissement)<br> 100 Mo (critique) | Avertissement | Gra1<br> Gra2 | Linux | 
| Inodes libres du disque logique (%) | 5 | 15 | < | 5 % | Critique | Gra1 | Linux | 
| Espace libre du disque logique (%) | 5 | 15 | < | 5 % | Critique | Gra1 | Linux | 
| État de la carte réseau | 5 | 15 | <> | 1 (true) | Avertissement | Gra2 | Linux | 
| Mémoire disponible en mégaoctets du système d’exploitation | 5 | 10 | < | 2,5 Mo | Critique | Gra1 | Linux | 
| Disque moy. Disk sec/Read | 5 | 25 | > | 0,05 s | Critique | Gra1 | Linux | 
| Disque moy. Disk sec/Transfer | 5 | 25 | > | 0,05 s | Critique | Gra1 | Linux | 
| Disque moy. Disk sec/Write | 5 | 25 | > | 0,05 s | Critique | Gra1 | Linux | 
| État du disque | 5 | 25 | <> | 1 (true) | Critique | Gra1 | Linux | 
| Pourcentage du temps processeur total du système d’exploitation | 5 | 10 | >= | 95 % | Critique | Gra1 | Linux | 
| Pourcentage d’utilisation du processeur total | 5 | 10 | >= | 95 % | Critique | Gra1 | Windows | 
| Erreur ou corruption du système de fichiers | 60 | 60 | <> | 4 | Critique | Gra1 | Windows | 
| Moyenne disque logique en secondes par lecture | 1 | 15 | > | 0,04 s | Avertissement | Gra2 | Windows | 
| Moyenne disque logique en secondes par transfert | 1 | 15 | > | 0,04 s | Avertissement | Gra2 | Windows | 
| Moyenne disque logique en secondes par écriture (disque logique) | 1 | 15 | > | 0,04 s | Avertissement | Gra2 | Windows | 
| Longueur de la file d’attente du disque actuel (disque logique) | 5 | 60 | >= | 32 | Avertissement | Gra2 | Windows | 
| Espace libre du disque logique (Mo) | 15 | 60 | > | 500 Mo avertissement<br> 300 Mo critique | Critique | Gra1<br> Gra2 | Windows | 
| Espace libre du disque logique (%) | 15 | 60 | > | 10 % avertissement<br> 5 % critique | Critique | Gra1<br> Gra2 | Windows |
| Temps d’inactivité du disque logique en pourcentage | 15 | 360 | <= | 20% | Avertissement | Gra2 | Windows | 
| Pourcentage de bande passante utilisée en lecture | 5 | 60 | >= | 60% | Avertissement | Gra2 | Windows | 
| Pourcentage de bande passante utilisée au total | 5 | 60 | >= | 75 % | Avertissement | Gra2 | Windows | 
| Pourcentage de bande passante utilisée en écriture | 5 | 60 | >= | 60% | Avertissement | Gra2 | Windows | 
| Service Health du client DHCP | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Service Health du client DNS | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Service Health du journal des événements Windows | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Service Health du pare-feu Windows | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Service Health du RPC | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Service Health du serveur | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Service Health Windows Remote Management | 5 | 12 | <> | 4 (exécution en cours) | Critique | Gra1 | Windows | 
| Mégaoctets de mémoire disponibles | 5 | 10 | < | 100 Mo | Critique | Gra1 | Windows | 
| Entrées libres de la table de pagination système | 5 | 10 | <= | 5 000 | Critique | Gra1 | Windows | 
| Pages mémoire par seconde | 5 | 10 | >= | 5000/s | Avertissement | Gra1 | Windows | 
| Pourcentage de mémoire allouée en cours d’utilisation | 5 | 10 | > | 80 % | Critique | Gra1 | Windows | 
| Moyenne disque en secondes par transfert | 1 | 15 | > | 0,04 s | Avertissement | Gra2 | Windows | 
| Moyenne disque en secondes par écriture | 1 | 15 | > | 0,04 s | Avertissement | Gra2 | Windows | 
| Longueur actuelle de la file d'attente du disque | 5 | 60 | >= | 32 | Avertissement | Gra2 | Windows | 
| Temps d’inactivité du disque en pourcentage | 5 | 60 | >= | 20% | Avertissement | Gra2 | Windows | 

>[!NOTE]
>La durée de recherche en arrière représente la fréquence à laquelle la fenêtre de recherche en arrière vérifie les valeurs des métriques, par exemple au cours des cinq dernières minutes.  

>[!NOTE]
>La fréquence représente la fréquence à laquelle l’alerte de métrique vérifie si les conditions sont remplies, par exemple à chaque minute.  Il s’agit de la fréquence à laquelle le critère d’intégrité est exécuté et la recherche en arrière correspond à la durée pendant laquelle le critère d’intégrité est évalué. Par exemple, le critère d’intégrité évalue si la condition **Utilisation de l’UC** est supérieure à 95 % avec une fréquence de 5 minutes et reste supérieure à 95 % pendant 15 minutes (3 cycles d’évaluation consécutifs). Alors, l’état est mis à jour et prend la valeur Gravité critique s’il ne l’avait pas déjà.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Pour vous connecter, accédez au [portail Azure](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Présentation de l’intégrité d’Azure Monitor pour machines virtuelles

Avant d’utiliser la fonctionnalité d’intégrité pour une machine virtuelle unique ou un groupe de machines virtuelles, il est important de comprendre comment les informations sont présentées et ce que représentent les visualisations.

### <a name="view-health-directly-from-a-vm"></a>Afficher l’intégrité directement à partir d’une machine virtuelle

Pour afficher l’intégrité d’une machine virtuelle Azure, sélectionnez **Insights (preview)** (Informations (préversion)) dans le volet de gauche de la machine virtuelle. Sur la page d’informations de machine virtuelle, l’onglet **Intégrité** est ouvert par défaut et affiche la vue d’intégrité de la machine virtuelle.

![Vue d’ensemble de l’intégrité des machines virtuelles Azure Monitor pour une machine virtuelle Azure donnée](./media/vminsights-health/vminsights-directvm-health-01.png)

Dans la section **Guest VM health (Intégrité de la machine virtuelle invitée)** , le tableau présente le cumul d’intégrité des performances des composants surveillés par les critères d’intégrité pour la machine virtuelle, et le nombre total d’alertes d’intégrité de la machine virtuelle déclenchées par des composants défectueux. Ces composants incluent **l’UC**, la **mémoire**, le **disque** et le **réseau**. Développez le chevron à côté de Guest VM health (Intégrité de la machine virtuelle invitée) pour afficher l’intégrité de ses composants.

![État de l’intégrité des composants des machines virtuelles Azure Monitor pour une machine virtuelle Azure donnée](./media/vminsights-health/vminsights-directvm-health-02.png)

La sélection de l’état à côté du composant permet d’ouvrir l’interface des diagnostics d’intégrité dans le contexte du composant sélectionné. Elle affiche la composition de l’état de ce composant et indique les critères d’intégrité utilisés pour calculer son intégrité. Pour plus d’informations, consultez [Diagnostics d’intégrité et utilisation des critères d’intégrité](#health-diagnostics). Pour plus d’informations sur les alertes, consultez la section [Alertes](#alerts).

Les états d’intégrité définis pour une machine virtuelle sont décrits dans le tableau suivant :

|Icône |État d’intégrité |Signification |
|-----|-------------|---------------|
| |Healthy |La machine virtuelle remplit les conditions d’intégrité définies. Cet état indique qu’aucun problème n’est détecté et que la machine virtuelle fonctionne normalement. Avec un moniteur Rollup parent, l’intégrité est regroupée et reflète le meilleur ou pire état de l’enfant.|
| |Critique |L’état ne remplit pas la condition d’intégrité définie, indiquant qu’un ou plusieurs problèmes critiques ont été détectés. Ces problèmes doivent être traités pour revenir à un fonctionnement normal. Avec un moniteur Rollup parent, l’état d’intégrité est regroupé et reflète le meilleur ou pire état de l’enfant.|
| |Avertissement |L’état est compris entre deux seuils pour la condition d’intégrité définie, où l’un indique un état d’avertissement et l’autre un état critique (trois seuils d’état d’intégrité peuvent être configurés), ou lorsqu’un problème non critique peut occasionner des problèmes critiques s’il n’est pas résolu. Avec un moniteur Rollup parent, si un ou plusieurs enfants sont dans un état d’avertissement, le parent reflète un état d’avertissement. Si un enfant se trouve dans un état critique et un autre enfant dans un état d’avertissement, le rollup parent affiche un état d’intégrité critique.|
| |Unknown |L’état ne peut pas être calculé pour plusieurs raisons. La section suivante fournit des détails supplémentaires et les solutions possibles. |

Un état d’intégrité inconnu peut être dû aux problèmes suivants :

- L’agent a été reconfiguré et ne communique plus avec l’espace de travail spécifié lors de l’activation d’Azure Monitor pour machines virtuelles. Pour configurer l’agent pour qu’il communique avec l’espace de travail, consultez [Ajout ou suppression d’un espace de travail](../platform/agent-manage.md#adding-or-removing-a-workspace).
- La machine virtuelle a été supprimée.
- L’espace de travail associé à Azure Monitor pour machines virtuelles a été supprimé. Vous pouvez récupérer l’espace de travail si vous disposez des avantages de support Premier. Accédez à [Premier](https://premier.microsoft.com/) et ouvrez une demande de support.
- Les dépendances de solution ont été supprimées. Pour réactiver les solutions ServiceMap et InfrastructureInsights dans votre espace de travail Log Analytics, réinstallez la solution ServiceMap à l’aide du [modèle Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution). Pour réinstaller la solution InfastructureInsights, faites-en la demande par e-mail à vminsights@microsoft.com. 
- La machine virtuelle a été arrêtée.
- Le service de machine virtuelle Azure n’est pas disponible, ou une maintenance est en cours d’exécution.
- La [limite de données quotidienne ou de rétention](../platform/manage-cost-storage.md) de l’espace de travail est atteinte.

Sélectionnez **Afficher les diagnostics d’intégrité** pour ouvrir une page qui présente tous les composants d’une machine virtuelle, les critères d’intégrité associés, les changements d’état et les autres problèmes détectés par les composants de surveillance liés à la machine virtuelle.

Pour plus d’informations, consultez la section [Diagnostics d’intégrité](#health-diagnostics).

Dans la section **Intégrité**, un tableau présente le cumul d’intégrité de performances des composants surveillés par les critères d’intégrité. Ces composants incluent **l’UC**, la **mémoire**, le **disque** et le **réseau**. Sélectionnez un composant pour ouvrir une page répertoriant l’ensemble des critères de surveillance et l’état d’intégrité de ce composant.

Lorsque vous accédez à l’intégrité à partir d’une machine virtuelle Azure exécutée sous Windows, l’état d’intégrité des cinq premiers principaux services Windows est affiché sous **Core services health** (Intégrité des principaux services). Sélectionnez un des services pour ouvrir une page répertoriant les critères d’intégrité de surveillance de ce composant ainsi que son état d’intégrité.

Sélectionnez le nom des critères d’intégrité pour ouvrir le volet des propriétés. Dans ce volet, vous pouvez consulter les détails de configuration, notamment si les critères d’intégrité sont associés à une alerte Azure Monitor.

Pour plus d’informations, consultez [Diagnostics d’intégrité et utilisation des critères d’intégrité](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Agréger la perspective de machine virtuelle

Pour afficher la collection d’intégrité pour l’ensemble de vos machines virtuelles dans un groupe de ressources, sélectionnez **Azure Monitor** dans la liste de navigation dans le portail, puis sélectionnez **Virtual Machines (preview)** (Machines virtuelles (préversion)).

![Affichage de supervision des insights de machine virtuelle à partir d’Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Dans les listes déroulantes **Abonnement** et **Groupe de ressources**, sélectionnez le groupe de ressources approprié qui comprend les machines virtuelles apparentées au groupe pour consulter leur état d’intégrité. Votre sélection s’applique uniquement à la fonctionnalité d’intégrité et n’est pas reportée sur les onglets **Performances** ou **Carte**.

L’onglet **Intégrité** fournit les informations suivantes :

* Combien de machines virtuelles se trouvent dans un état critique ou non sain, et combien sont saines ou ne soumettent aucune donnée (indiquées par un état inconnu).
* Combien de machines virtuelles par système d’exploitation signalent un état non sain et lesquelles.
* Combien de machines virtuelles, classées par état d’intégrité, sont non saines en raison d’un problème détecté avec un processeur, un disque, une mémoire ou une carte réseau.
* Combien de machines virtuelles, classées par état d’intégrité, sont non saines en raison d’un problème détecté avec un service du système d’exploitation principal.

Dans l’onglet **Intégrité**, vous pouvez identifier les problèmes critiques détectés par les critères d’intégrité de surveillance de la machine virtuelle, et passer en revue les détails de l’alerte et les articles de base de connaissances associés. Ces articles peuvent vous aider dans le diagnostic et la résolution des problèmes. Sélectionnez l’un des niveaux de gravité pour ouvrir la page [Toutes les alertes](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrée sur ce niveau de gravité.

La liste **Distribution de machine virtuelle par système d’exploitation** affiche les machines virtuelles répertoriées par édition Windows ou distribution Linux, ainsi que leur version. Dans chaque catégorie de système d’exploitation, les machines virtuelles sont ventilées selon l’intégrité de la machine virtuelle.

![Perspective de distribution de machine virtuelle VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Sélectionnez une colonne contenant **Nombre d’ordinateurs virtuels**, **Critique**, **Avertissement**, **Sain** ou **Inconnu**. Affichez la liste des résultats filtrés dans la page **Machines virtuelles** qui correspondent à la colonne sélectionnée.

Par exemple, pour passer en revue toutes les machines virtuelles qui exécutent Red Hat Enterprise Linux version 7.5, sélectionnez la valeur **Nombre d’ordinateurs virtuels** de ce système d’exploitation et les machines virtuelles correspondant à ce filtre ainsi leur état d’intégrité actuel sont répertoriées.

![Exemple de rollup des machines virtuelles Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Lorsque vous cochez la case **Afficher l’intégrité**, l’état d’intégrité est renvoyé pour les résultats filtrés dans la table.  

![Exemple d’état d’intégrité des machines virtuelles Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Pour chaque élément de la liste, vous pouvez cliquer sur l’état d’intégrité correspondant pour lancer les diagnostics d’intégrité, qui indiquent la manière dont l’intégrité est évaluée pour la machine virtuelle sélectionnée. 

Dans la page **Machines virtuelles**, si vous sélectionnez le nom d’une machine virtuelle sous la colonne **Nom de la machine virtuelle**, vous êtes dirigé vers la page **Instance d’ordinateur virtuel**. Cette page fournit plus d’informations sur les alertes et les problèmes de critères d’intégrité qui affectent la machine virtuelle sélectionnée. Filtrez les détails d’état d’intégrité en sélectionnant l’icône **État d’intégrité** dans le coin supérieur gauche de la page pour voir les composants non sains. Vous pouvez également afficher les alertes d’intégrité de machine virtuelle déclenchées par un composant non sain classées par gravité de l’alerte.

Dans la vue de la **liste de machines virtuelles**, sélectionnez le nom d’une machine virtuelle pour ouvrir la page **Intégrité** de cette machine virtuelle, comme si vous sélectionniez directement **Insights (preview)** (Informations (préversion)) à partir de la machine virtuelle.

![VM Insights d’une machine virtuelle Azure sélectionnée](./media/vminsights-health/vminsights-directvm-health.png)

La page **machines virtuelles (préversion) d’Azure Monitor** affiche le cumul d’état d’intégrité cumulatif pour la machine virtuelle et les alertes. Cet état d’intégrité est classé par gravité, qui représente les alertes d’intégrité de machine virtuelle déclenchées lorsque l’état d’intégrité est passé de sain à non sain, en fonction de critères. Sélectionnez **VMs in critical condition** (Machines virtuelles en condition critique) pour ouvrir une page avec une liste d’une ou plusieurs machines virtuelles dans un état critique.

Sélectionnez l’état d’intégrité de l’une des machines virtuelles pour afficher la vue **Diagnostics de l’intégrité** de la machine virtuelle. Vous pouvez déterminer dans cette vue les critères d’intégrité reflétant un problème d’état d’intégrité. Lorsque la page **Diagnostics de l’intégrité** s’ouvre, elle affiche tous les composants de machine virtuelle et leurs critères d’intégrité associés ainsi que l’état d’intégrité actuel.

Pour plus d’informations, consultez la section [Diagnostics d’intégrité](#health-diagnostics).

Sélectionnez **Afficher tous les critères d’intégrité** pour ouvrir une page affichant une liste de tous les critères d’intégrité disponibles avec cette fonctionnalité. Les informations peuvent être filtrées plus précisément selon les options suivantes :

* **Type**. Il existe trois types de critères d’intégrité servant à évaluer les conditions et regrouper les états d’intégrité généraux d’une machine virtuelle surveillée :
    - **Unité**. Mesure certains aspects d’une machine virtuelle. Ce type de critère d’intégrité peut vérifier un compteur de performances pour déterminer les performances du composant, exécuter un script pour effectuer une transaction synthétique ou guetter un événement qui indique une erreur. Le filtre est défini sur unité par défaut.
    - **Dépendance**. Fournit un rollup d’intégrité entre les différentes entités. Ce critère d’intégrité permet à l’intégrité d’une entité de dépendre de l’intégrité d’un autre type d’entité sur laquelle elle s’appuie pour un fonctionnement réussi.
    - **Agrégat**. Fournit un état d’intégrité combiné de critères d’intégrité similaires. Les critères d’intégrité Unité et Dépendance sont généralement configurés sous un critère d’intégrité agrégé. En plus de fournir une meilleure organisation générale des nombreux critères d’intégrité différents ciblant une entité, le critère d’intégrité agrégé fournit un état d’intégrité unique pour différentes catégories d’entités.

* **Catégorie**. Type de critère d’intégrité utilisé pour regrouper des critères similaires à des fins de génération de rapports. Ces catégories sont **Disponibilité** et **Performances**.

Pour afficher les instances non saines, sélectionnez une valeur dans la colonne **Unhealthy Component** (Composant non sain). Dans cette page, un tableau répertorie les composants qui se trouvent dans un état d’intégrité critique.

## <a name="health-diagnostics"></a>Diagnostics d'intégrité

La page **Diagnostics de l’intégrité** vous permet de visualiser le modèle d’intégrité d’une machine virtuelle. Cette page répertorie tous les composants de machine virtuelle, les critères d’intégrité associés, les changements d’état et les autres problèmes importants identifiés par les composants surveillés liés à la machine virtuelle.

![Exemple de page de Diagnostics d’intégrité pour une machine virtuelle](./media/vminsights-health/health-diagnostics-page-01.png)

Démarrez les diagnostics d’intégrité à l’aide des méthodes suivantes :

* Par rollup d’état d’intégrité pour toutes les machines virtuelles à partir de la perspective d’agrégation des machines virtuelles dans Azure Monitor :

    1. Sur la page **Intégrité**, sélectionnez l’icône d’état d’intégrité **Critique**, **Avertissement**, **Sain** ou **Inconnu** dans la section **Guest VM health** (Intégrité de la machine virtuelle invitée).
    2. Accédez à la page qui répertorie toutes les machines virtuelles correspondant à cette catégorie filtrée.
    3. Sélectionnez la valeur dans la colonne **État d’intégrité** pour ouvrir les diagnostics d’intégrité étendus à cette machine virtuelle.

* Par système d’exploitation à partir de la perspective d’agrégation de la machine virtuelle dans Azure Monitor. Sous **Distribution de machine virtuelle**, sélectionnez l’une des valeurs de colonne pour ouvrir la page **Machines virtuelles** page et retourner une liste dans la table correspondant à la catégorie filtrée. Sélectionnez la valeur indiquée dans la colonne **État d’intégrité** pour ouvrir les diagnostics d’intégrité de la machine virtuelle sélectionnée.
 
* Depuis la machine virtuelle invitée, dans l’onglet **Intégrité** d’Azure Monitor pour machines virtuelles, sélectionnez **View health diagnostics** (Afficher les diagnostics d’intégrité).

Les diagnostics d’intégrité classent les informations d’intégrité dans deux catégories : disponibilité et performances.
 
Tous les critères d’intégrité définis pour un composant, tels que le disque logique, l’UC, etc., peuvent être affichés sans filtrer sur les deux catégories. Ces vues peuvent être présentées dans une vue d’ensemble de critères ou via un filtrage des résultats par catégorie lorsque vous sélectionnez **Disponibilité** ou **Performances**.

La catégorie de critères peut également être vue en regard de la colonne **Critères d’intégrité**. Si les critères ne correspondent pas à la catégorie sélectionnée, un message indiquant qu’**aucun critère d’intégrité n’est disponible pour la catégorie sélectionnée** s’affiche dans la colonne **Critères d’intégrité**.

L’état d’un critère d’intégrité est défini par un des quatre types suivants : **Critique**, **Avertissement**, **Sain** et **Inconnu**. Les trois premiers sont configurables, ce qui signifie que vous pouvez modifier les valeurs de seuil des analyses à l’aide du volet de configuration **Critères d’intégrité**. Cela est également possible à l’aide de l’[opération de mise à jour de la surveillance](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update) de l’API REST Azure Monitor. L’état **Inconnu** n’est pas configurable et est réservé à des scénarios spécifiques.

La page **Diagnostics de l’intégrité** comporte trois sections principales :

* Modèle de composant
* Critères d’intégrité
* Changements d'état

![Sections d’une page de Diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modèle de composant

La colonne située à l’extrême gauche de la page **Diagnostics de l’intégrité** est la colonne **Modèle de composant**. Tous les composants associés à la machine virtuelle sont affichés dans cette colonne ainsi que leur état d’intégrité actuel.

Dans l’exemple suivant, les composants détectés sont le **disque**, le **disque logique**, le **processeur**, la **mémoire** et le **système d’exploitation**. Plusieurs instances de ces composants sont découvertes et affichées dans cette colonne.

Par exemple, la figure suivante indique que la machine virtuelle comprend deux instances de disques logiques **C:** et **D:** , dont l’état est sain :

![Exemple de modèle de composant présenté dans les diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critères d’intégrité

La colonne centrale de la page de diagnostics d’intégrité est la colonne **Critères d’intégrité**. Le modèle d’intégrité défini pour la machine virtuelle s’affiche dans une arborescence hiérarchique. Il se compose des critères d’intégrité Unité et Agrégat.

![Exemples de critères d’intégrité présentés dans les diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Un critère d’intégrité mesure l’intégrité d’une instance surveillée et peut être une valeur de seuil, l’état d’une entité, etc. Un critère d’intégrité comporte deux ou trois seuils d’état d’intégrité configurables, comme décrit plus haut. À tout moment, le critère d’intégrité ne peut être que dans l’un de ses états potentiels.

Le modèle d’intégrité définit des critères qui déterminent l’intégrité de la cible globale et des composants de la cible. La hiérarchie de critères est illustrée dans la section **Critères d’intégrité** de la page **Diagnostics de l’intégrité**.

La stratégie de Rollup d’intégrité fait partie intégrante de la configuration des critères d’intégrité d’agrégat (définie par défaut sur **worst-of** (le pire)). Vous trouverez un ensemble par défaut de critères d’intégrité exécutés dans le cadre de cette fonctionnalité dans la section [Surveillance des détails de configuration](#monitoring-configuration-details) de cet article.

Vous pouvez également utiliser la [liste d’instances de surveillance par ressource](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) de l’API REST Azure Monitor pour obtenir une liste de tous les critères d’intégrité. Ces critères incluent des détails de configuration en cours d’exécution sur la ressource de machine virtuelle Azure.

La configuration du type de critères d’intégrité **Unité** peut être modifiée en sélectionnant le lien des points de suspension à droite. Sélectionnez **Afficher les détails** pour ouvrir le volet de configuration.

![Exemple de configuration de critères d’intégrité](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Dans le volet de configuration des critères d’intégrité sélectionnés, si vous utilisez l’exemple **Average Disk Seconds Per Write** (Moyenne de secondes sur disque par écriture), le seuil peut être configuré avec une valeur numérique différente. Il s’agit d’une surveillance à deux états, ce qui signifie qu’elle peut uniquement passer de l’état **Sain** à l’état **Avertissement**.

D’autres critères d’intégrité utilisent parfois trois états, pour lesquels vous pouvez configurer une valeur pour les seuils d’état d’intégrité Avertissement et Critique. Vous pouvez également modifier un seuil à l’aide de la [configuration de la surveillance](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update) de l’API REST Azure Monitor.

>[!NOTE]
>L’application des modifications de configuration des critères d’intégrité à une instance les transmet à toutes les instances surveillées. Par exemple, si vous sélectionnez **Disk -1 D:** puis modifiez le seuil **Average Disk Seconds Per Write** (Moyenne de secondes sur disque par écriture), la modification s’applique à toutes les instances découvertes et surveillées sur la machine virtuelle.


![Exemple de configuration d’un critère d’intégrité d’un moniteur d'unités](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Pour plus d’informations sur les critères d’intégrité, nous incluons des articles de la base de connaissances pour vous aider à identifier les problèmes, les causes et les mesures de résolution. Sélectionnez **Afficher les informations** sur la page pour voir l’article de la base de connaissances lié.

Pour passer en revue tous les articles de la base de connaissances inclus avec l’intégrité d’Azure Monitor pour machines virtuelles, consultez la [documentation de la base de connaissances sur l’intégrité d’Azure Monitor](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Changements d'état

La colonne la plus à droite de la page **Diagnostics de l’intégrité** est la colonne **Changements d’état**. Cette colonne répertorie tous les changements d’état associés aux critères d’intégrité sélectionnés dans la section **Critères d’intégrité**, ou les changements d’état de la machine virtuelle si une machine virtuelle a été sélectionnée dans la colonne **Modèle de composant** ou **Critères d’intégrité** de la table.

![Exemples de changements d’état présentés dans les diagnostics d’intégrité](./media/vminsights-health/health-diagnostics-page-statechanges.png)

La section suivante montre l’état de critères d’intégrité et l’heure associée. Ces informations présentent le dernier état en haut de la colonne.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Association des colonnes Modèle de composant, Critères d’intégrité et Changements d’état

Les trois colonnes sont liées entre elles. Lorsque vous sélectionnez une instance dans la colonne **modèle de composant**, la colonne **Critères d’intégrité** est filtrée sur cette vue de composant. En conséquence, la colonne **Changements d’état** est mise à jour en fonction des critères d’intégrité sélectionnés.

![Exemple de sélection d’instance et de résultats surveillés](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Par exemple, si vous sélectionnez *Disk - 1 D:* dans la liste sous **Modèle de composant**, **Critères d’intégrité** est filtré sur *Disk - 1D:* et **Changements d’état** indique le changement d’état en fonction de la disponibilité de *Disk - 1 D:* .

Pour afficher l’état d’intégrité mis à jour, vous pouvez actualiser la page des diagnostics d’intégrité en sélectionnant le lien **Actualiser**. S’il existe une mise à jour de l’état d’intégrité des critères d’intégrité en fonction de l’intervalle d’interrogation prédéfini, cette tâche vous permet d’éviter l’attente en reflétant l’état d’intégrité le plus récent. **L’état des critères d’intégrité** est un filtre qui vous permet de limiter les résultats en fonction de l’état d’intégrité sélectionné : Sain, Avertissement, Critique, Inconnu et Tous. L’heure **Dernière mise à jour** indiquée dans l’angle supérieur droit correspond à la dernière actualisation de la page des diagnostics d’intégrité.

## <a name="alerts"></a>Alertes

L’intégrité d’Azure Monitor pour machines virtuelles s’intègre aux [alertes Azure](../../azure-monitor/platform/alerts-overview.md). Elle déclenche une alerte lorsque des critères prédéfinis, s’ils sont détectés, entraînent un changement de l’état de sain à non sain. Les alertes sont classées par gravité, de Gravité 0 à Gravité 4, où Gravité 0 est le niveau le plus élevé.

Les alertes ne sont pas associées à un groupe d’actions pour vous avertir quand l’alerte a été déclenchée. Un utilisateur doté du rôle Propriétaire au niveau de l'abonnement doit configurer des notifications en suivant les étapes décrites dans la section [Configurer des alertes](#configure-alerts).

Le nombre total d’alertes d’intégrité de machine virtuelle classées par gravité est disponible au niveau du tableau de bord **Intégrité** dans la section **Alertes**. Lorsque vous sélectionnez le nombre total d’alertes ou le numéro correspondant à un niveau de gravité, la page **Alertes** s’ouvre et affiche toutes les alertes correspondant à votre sélection.

Par exemple, si vous sélectionnez la ligne correspondant au **Niveau de gravité 1**, vous voyez l’écran suivant :

![Exemple de toutes les alertes de niveau de gravité 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

La page **Toutes les alertes** n’est pas limité pour n’afficher que les alertes correspondant à votre sélection. Elle est également filtrée par **Type de ressource** pour n’afficher que les alertes d’intégrité déclenchées par une ressource de machine virtuelle. Ce format est reflété dans la liste des alertes, sous la colonne **Ressource cible**, qui indique la machine virtuelle ayant déclenché l’alerte lorsqu’une condition non saine a été remplie.

Les alertes d’autres types de ressources ou services ne sont pas présentées dans cette vue. Ces alertes incluent les alertes de journal, qui sont basées sur des requêtes de journal ou des alertes de métrique que vous verriez normalement dans la page [Toutes les alertes](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) d’Azure Monitor par défaut.

Vous pouvez filtrer cette vue en sélectionnant des valeurs dans les menus déroulants en haut de la page.

|Colonne |Description |
|-------|------------|
|Subscription |Sélectionnez un abonnement Azure. Seules les alertes dans l’abonnement sélectionné sont incluses dans la vue. |
|Groupe de ressources |Sélectionnez un seul groupe de ressources. Seules les alertes avec des cibles dans le groupe de ressources sélectionné sont incluses dans la vue. |
|Type de ressource |Sélectionnez un ou plusieurs types de ressources. Par défaut, seules les alertes des **machines virtuelles** cibles sont sélectionnées et incluses dans cette vue. Cette colonne n’est disponible qu’après qu’un groupe de ressources a été spécifié. |
|Ressource |Sélectionnez une ressource. Seules les alertes ayant ces ressources pour cible sont incluses dans l’affichage. Cette colonne n’est disponible qu’après qu’un type de ressource a été spécifié. |
|severity |Sélectionnez un niveau de gravité d’alerte ou **Tous** pour inclure les alertes de tous les niveaux de gravité. |
|Condition d’analyse |Sélectionnez une condition de surveillance pour filtrer les alertes selon qu’elles soient déclenchées ou résolues par le système (si la condition n’est plus active). Vous pouvez également sélectionner **Tous** pour inclure les alertes de toutes les conditions. |
|État d’alerte |Sélectionnez un état d’alerte **Nouveau**, **Reconnu**, **Fermé** ou **Tous** pour inclure les alertes de tous les états. |
|Service de surveillance |Sélectionnez un service ou sélectionnez **Tous** pour inclure tous les services. Seules les alertes d’informations de machine virtuelle sont prises en charge pour cette fonctionnalité.|
|Plage temporelle| Seules les alertes déclenchées dans la fenêtre de temps sélectionnée seront incluses dans l’affichage. Les valeurs prises en charge sont : dernière heure, dernières 24 heures, 7 derniers jours et 30 derniers jours. |

Lorsque vous sélectionnez une alerte, la page **Détails de l’alerte** s’affiche. Cette page fournit des détails de l’alerte et vous permet de changer son état.

Pour plus d’informations sur la gestion des alertes, consultez [Créer, afficher et gérer des alertes à l’aide d’Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Ni la création d’alertes basées sur des critères d’intégrité ni la modification des règles d’alertes d’intégrité existantes dans Azure Monitor à partir du portail ne sont actuellement prises en charge.


![Volet des détails de l’alerte pour une alerte sélectionnée](./media/vminsights-health/alert-details-pane-01.png)

Vous pouvez modifier un état d’alerte pour une ou plusieurs alertes en les sélectionnant puis en sélectionnant **Modifier l’état** depuis la page **Toutes les alertes** dans le coin supérieur gauche. Sélectionnez l’un des états dans le volet **Change alert state** (Modifier l’état de l’alerte), ajoutez une description de la modification dans le champ **Commentaire**, puis sélectionnez **OK** pour valider vos modifications. Lorsque les informations sont vérifiées et les modifications appliquées, suivez la progression sous **Notifications** dans le menu.

### <a name="configure-alerts"></a>Configurer des alertes
Vous ne pouvez pas gérer certaines tâches de gestion des alertes à partir du portail Azure. Ces tâches doivent être effectuées à l’aide de l’[API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Plus précisément :

- Activation ou désactivation d’une alerte pour des critères d’intégrité
- Configuration de notifications pour des alertes de critères d’intégrité

Chaque exemple utilise [ARMClient](https://github.com/projectkudu/armclient) sur votre machine Windows. Si vous ne connaissez pas cette méthode, consultez [Using ARMClient](../platform/rest-api-walkthrough.md#use-armclient) (Utilisation du client ARM).

#### <a name="enable-or-disable-an-alert-rule"></a>Activer ou désactiver une règle alerte

Pour activer ou désactiver une alerte pour des critères d’intégrité spécifiques, la propriété **alertGeneration** doit être définie sur la valeur **Désactivé** ou **Activé**.

Pour identifier le *monitorId* de critères d’intégrité spécifiques, l’exemple suivant montre comment rechercher cette valeur pour le critère **LogicalDisk\Avg Disk Seconds Per Transfer** :

1. Dans une fenêtre de terminal, saisissez **armclient.exe login**. Vous êtes alors invité à vous connecter à Azure.

2. Entrez la commande suivante pour récupérer tous les critères d’intégrité actifs sur une machine virtuelle spécifique et identifier la valeur de la propriété *monitorId* :

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    L’exemple suivant montre la sortie de la commande *armclient GET*. Notez la valeur de *MonitorId*. Cette valeur est requise à l’étape suivante, dans laquelle nous devons spécifier l’ID des critères d’intégrité et modifier leur propriété pour créer une alerte.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Entrez la commande suivante pour modifier la propriété *alertGeneration* :

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Entrez la commande GET utilisée à l’étape 2 pour vérifier que la valeur de propriété est définie sur **Désactivé**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associer un groupe d’actions à des critères d’intégrité

L’intégrité d’Azure Monitor pour machines virtuelles prend en charge les notifications par SMS et e-mail lorsque des alertes sont générées à partir de critères d’intégrité non sains. Pour configurer des notifications, notez le nom du groupe d’actions configuré pour envoyer des notifications par SMS ou e-mail.

>[!NOTE]
>Cette action doit être effectuée sur chaque machine virtuelle surveillée qui doit recevoir une notification. Elle ne s’applique à toutes les machines virtuelles d’un groupe de ressources.

1. Dans une fenêtre de terminal, entrez *armclient.exe login*. Vous êtes alors invité à vous connecter à Azure.

2. Entrez la commande suivante pour associer un groupe d’actions à des règles d’alerte :
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Pour vérifier que la valeur de la propriété **actionGroupResourceIds** a été correctement mise à jour, entrez la commande suivante :

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    La sortie doit ressembler aux critères suivants :
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les limitations et les performances globales des machines virtuelles, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).

- Pour en savoir plus sur les dépendances des applications détectées, consultez [View Azure Monitor for VMs Map](vminsights-maps.md) (Afficher la carte d’Azure Monitor pour machines virtuelles).
