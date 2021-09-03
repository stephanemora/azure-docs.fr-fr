---
title: À propos du basculement et de la restauration automatique dans Azure Site Recovery - Préversion
description: En savoir plus sur le basculement et la restauration automatique dans Azure Site Recovery - Préversion
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: d7e08623a1e297d618365a126f835f88e9dfb716
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535343"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback---preview"></a>À propos du basculement/restauration automatique de la récupération d’urgence locale - Préversion

Cet article fournit une vue d’ensemble du basculement et de la restauration automatique lors de la récupération d’urgence de machines locales vers Azure avec [Azure Site Recovery](site-recovery-overview.md) - Préversion.

Pour plus d’informations sur le basculement et la restauration automatique dans les versions classiques d’Azure Site Recovery, [consultez cet article](failover-failback-overview.md).

## <a name="recovery-stages"></a>Étapes de la récupération

Le basculement et la restauration automatique dans Azure Site Recovery comportent quatre étapes :

- **Étape 1 : basculer à partir d’un site local** : après la configuration de la réplication vers Azure pour les machines locales, lorsque votre site local tombe en panne, vous basculez ces machines vers Azure. Une fois le basculement effectué, les machines virtuelles Azure sont créées à partir des données répliquées.
- **Étape 2 : reprotéger les machines virtuelles Azure** : dans Azure, vous reprotégez les machines virtuelles Azure afin qu’elles commencent à se répliquer sur le site local. La machine virtuelle locale (si elle est disponible) est désactivée au cours de la reprotection afin de garantir la cohérence des données.
- **Étape 3 : basculer à partir d’Azure** : quand votre site local s’exécute de nouveau normalement, vous opérez un autre basculement, cette fois pour restaurer automatiquement les machines virtuelles Azure sur votre site local. Vous pouvez effectuer une restauration automatique vers l’emplacement d’origine à partir duquel vous avez basculé ou vers un autre emplacement. Cette activité est appelée *basculement planifié*.
- **Étape 4 : reprotéger les machines locales** : après restauration, réactivez la réplication des machines locales sur Azure.

## <a name="failover"></a>Basculement

Vous effectuez un basculement dans le cadre de votre stratégie de continuité d’activité et de reprise d’activité (BCDR).

- La première étape de votre stratégie BCDR consiste à répliquer vos machines locales sur Azure de manière continue. Les utilisateurs accèdent aux charges de travail et applications qui s’exécutent sur les machines sources locales.
- En cas de besoin, par exemple en cas de panne en local, les machines de réplication sont basculées vers Azure. Les machines virtuelles Azure sont créées à l’aide des données répliquées.
- Pour la continuité des activités, les utilisateurs peuvent continuer à accéder aux applications sur les machines virtuelles Azure.

Le basculement est une activité en deux phases :

- **Basculer** : basculement qui crée et affiche une machine virtuelle Azure utilisant le point de récupération sélectionné.
- **Valider** : après le basculement, vous vérifiez la machine virtuelle dans Azure :
    - Vous pouvez ensuite valider le basculement vers le point de récupération sélectionné ou sélectionner un autre point pour la validation.
    - Une fois le basculement validé, le point de récupération ne peut pas être modifié.


## <a name="connect-to-azure-after-failover"></a>Se connecter à Azure après le basculement

Il existe plusieurs exigences pour se connecter aux machines virtuelles Azure créées après le basculement à l’aide de RDP/SSH.

