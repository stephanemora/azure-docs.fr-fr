---
title: Surveiller Azure Site Recovery | Microsoft Docs
description: Surveiller et résoudre les opérations et les problèmes de réplication d’Azure Site Recovery à l’aide du portail
services: site-recovery
author: bsiva
manager: abhemra
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 07/19/2018
ms.author: bsiva
ms.openlocfilehash: 89270a0bd1914a2486c8cc634563bdded743adaa
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746471"
---
# <a name="monitor-and-troubleshoot-site-recovery"></a>Surveiller et résoudre les problèmes liés à Site Recovery

Dans cet article, vous allez apprendre à utiliser les fonctionnalités intégrées de surveillance et de résolution des problèmes d’Azure Site Recovery. 

## <a name="use-the-dashboard"></a>Utiliser le tableau de bord

1. Dans le coffre, cliquez sur **Vue d’ensemble** pour ouvrir le tableau de bord Site Recovery. Le tableau de bord comprend des pages pour Site Recovery et Azure Backup, que vous pouvez afficher à tour de rôle.

    ![Tableau de bord Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Le tableau de bord regroupe toutes les informations de surveillance pour le coffre dans un emplacement unique. À partir du tableau de bord, vous pouvez explorer plus en détail différentes zones. 

    ![Tableau de bord Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Sous **Éléments répliqués**, cliquez sur **Afficher tout** pour voir tous les serveurs dans le coffre.
4. Effectuez un zoom avant en cliquant sur les informations d’état de chaque section. Sous **Vue d’infrastructure**, vous pouvez trier les informations de surveillance selon le type de machines que vous répliquez.

## <a name="monitor-replicated-items"></a>Surveiller les éléments répliqués

La section Éléments répliqués affiche l’intégrité de toutes les machines pour lesquelles la réplication est activée dans le coffre.

**State** | **Détails**
--- | ---
Healthy | La réplication s’exécute normalement. Aucun symptôme d’erreur ou d’avertissement n’est détecté.
Avertissement | Un ou plusieurs symptômes d’avertissement qui peuvent avoir un impact sur la réplication sont détectés.
Critique | Un ou plusieurs symptômes d’erreur de réplication critique ont été détectés.<br/><br/> Ces erreurs indiquent que la réplication est soit bloquée, soit évolue moins vite que le rythme de modification des données.
Non applicable | Serveurs ne devant pas être répliqués pour le moment. Cela peut inclure des machines qui ont été basculées.

## <a name="monitor-test-failovers"></a>Surveiller les tests de basculement

Vous pouvez afficher l’état du test de basculement pour les machines présentes dans le coffre.

- Nous vous recommandons d’exécuter un test de basculement sur les machines répliquées au moins une fois tous les six mois. Cela vous permet de vérifier que le basculement fonctionne comme prévu, sans interrompre votre environnement de production. 
- Un test de basculement est considéré comme réussi uniquement une fois que le basculement et le nettoyage après basculement sont terminés.

**State** | **Détails**
--- | ---
Test recommandé | Machines qui n’ont pas fait l’objet d’un test de basculement depuis l’activation de la protection.
Effectué | Machines avec un ou plusieurs tests de basculement réussis.
Non applicable | Machines actuellement non éligibles à un test de basculement. Par exemple, pour les machines ayant basculé, une réplication initiale/un test de basculement/un basculement est en cours.

## <a name="monitor-configuration-issues"></a>Surveiller les problèmes de configuration

La section **Problèmes de configuration** affiche la liste des problèmes qui peuvent affecter votre capacité à réussir le basculement.

- Les problèmes de configuration (autres que la disponibilité des mises à jour logicielles) sont détectés par une validation périodique qui s’exécute toutes les 12 heures par défaut. Vous pouvez forcer l’exécution immédiate de l’opération de validation en cliquant sur l’icône d’actualisation en regard de l’intitulé de la section **Problèmes de configuration**.
- Cliquez sur les liens pour obtenir plus d’informations. Pour les problèmes affectant des machines spécifiques, cliquez sur **Doit être surveillé** dans la colonne **Configurations cibles**. Les détails incluent des recommandations de correction.

**State** | **Détails**
--- | ---
Configurations manquantes | Un paramètre nécessaire est manquant, par exemple un réseau de récupération ou un groupe de ressources.
Ressources manquantes | Une ressource spécifiée est introuvable ou n’est pas disponible dans l’abonnement. Par exemple, la ressource a été supprimée ou migrée. Les ressources surveillées incluent le groupe de ressources cible, le sous-réseau/réseau virtuel cible, le compte de stockage cible/des journaux, le groupe à haute disponibilité cible et l’adresse IP cible.
Quota d’abonnement |  Le quota de ressources disponibles dans l’abonnement est comparé au solde requis pour faire basculer toutes les machines dans le coffre.<br/><br/> Si les ressources sont insuffisantes, un solde de quota insuffisant est signalé.<br/><br/> Pour les quotas, le système surveille le nombre de cœurs de machine virtuelle, le nombre de cœurs de famille de machines virtuelles et le nombre de cartes d’interface réseau.
Mises à jour logicielles | La disponibilité des nouvelles mises à jour logicielles et des informations sur les versions logicielles arrivant à expiration.


## <a name="monitoring-errors"></a>Surveillance des erreurs 
La section **Résumé des erreurs** affiche les erreurs actives qui peuvent avoir un impact sur la réplication des serveurs dans le coffre, ainsi que le nombre de machines affectées.

- Au début de la section, les erreurs ayant un impact sur les composants d’infrastructure locaux sont affichées. Par exemple, la non réception d’une pulsation du fournisseur Azure Site Recovery en cours d’exécution sur le serveur de configuration local, le serveur VMM ou l’hôte Hyper-V.
- Les erreurs de réplication ayant un impact sur les serveurs répliqués apparaissent ensuite.
- Le tableau est trié par ordre décroissant de la gravité des erreurs, puis par ordre décroissant du nombre de machines concernées.
- Le nombre de serveurs concernés est une information qui permet de comprendre si un même problème sous-jacent peut concerner plusieurs machines. Par exemple, un problème réseau peut avoir un impact sur toutes les machines en cours de réplication vers Azure. 
- Plusieurs erreurs de réplication peuvent se produire sur un seul serveur. Dans ce cas, chaque erreur inclut ce serveur dans la liste de ses serveurs affectés. Une fois le problème est résolu, les paramètres de réplication sont optimisés et l’erreur est résolue à partir de la machine.

## <a name="monitor-the-infrastructure"></a>Surveiller l’infrastructure

La section **Vue d’infrastructure** affiche les composants d’infrastructure impliqués dans la réplication, ainsi que l’intégrité de la connectivité entre les serveurs et les services Azure.

- Une ligne verte indique que la connexion est opérationnelle.
- Une ligne rouge sur laquelle est superposée une icône d’erreur indique l’existence d’une ou de plusieurs erreurs qui affectent la connectivité.
-  Placez le pointeur de la souris sur l’icône d’erreur pour afficher l’erreur et le nombre d’entités concernées. Cliquez sur l’icône pour afficher une liste filtrée des entités concernées.

    ![Vue d’infrastructure de Site Recovery (coffre)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Conseils relatifs à la surveillance de l’infrastructure

- Assurez-vous que les composants d’infrastructure locaux (serveur de configuration, serveurs de traitement, serveurs VMM, hôtes Hyper-V, machines virtuelles VMware) exécutent les dernières versions du fournisseur et/ou des agents Site Recovery.
- Pour utiliser toutes les fonctionnalités de la vue d’infrastructure, vous devez exécuter le [Correctif cumulatif 22](https://support.microsoft.com/help/4072852) pour ces composants.
- Pour utiliser la vue d’infrastructure, sélectionnez le scénario de réplication approprié dans votre environnement. Vous pouvez effectuer un zoom avant pour obtenir plus d’informations. Le tableau suivant affiche les scénarios qui sont représentés.

    **Scénario** | **State**  | **Vue disponible ?**
    --- |--- | ---
    **Réplication entre des sites locaux** | Tous les états | Non  
    **Réplication de machines virtuelles Azure entre les régions Azure**  | Réplication activée/réplication initiale en cours | Oui
    **Réplication de machines virtuelles Azure entre les régions Azure** | Basculement/restauration automatique | Non    
    **Réplication VMware vers Azure** | Réplication activée/réplication initiale en cours | Oui     
    **Réplication VMware vers Azure** | Basculement/restauration automatique | Non       
    **Réplication Hyper-V vers Azure** | Basculement/restauration automatique | Non 

- Pour afficher la vue d’infrastructure pour une seule machine en cours de réplication, dans le menu du coffre, cliquez sur **Éléments répliqués**, puis sélectionnez un serveur.  

### <a name="common-questions"></a>Questions courantes


**Pourquoi le nombre de machines virtuelles dans la vue d’infrastructure du coffre diffère-t-il du nombre total affiché dans la section des éléments répliqués ?**

La vue d’infrastructure du coffre est limitée par les scénarios de réplication. Seules les machines incluses dans le scénario de réplication sélectionné sont comptabilisées dans la vue d’infrastructure. En outre, nous comptabilisons uniquement les machines virtuelles qui sont configurées pour être répliquées vers Azure. Les machines ayant basculé ou en cours de réplication vers un site local ne sont pas comptabilisées dans la vue.

**Pourquoi le nombre d’éléments répliqués affichés dans la section des éléments principaux diffère-t-il du nombre total d’éléments répliqués indiqué dans le tableau de bord ?**

Seules les machines sur lesquelles la réplication initiale est terminée sont incluses dans le nombre indiqué dans la section des éléments principaux. Dans la section des éléments répliqués, le total inclut toutes les machines présentes dans le coffre, y compris celles dont la réplication initiale est en cours.


## <a name="monitor-recovery-plans"></a>Surveiller les plans de récupération

Dans la **section Plans de récupération**, vous pouvez consulter le nombre de plans, créer des plans et modifier des plans existantes.  

## <a name="monitor-jobs"></a>Surveiller des travaux

La section **Travaux** affiche l’état des opérations Site Recovery.

- La plupart des opérations dans Azure Site Recovery s’exécutent de façon asynchrone, avec la création d’un travail de suivi qui est utilisé pour suivre l’état d’avancement de chaque opération. 
- L’objet de travail dispose de toutes les informations nécessaires pour suivre l’état et la progression des opérations. 

Pour surveiller les travaux, procédez comme suit :

1. Dans le tableau de bord > section **Travaux**, vous pouvez voir un résumé des travaux terminés, en cours d’exécution ou en attente pour l’entrée au cours des dernières 24 heures. Vous pouvez cliquer sur n’importe quel état pour obtenir plus d’informations sur les travaux correspondants.
2. Cliquez sur **Afficher tout** pour afficher tous les travaux des dernières 24 heures.

    > [!NOTE]
    > Vous pouvez également accéder aux informations sur les travaux à partir du menu du coffre > **Travaux Site Recovery**. 

2. La liste **Travaux Site Recovery** affiche tous les travaux. Dans le menu supérieur, vous pouvez afficher les détails d’erreur pour des travaux spécifiques, filtrer la liste des travaux selon des critères spécifiques et exporter les détails d’un travail sélectionné vers Excel.
3. Cliquez sur un travail pour en afficher les détails. 

## <a name="monitor-virtual-machines"></a>Surveillance des machines virtuelles

Parallèlement au tableau de bord, vous pouvez surveiller les machines à partir de la page de machines virtuelles. 

1. Dans le coffre, cliquez sur **Éléments répliqués** pour obtenir la liste des machines répliquées.  L’autre méthode consiste à afficher la liste filtrée des éléments protégés en cliquant sur l’un des raccourcis limités disponibles sur la page du tableau de bord.

    ![Vue de la liste des éléments répliqués dans Azure Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Dans la page **Éléments répliqués**, vous pouvez afficher et filtrer les informations. Dans le menu d’action supérieur, vous pouvez effectuer des actions pour une machine donnée, y compris exécuter un test de basculement ou afficher des erreurs spécifiques.
3.  Cliquez sur **Colonnes** pour afficher des colonnes supplémentaires, par exemple pour afficher le RPO, les problèmes de configuration cible et les erreurs de réplication.
4. Cliquez sur **Filtre** pour afficher les informations en fonction de paramètres spécifiques telles que l’intégrité de la réplication, ou une stratégie de réplication particulière.
5. Cliquez avec le bouton droit sur une machine pour y lancer des opérations telles qu’un test de basculement, ou pour afficher les détails des erreurs qui lui sont associées.
6. Cliquez sur une machine pour en afficher les détails. Les détails sont les suivants :
      - **Informations de réplication** : état et intégrité actuels de la machine.
      - **RPO** (objectif de point de récupération) : RPO actuel de la machine virtuelle et heure du dernier calcul du RPO.
      - **Points de récupération**: derniers points de récupération disponibles pour la machine.
      - **Disponibilité du basculement** : indique si un test de basculement a été exécuté pour la machine, la version de l’agent en cours d’exécution sur la machine (pour les machines exécutant le service Mobilité) et les éventuels problèmes de configuration.
      - **Erreurs** : liste des symptômes d’erreur de réplication actuellement observés sur la machine, ainsi que les causes/actions possibles.
      - **Événements** : liste chronologique des événements récents ayant un impact sur la machine. La colonne Détails de l’erreur indique les erreurs actuellement observables sur la machine, tandis que la colonne Événements est un enregistrement historique des problèmes qui ont eu un impact sur la machine.
      - **Vue d’infrastructure** : affiche l’état de l’infrastructure pour le scénario de réplication des machines vers Azure.

    ![Vue d’ensemble/détails des éléments répliqués d’Azure Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Questions courantes

**En quoi l’objectif de point de récupération (ou RPO) est-il différent du dernier point de récupération disponible ?**


- Site Recovery utilise un processus asynchrone en plusieurs étapes pour répliquer des machines vers Azure.
- À l’avant-dernière étape de la réplication, les modifications récentes apportées à la machine, ainsi que les métadonnées, sont copiées dans un compte de stockage de cache/du journal.
- Ces modifications, ainsi que la balise qui identifie un point récupérable, sont écrites dans le compte de stockage de la région cible.
-  Site Recovery peut désormais générer un point récupérable pour la machine virtuelle.
- À ce stade, le RPO a été atteint pour les modifications téléchargées jusqu’à présent au compte de stockage. En d’autres termes, le RPO de la machine est à ce stade égal au temps écoulé depuis l’horodatage correspondant au point récupérable.
- À présent, Site Recovery récupère les données téléchargées à partir du compte de stockage et les applique aux disques de réplica créés pour la machine.
- Site Recovery génère ensuite un point de récupération et le fournit pour effectuer la récupération lors du basculement. Le dernier point de récupération disponible indique l’horodatage correspondant au dernier point de récupération déjà traité et appliqué aux disques de réplica.

> [!NOTE]
> Une heure système incorrecte sur la machine source en cours de réplication ou les serveurs d’infrastructure locaux fausse la valeur de RPO calculée. Pour obtenir un RPO précis, assurez-vous que l’horloge système affiche la même heure sur l’ensemble des serveurs et des machines. 

## <a name="subscribe-to-email-notifications"></a>S’abonner aux notifications par courrier électronique

Vous pouvez vous abonner pour recevoir des notifications par courrier électronique pour ces événements critiques :
 
- État critique de la machine répliquée.
- Aucune connectivité entre les composants d’infrastructure locaux et le service Site Recovery. La connectivité entre Site Recovery et les serveurs locaux enregistrés dans un coffre est détectée à l’aide d’un mécanisme de pulsation.
- Échecs de basculement.

Pour vous abonner, procédez comme suit :

Dans le coffre > section **Surveillance et rapports**, cliquez sur **Événements Site Recovery**.
2. Cliquez sur **Notifications par e-mail**.
3. Sous **Notifications par e-mail**, activez les notifications et spécifiez le destinataire des notifications. Vous pouvez choisir d’envoyer les notifications à tous les administrateurs de l’abonnement ou à des adresses e-mail spécifiques.

    ![Notifications par e-mail](./media/site-recovery-monitor-and-troubleshoot/email.png)