**Type de basculement** | **Lieu** | **Actions**
--- | --- | ---
**Machine virtuelle Azure exécutant Windows** | Sur la machine locale, avant le basculement | **Accès via Internet** : activez le protocole RDP. Assurez-vous que les règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé pour tous les profils dans **Pare-feu Windows** > **Applications autorisées**.<br/><br/> **Accès via un VPN site à site** : activez le protocole RDP sur la machine. Vérifiez que RDP est autorisé dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées**, pour les réseaux **Domaine et Privé** .<br/><br/>  Vérifiez que la stratégie SAN du système d’exploitation est définie sur la valeur **OnlineAll**. [Plus d’informations](https://support.microsoft.com/kb/3031135)<br/><br/> Vérifiez qu’aucune mise à jour de Windows n’est en attente sur la machine virtuelle quand vous déclenchez un basculement. Windows Update peut démarrer lors du basculement, et vous ne pourrez pas vous connecter à la machine virtuelle tant que les mises à jour ne sont pas terminées.
**Machine virtuelle Azure exécutant Windows** | Sur la machine virtuelle Azure, après le basculement |  [Ajoutez une adresse IP publique](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) pour la machine virtuelle.<br/><br/> Les règles des groupes de sécurité réseau sur la machine virtuelle basculée (et le sous-réseau Azure auquel elle est connectée) doivent autoriser les connexions entrantes avec le port RDP.<br/><br/> Cochez **Diagnostics de démarrage** pour examiner une capture d’écran de la machine virtuelle. Si vous ne parvenez pas à vous connecter, vérifiez que la machine virtuelle est en cours d’exécution et consultez ces [conseils de résolution des problèmes](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Machine virtuelle Azure exécutant Linux** | Sur la machine locale, avant le basculement | Vérifiez que le service Secure Shell, sur la machine virtuelle, est défini pour démarrer automatiquement au démarrage du système.<br/><br/> Vérifiez que les règles de pare-feu autorisent une connexion SSH à ce dernier.
**Machine virtuelle Azure exécutant Linux** | Sur la machine virtuelle Azure, après le basculement | Les règles des groupes de sécurité réseau figurant sur la machine virtuelle basculée (et le sous-réseau Azure auquel elle est connectée) doivent autoriser les connexions entrantes avec le port SSH.<br/><br/> [Ajoutez une adresse IP publique](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) pour la machine virtuelle.<br/><br/> Cochez **Diagnostics de démarrage** pour obtenir une capture d’écran de la machine virtuelle.<br/><br/>

## <a name="types-of-failover"></a>Types de basculement

Site Recovery propose différentes options de basculement.

**Type de basculement** | **Détails** | **Récupération** | **Workflow**
--- | --- | --- | ---
**Test de basculement** | Utilisé pour exécuter une analyse qui valide votre stratégie BCDR, sans perte de données ni temps d’arrêt.| Crée une copie de la machine virtuelle dans Azure, sans impact sur la réplication en cours ou sur votre environnement de production. | 1. Exécutez un test de basculement sur une seule machine virtuelle ou sur plusieurs machines virtuelles dans un plan de récupération.<br/><br/> 2. Sélectionnez un point de récupération à utiliser pour le test de basculement.<br/><br/> 3. Sélectionnez un réseau Azure dans lequel la machine virtuelle Azure sera localisée lors de sa création après le basculement. Le réseau est utilisé uniquement pour le test de basculement.<br/><br/> 4. Vérifiez que le test a fonctionné comme prévu. Site Recovery nettoie automatiquement les machines virtuelles créées dans Azure pendant le test.
**Basculement planifié Hyper V**  | Généralement utilisé pour les temps d’arrêt planifiés.<br/><br/> Les machines virtuelles sources sont arrêtées. Les données les plus récentes sont synchronisées avant de lancer le basculement. | Aucune perte de données pour le flux de travail planifié. | 1. Planifiez une fenêtre de maintenance de temps d’arrêt et avertissez les utilisateurs.<br/><br/> 2. Mettez les applications orientées utilisateur hors connexion.<br/><br/> 3. Initiez un basculement planifié avec le dernier point de récupération. Le basculement ne s’exécute pas si la machine n’est pas arrêtée ou si des erreurs sont rencontrées.<br/><br/> 4. Après le basculement, vérifiez que la machine virtuelle Azure de réplica est active dans Azure.<br/><br/> 5. Validez le basculement pour terminer. L’action de validation annule tous les point de récupération.
**Basculement Hyper V** | S’exécute généralement en cas de panne non planifiée ou si le site principal n’est pas disponible.<br/><br/> Si vous le souhaitez, arrêtez la machine virtuelle et synchronisez les dernières modifications avant de lancer le basculement.  | Perte de données minimale pour les applications. | 1. Lancez votre plan BCDR. <br/><br/> 2. Lancez un basculement. Spécifiez si Site Recovery doit arrêter la machine virtuelle et synchroniser/répliquer les dernières modifications avant de déclencher le basculement.<br/><br/> 3. Vous pouvez basculer vers un certain nombre d'options de points de récupération, résumées dans le tableau ci-dessous.<br/><br/> Si vous n’activez pas l’option permettant d’arrêter la machine virtuelle, ou si Site Recovery ne pouvez pas l’arrêter, le point de récupération le plus récent est utilisé.<br/>Le basculement s’exécute même si la machine ne peut pas être arrêtée.<br/><br/> 4. Après le basculement, vous vérifiez que la machine virtuelle Azure de réplica est active dans Azure.<br/> Si nécessaire, vous pouvez sélectionner un autre point de récupération dans la fenêtre de rétention de 24 heures.<br/><br/> 5. Validez le basculement pour terminer. L’action de validation supprime tous les points de récupération disponibles.
**Basculement-VMware** | S’exécute généralement en cas de panne non planifiée ou si le site principal n’est pas disponible.<br/><br/> Spécifiez éventuellement que Site Recovery doit tenter de déclencher un arrêt de la machine virtuelle, et synchroniser et répliquer les modifications finales avant de lancer le basculement.  | Perte de données minimale pour les applications. | 1. Lancez votre plan BCDR. <br/><br/> 2. Initiez un basculement à partir de Site Recovery. Spécifiez si Site Recovery doit essayer de déclencher l’arrêt de la machine virtuelle et synchroniser avant d’exécuter le basculement.<br/> Le basculement s’exécute même si la machine ne peut pas être arrêtée.<br/><br/> 3. Après le basculement, vérifiez que la machine virtuelle Azure de réplica est active dans Azure. <br/>Si nécessaire, vous pouvez sélectionner un autre point de récupération dans la fenêtre de rétention de 72 heures.<br/><br/> 5. Validez le basculement pour terminer. L’action de validation annule tous les point de récupération.<br/> Pour les machines virtuelles Windows, Site Recovery désactive les outils VMware pendant le basculement.
**Basculement planifié-VMware** | Vous pouvez effectuer un basculement planifié à partir d’Azure vers un emplacement local. | Puisqu’il s’agit d’une activité de basculement planifié, le point de récupération est généré après le déclenchement de la tâche de basculement planifié. | Lorsque le basculement planifié est déclenché, les modifications en attente sont copiées localement, un dernier point de récupération de la machine virtuelle est généré et la machine virtuelle Azure est arrêtée.<br/><br/> Suivez le processus de basculement comme expliqué [ici](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises). Après cela, la machine locale est activée. Une fois le basculement planifié réussi, la machine est active dans votre environnement local.

## <a name="failover-processing"></a>Traitement du basculement

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ 8 à 10 minutes. Vous noterez éventuellement que les délais des tests de basculement sont plus longs pour les machines suivantes :

* Machines virtuelles VMware sur lesquelles le service DHCP n’est pas activé
* Machines virtuelles VMware qui n’ont pas les pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Options de point de récupération

Pendant le basculement, vous pouvez sélectionner un certain nombre d’options de point de récupération.

**Option** | **Détails**
--- | ---
**Dernier (objectif de point de récupération le plus faible)** | Cette option fournit l’objectif de point de récupération (RPO) le plus bas. Il traite tout d’abord toutes les données qui ont été envoyées au service Site Recovery, afin de créer un point de récupération pour chaque machine virtuelle avant de basculer vers celle-ci. Toutes les données sont répliquées sur ce point de récupération sur Site Recovery lorsque le basculement a été déclenché.
**Dernier point traité**  | Cette option bascule les machines virtuelles vers le dernier point de récupération traité par Site Recovery. Pour voir le dernier point de récupération d’une machine virtuelle spécifique, cochez **Derniers points de récupération** dans les paramètres de la machine virtuelle. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
**Dernier point de cohérence des applications** |  Cette option bascule toutes les machines virtuelles du plan vers le dernier point de récupération de cohérence des applications traité par Site Recovery. Vérifiez le dernier point de récupération dans les paramètres de la machine virtuelle.
**Dernier point multimachine virtuelle traité** | cette option est disponible pour les plans de récupération qui comportent une ou plusieurs machines virtuelles dont la cohérence multimachine virtuelle est activée. Les machines virtuelles pour lesquelles ce paramètre est activé basculent vers le dernier point de récupération de cohérence multimachine virtuelle commun. Toutes les autres machines virtuelles du plan basculent vers le dernier point de récupération traité.
**Dernière cohérence des applications multimachines virtuelles** : |  cette option est disponible pour les plans de récupération qui comportent une ou plusieurs machines virtuelles dont la cohérence multimachine virtuelle est activée. Les machines virtuelles qui font partie d’un groupe de réplication basculent vers le dernier point de récupération de cohérence des applications multimachine virtuelle commun. Les autres machines virtuelles basculent vers leur dernier point de récupération de cohérence des applications.
**Personnalisée** | Utilisez cette option pour basculer une machine virtuelle spécifique à un point de récupération particulier dans le temps. Cette option n’est pas disponible pour les plans de récupération.

> [!NOTE]
> Les points de récupération ne peuvent pas être migrés vers un autre coffre Recovery Services.

## <a name="reprotectionplanned-failover"></a>Reprotection/basculement planifié

Après le basculement vers Azure, les machines virtuelles Azure répliquées sont dans un état non protégé.

- Pour effectuer une première étape de restauration vers votre site local, vous devez démarrer les machines virtuelles Azure répliquées en local. Le processus de reprotection dépend du type d’ordinateur sur lequel vous avez basculé.
- Après la réplication des machines d’Azure vers le site local, vous pouvez exécuter un basculement à partir d’Azure vers votre site local.
- Une fois les machines réexécutées en local, vous pouvez activer la réplication afin qu’elles soient répliquées vers Azure pour la récupération d’urgence.

**Le basculement planifié fonctionne comme suit** :

- Pour effectuer la restauration automatique localement, une machine virtuelle a besoin d’au moins un point de récupération pour pouvoir être restaurée. Dans un plan de récupération, toutes les machines virtuelles du plan ont besoin d’au moins un point de récupération.
- Puisqu’il s’agit d’une activité de basculement planifié, vous pouvez sélectionner le type de point de récupération vers lequel vous souhaitez effectuer une restauration automatique. Nous vous recommandons d’utiliser un point de cohérence en cas d’incident.
    - Il existe également une option de point de récupération cohérent d’application. Dans ce cas, une seule machine virtuelle est récupérée vers son dernier point de récupération cohérent d’application disponible. Pour un plan de récupération avec un groupe de réplication, chaque groupe de réplication est récupéré à son point de récupération disponible commun.
    - Les points de récupération de cohérence des applications peuvent être en retard, et il peut y avoir une perte de données.
- Lors du basculement d’Azure vers le site local, Site Recovery arrête les machines virtuelles Azure. Lorsque vous validez le basculement, Site Recovery supprime les machines virtuelles Azure restaurées automatiquement dans Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/reprotection physique/restauration automatique

Pour reprotéger et restaurer automatiquement les machines virtuelles et les serveurs physiques VMware à partir d’Azure vers un emplacement local, assurez-vous de disposer d’une appliance saine.

**Sélection de l’appliance**

- Vous pouvez sélectionner n’importe quelle appliance de réplication Azure Site Recovery inscrite dans un coffre pour la reprotéger localement. Vous n’avez pas besoin d’un serveur de traitement distinct dans Azure pour l’opération de reprotection et d’un serveur cible maître scale-out pour les machines virtuelles Linux.
- L’appliance de réplication ne nécessite pas de connexion ou de ports réseau supplémentaires (par rapport à la protection avant) pendant la restauration automatique. La même appliance peut être utilisée pour les protections avant et arrière, si son état est sain. Cela ne devrait pas impacter les performances des réplications.
- Assurez-vous que le magasin de données ou l’hôte sélectionné est celui sur lequel l’appliance est située et que l’appliance sélectionnée peut y accéder.
  > [!NOTE]
  > Le stockage vMotion de l’appliance de réplication n’est pas pris en charge après une opération de reprotection.


**Tâche de reprotection**

- S’il s’agit d’une nouvelle opération de reprotection, un nouveau compte de stockage de journal est automatiquement créé par défaut par Azure Site Recovery dans la région cible. Le disque de rétention n’est pas requis.
- En cas de récupération à un autre emplacement et de récupération à l’emplacement d’origine, les configurations d’origine des machines sources sont récupérées.
  > [!NOTE]
  > - L’adresse IP statique ne peut pas être conservée en cas de reprotection à un autre emplacement (ALR) ou de reprotection à l’emplacement d’origine (OLR).
  > - fstab, LVMconf serait modifié.


**Échec**

- Toute tâche de reprotection ayant échoué peut être retentée. Lors de la nouvelle tentative, vous pouvez choisir une appliance de réplication saine.

Lorsque vous reprotégez des machines vers un emplacement local, vous êtes informé que vous effectuez à l’emplacement d’origine ou à un autre emplacement.

- **Récupération d’emplacement d’origine** : cette opération entraîne le basculement d’Azure vers la même machine locale source, si elle existe. Dans ce scénario, seules les modifications sont répliquées vers le serveur local.
  - **Sélection du magasin de données lors de la reprotection à l’emplacement d’origine** : le magasin de données attaché à la machine source est automatiquement sélectionné.
- **Récupération d’autre emplacement** : si la machine locale n’existe pas, vous pouvez effectuer une restauration automatique à partir d’Azure vers un autre emplacement. Lorsque vous reprotégez la machine virtuelle Azure en local, la machine locale est créée. La réplication complète des données s’effectue à partir d’Azure vers le site local. [Passez en revue](concepts-types-of-failback.md) les exigences et limitations relatives à la restauration de l’emplacement.
  - **Sélection du magasin de données lors de la reprotection à un autre emplacement** : tout magasin de données géré par vCenter sur lequel l’appliance est située et qui est accessible (autorisations de lecture et d’écriture) par l’appliance peut être choisi (original/nouveau). Vous pouvez choisir le compte de stockage de cache utilisé pour la reprotection.

- Une fois le basculement terminé, l’agent de mobilité de la machine virtuelle Azure est inscrit automatiquement aux services Site Recovery. En cas d’échec de l’inscription, un problème d’intégrité critique est déclenché sur la machine virtuelle basculée. Une fois le problème résolu, l’inscription est automatiquement déclenchée. Après avoir résolu les problèmes, vous pouvez terminer l’inscription manuellement.


## <a name="cancel-failover"></a>Annuler le basculement

Si votre environnement local n’est pas prêt ou si vous rencontrez des difficultés, vous pouvez annuler le basculement.

Une fois le basculement planifié lancé et terminé correctement, votre environnement local devient disponible pour utilisation. Cependant, une fois l’opération terminée, vous pouvez annuler le basculement si vous souhaitez basculer vers un autre point de récupération.


- Seul le basculement planifié peut être annulé.

- Vous pouvez annuler un basculement planifié depuis la page **Éléments répliqués** de votre coffre Recovery Services.

- Une fois le basculement annulé, vos machines Azure sont réactivées et la réplication commence à nouveau depuis Azure vers un emplacement local.


## <a name="next-steps"></a>Étapes suivantes
- [Basculement de machines virtuelles VMware vers Azure (préversion)](vmware-azure-tutorial-failover-failback-preview.md#run-a-failover-to-azure)
- [Basculement planifié (préversion)](vmware-azure-tutorial-failover-failback-preview.md#planned-failover-from-azure-to-on-premises)
- [Reprotéger (préversion)](vmware-azure-tutorial-failover-failback-preview.md#re-protect-the-on-premises-machine-to-azure-after-successful-planned-failover)
- [Annuler le basculement (préversion)](vmware-azure-tutorial-failover-failback-preview.md#cancel-planned-failover)
