---
title: 'Questions courantes : Reprise d’activité après sinistre au sein d’Azure avec Azure Site Recovery | Microsoft Docs'
description: Cet article récapitule les questions courantes concernant la configuration de la reprise d’activité après sinistre de machines virtuelles Azure dans une autre région Azure avec Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 20311f904356f16b34f64d0aaf6ed438ba692857
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155148"
---
# <a name="common-questions-azure-to-azure-replication"></a>Questions courantes : Réplication Azure vers Azure

Cet article fournit des réponses aux questions courantes concernant le déploiement de la reprise d’activité après sinistre de machines virtuelles Azure dans une autre région Azure avec Azure Site Recovery. Si, après avoir lu cet article, vous avez des questions, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Généralités
### <a name="how-is-site-recovery-priced"></a>Comment les tarifs Azure Site Recovery sont-ils fixés ?
Pour plus d’informations, consultez [Tarification Site Recovery ](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="what-are-the-best-practices-for-configuring-site-recovery-on-azure-vms"></a>Quelles sont les meilleures pratiques pour la configuration de Site Recovery sur des machines virtuelles Azure ?
1. [Comprendre l’architecture Azure vers Azure](azure-to-azure-architecture.md)
2. [Examiner les configurations prises en charge et non prises en charge](azure-to-azure-support-matrix.md)
3. [Configurer la récupération d’urgence pour les machines virtuelles Azure](azure-to-azure-how-to-enable-replication.md)
4. [Exécuter un test de basculement](azure-to-azure-tutorial-dr-drill.md)
5. [Effectuer une restauration automatique et un basculement vers la région primaire](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Réplication

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Puis-je répliquer des machines virtuelles activées via le chiffrement de disque Azure ?
Oui, vous pouvez les répliquer. Consultez l’article [Répliquer des machines virtuelles prenant en charge Azure Disk Encryption vers une autre région Azure](azure-to-azure-how-to-enable-replication-ade-vms.md). Actuellement, Azure Site Recovery prend uniquement en charge des machines virtuelles Azure exécutant le système d’exploitation Windows et pour lesquelles le chiffrement est activé avec l’application Azure Active Directory (Azure AD).

### <a name="can-i-replicate-vms-to-another-subscription"></a>Puis-je répliquer des machines virtuelles vers un autre abonnement ?
Oui, vous pouvez répliquer des machines virtuelles Azure vers un autre abonnement du même locataire Azure AD.
Configuration d’une récupération d’urgence [entre abonnements](https://azure.microsoft.com/blog/cross-subscription-dr) est simple. Vous pouvez sélectionner un autre abonnement au moment de la réplication.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Puis-je répliquer des machines virtuelles Azure épinglées à une zone vers une autre région ?
Oui, vous pouvez [répliquer des machines virtuelles épinglées à une zone](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) vers une autre région.

### <a name="can-i-exclude-disks"></a>Puis-je exclure des disques ?

Oui, vous pouvez exclure des disques au moment de la protection à l’aide de PowerShell. Pour plus d’informations, consultez la page [Conseils PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).

### <a name="how-often-can-i-replicate-to-azure"></a>À quelle fréquence puis-je répliquer vers Azure ?
La réplication de machines virtuelles Azure vers une autre région Azure est continue. Pour plus d’informations, consultez [Architecture de réplication Azure vers Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Puis-je répliquer des machines virtuelles au sein d’une même région ? J’ai besoin de cette fonctionnalité pour migrer des machines virtuelles.
Vous ne pouvez pas utiliser une solution de reprise d’activité après sinistre d’Azure vers Azure pour répliquer des machines virtuelles au sein d’une même région.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Puis-je répliquer des machines virtuelles vers toute région Azure ?
Site Recovery vous permet de répliquer et récupérer des machines virtuelles entre deux régions quelconques au sein du même cluster géographique. Les clusters géographiques sont définis en gardant à l’esprit la souveraineté et la latence des données. Pour plus d’informations, consultez le [tableau de prise en charge régionale](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) de Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery nécessite-t-il une connexion Internet ?

Non. Site Recovery ne nécessite pas de connexion Internet mais un accès aux URL Site Recovery et aux plages d’adresses IP, comme indiqué dans cet [article](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="replication-policy"></a>Stratégie de réplication

### <a name="what-is-a-replication-policy"></a>Qu’est-ce qu’une stratégie de réplication ?
Cette stratégie définit les paramètres de l’historique de conservation des points de récupération et la fréquence des captures instantanées cohérentes au niveau application. Par défaut, Azure Site Recovery crée une nouvelle stratégie de réplication avec les paramètres par défaut suivants :

* 24 heures pour l’historique de rétention des points de récupération.
* 60 minutes pour la fréquence des captures instantanées cohérentes au niveau application.

[Plus d’informations](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)

### <a name="what-is-a-crash-consistent-recovery-point"></a>Qu’est-ce qu’un point de récupération cohérent en cas d’incident ?
Un point de récupération cohérent en cas d’incident représente les données sur disque comme si la machine virtuelle était tombée en panne ou si le cordon d’alimentation avait été débranché du serveur au moment de la prise de la capture instantanée. Il n’inclut rien de ce qui était en mémoire lors de la prise de l’instantané. 

Aujourd’hui, la plupart des applications peuvent récupérer correctement à partir de captures instantanées cohérentes en cas d’incident. Un point de récupération cohérent en cas d’incident ne suffit généralement pas pour des systèmes d’exploitation de base de données et des applications telles que des serveurs de fichiers, des serveurs DHCP et des serveurs d’impression.

### <a name="what-is-an-application-consistent-recovery-point"></a>Qu’est-ce qu’un point de récupération cohérent au niveau application ? 
Les points de récupération cohérents au niveau application sont créés à partir de captures instantanées cohérentes au niveau application. Des points de récupération cohérents au niveau application capturent les mêmes données que des captures instantanées cohérentes en cas d’incident, avec l’ajout de toutes les données en mémoire et de toutes les transactions en cours. 

En raison de leur contenu supplémentaire, les instantanés de cohérence d’application sont davantage impliqués et leur exécution prend plus de temps. Les points de récupération cohérent au niveau application sont recommandés pour des systèmes d’exploitation de base de données et des applications telles que SQL Server.

### <a name="how-are-recovery-points-generated-and-saved"></a>Comment les points de récupération sont-ils générés et enregistrés ?
Pour comprendre comment Site Recovery génère des points de récupération, prenons l’exemple de la stratégie de réplication qui a une fenêtre de rétention de point de récupération de 24 heures et une fréquence de capture instantanée de cohérence au niveau application de 1 heure.

Site Recovery crée un point de récupération cohérent en cas d’incident toutes les 5 minutes. L’utilisateur ne peut pas modifier cette fréquence. Par conséquent, pour la dernière heure, l’utilisateur a le choix entre 12 points cohérents en cas d’incident et 1 point cohérent au niveau application. Au fil du temps, pour la période antérieure à la dernière heure, Site Recovery n’enregistre qu’un seul point de récupération par heure et écarte tous les autres.

La capture d’écran suivante illustre cet exemple. Dans la capture d’écran :

1. Pour la dernière heure, il existe des points de récupération qui ont été capturés toutes les 5 minutes.
2. Pendant la durée au-delà de la dernière heure, Site Recovery conserve uniquement 1 point de récupération.

  ![Liste des points de récupération générés](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jusqu’à quand peut remonter la récupération ?
Le point de récupération le plus ancien que vous pouvez utiliser remonte à 72 heures.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-pruned"></a>Que se passe-t-il si j’ai une stratégie de réplication de 24 heures et qu’un problème empêche la récupération Site Recovery de générer des points de récupération pendant plus de 24 heures ? Mes points de récupération antérieurs sont-ils écartés ?
Non, dans ce cas, Site Recovery conserve tous vos points de récupération antérieurs. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Une fois la réplication activée sur une machine virtuelle, comment modifier la stratégie de réplication ? 
Accédez à **Coffre Site Recovery** > **Infrastructure Site Recovery** > **Stratégies de réplication**. Sélectionnez la stratégie à modifier, modifiez-la, puis enregistrez les modifications. Toute modification s’applique également à toutes les réplications existantes. 

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tous les points de récupération sont-ils une copie complète ou différentielle de la machine virtuelle ?
Le premier point de récupération qui est généré possède la copie complète. Les points de récupération successifs ont des modifications d’ordre différentiel.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>L’accroissement de la période de rétention des points de récupération augmente-t-elle le coût de stockage ?
Oui. Si vous augmentez la période de rétention de 24 à 72 heures, Site Recovery enregistre les points de récupération pendant 48 heures supplémentaires. Cette durée supplémentaire occasionne des frais de stockage. Par exemple, si un point de récupération unique présente 10 Go de modifications d’ordre différentiel dont le coût par Go est de 0,16 USD par mois, les frais supplémentaires s’élèvent à 1,6 $ * 48 par mois.

## <a name="multi-vm-consistency"></a>Cohérence multimachine virtuelle 

### <a name="what-is-multi-vm-consistency"></a>Définition de la cohérence multimachine virtuelle
Cette fonctionnalité permet de s’assurer que le point de récupération est cohérent dans toutes les machines virtuelles répliquées.
Site Recovery fournit l’option Cohérence multimachine virtuelle, qui, lorsque vous la sélectionnez, crée un groupe de réplication pour répliquer ensemble toutes les machines qui font partie du groupe.
Toutes les machines virtuelles ont des points de récupération cohérents après incident et cohérents au niveau application lorsqu’elles basculent.
Consultez le tutoriel pour [activer la cohérence multimachine virtuelle](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Puis-je basculer une seule machine virtuelle au sein d’un groupe de réplication avec cohérence multimachine virtuelle ?
En sélectionnant l’option Cohérence multimachine virtuelle, vous indiquez que l’application a une dépendance sur toutes les machines virtuelles au sein d’un groupe. Par conséquent, le basculement d’une machine virtuelle unique n’est pas autorisé. 

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Combien de machines virtuelles puis-je répliquer dans un groupe de réplication avec cohérence multimachine virtuelle ?
Vous pouvez répliquer 16 machines virtuelles ensemble dans un groupe de réplication.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Quand dois-je activer la cohérence multimachine virtuelle ?
Comme il s’agit d’une opération gourmande en ressources, l’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail. Il convient de l’utiliser uniquement si les machines exécutent la même charge de travail et si vous avez besoin de cohérence sur plusieurs machines virtuelles. Par exemple, si vous avez deux instances de SQL Server et deux serveurs web dans une application, vous devez avoir la cohérence multimachine virtuelle pour les instances SQL Server uniquement.


## <a name="failover"></a>Basculement

### <a name="is-failover-automatic"></a>Le basculement est-il automatique ?

Le basculement n’est pas automatique. Vous lancez les basculements d’un seul clic dans le portail ou en utilisant [PowerShell](azure-to-azure-powershell.md). 

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Puis-je conserver l’adresse IP publique après basculement ?

L’adresse IP publique de l’application de production *ne peut pas être conservée lors du basculement*. Les charges de travail montées dans le cadre du processus de basculement doivent être affectées à une ressource d’adresse IP publique Azure disponible dans la région cible. Vous pouvez effectuer cette étape manuellement ou l’automatiser via un plan de récupération. Pour affecter une adresse IP publique à l’aide d’un plan de récupération, consultez [Configurer des adresses IP publiques après le basculement](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Puis-je conserver l’adresse IP privée pendant le basculement ?
Oui, vous pouvez conserver l’adresse IP privée. Par défaut, quand vous activez la reprise d’activité après sinistre pour les machines virtuelles Azure, Site Recovery crée des ressources cibles en fonction des paramètres de ressources sources. Pour les machines virtuelles Azure configurées avec des adresses IP statiques, Site Recovery tente de provisionner la même adresse IP pour la machine virtuelle cible, si elle n’est pas en cours d’utilisation. Pour conserver une adresse IP privée dans différentes conditions, consultez [Conserver des adresses IP pendant le basculement](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-doesnt-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-a-new-ip-address"></a>Après basculement, le serveur n’a plus la même adresse IP que la machine virtuelle source. Pourquoi une nouvelle adresse IP lui est-elle attribuée ?

Site Recovery tente de fournir l’adresse IP au moment du basculement. Si une autre machine virtuelle prend cette adresse, Site Recovery définit l’adresse IP suivante disponible comme cible. Pour obtenir une explication complète de la façon dont Site Recovery gère l’adressage, consultez [Configurer le mappage réseau et l’adressage IP pour les réseaux virtuels](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Que sont les points de récupération **Dernier (objectif de point de récupération le plus faible)**  ?
L’option **Dernier (objectif de point de récupération le plus faible)** permet de traiter d’abord toutes les données qui ont été envoyées au service Site Recovery afin de créer un point de récupération pour chaque machine virtuelle avant de basculer les machines virtuelles vers celui-ci. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle créée après le basculement comporte toutes les données répliquées vers Site Recovery au moment où le basculement a été déclenché.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>L’option **Dernier (objectif de point de récupération le plus faible)** a-t-elle un impact sur l’objectif de délai de récupération (RTO) de basculement ?
Oui. Site Recovery traite toutes les données en attente avant le basculement. Par conséquent, cette option a un objectif de délai de récupération supérieur à d’autres options.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Que signifie l’option **Dernier point traité** dans les points de récupération ?
L’option **Dernier point traité** bascule toutes les machines virtuelles du plan vers le dernier point de récupération traité par Site Recovery. Pour voir le dernier point de récupération d’une machine virtuelle spécifique, cochez **Derniers points de récupération** dans les paramètres de la machine virtuelle. Cette option fournit un objectif de délai de récupération faible, car aucun temps n’est consacré à traiter les données non traitées.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Si j’effectue une réplication entre deux régions Azure, que se passe-t-il si ma région primaire subit une panne inattendue ?
Vous pouvez déclencher un basculement après la panne. Site Recovery n’a pas besoin de connectivité de la région primaire pour opérer le basculement.

## <a name="recovery-plan"></a>Plan de récupération

### <a name="what-is-a-recovery-plan"></a>Qu’est-ce qu’un plan de récupération ?
Les plans de récupération dans Site Recovery orchestrent la récupération par basculement des machines virtuelles. Ils aident également à rendre la récupération toujours précise, répétable et automatisée. Un plan de récupération répond aux besoins suivants de l’utilisateur :

- Définition d’un groupe de machines virtuelles qui basculent ensemble
- Définition des dépendances entre les machines virtuelles afin que l’application opère avec précision
- Automatisation de la récupération en même temps qu’actions manuelles personnalisées afin que des tâches autres que le basculement des machines virtuelles puissent également être accomplies.

[Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Comment le séquencement est-il effectué dans un plan de récupération ?

Dans un plan de récupération, vous pouvez créer plusieurs groupes pour effectuer le séquencement. Le basculement intervient groupe après groupe. Cela signifie que les machines virtuelles d’un même groupe basculent ensemble, suivies de celles d’un autre groupe. Pour savoir comment modéliser une application à l’aide d’un plan de récupération, consultez [À propos des plans de récupération](recovery-plan-overview.md#model-apps). 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Comment trouver l’objectif de délai de récupération d’un plan de récupération ?
Pour vérifier l’objectif de délai de récupération d’un plan de récupération, testez le basculement du plan de récupération, puis accédez aux **travaux Site Recovery**.
Dans l’exemple suivant, le travail nommé SAPTestRecoveryPlan a mis 8 minutes et 59 secondes pour basculer toutes les machines virtuelles et effectuer les actions spécifiées.

![Liste des travaux Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Puis-je ajouter des runbooks Automation au plan de récupération ?
Oui, vous pouvez intégrer des runbooks Azure Automation dans votre plan de récupération. [Plus d’informations](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Reprotection et restauration automatique 

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Après basculement de la région primaire vers la région de reprise d’activité après sinistre, les machines virtuelles situées dans celle-ci sont-elles protégées automatiquement ?
 Non. Non, lorsque vous [basculez](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) des machines virtuelles Azure d’une région vers une autre, les machines virtuelles démarrent dans la région de reprise d’activité après sinistre en état non protégé. Pour restaurer automatiquement les machines virtuelles vers la région primaire, vous devez [reprotéger](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) les machines virtuelles dans la région secondaire.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Au moment de la reprotection, Site Recovery réplique-t-il la totalité des données de la région secondaire vers la région primaire ?
Cela dépend de la situation. Par exemple, si la machine virtuelle de la région source existe, seuls les différences entre le disque source et le disque cible sont synchronisées. Site Recovery calcule les différentiels en comparant les disques, puis transfère les données. Ce processus prend généralement plusieurs heures. Pour plus d’informations sur ce qui se passe lors de la reprotection, consultez [Reprotection de machines virtuelles Azure basculées vers la région primaire]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Combien de temps prend la restauration automatique ?
Après la reprotection, la durée de la restauration automatique est généralement similaire à la durée du basculement de la région primaire vers une région secondaire. 

## <a name="security"></a>Sécurité
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Les données de réplication sont-elles envoyées vers le service Site Recovery ?
Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les opérations exécutées sur vos machines virtuelles. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.  
Le logiciel Site Recovery est certifié conforme aux normes ISO 27001:2013, 27018, HIPAA et DPA. Il fait actuellement l’objet d’une évaluation de conformité aux exigences SOC2 et JAB FedRAMP.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery chiffre-t-il la réplication ?
Oui, le chiffrement en transit et le [chiffrement dans Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) sont tous deux pris en charge.

## <a name="next-steps"></a>Étapes suivantes
* [Examiner](azure-to-azure-support-matrix.md) les conditions de prise en charge.
* [Configurer](azure-to-azure-tutorial-enable-replication.md) la réplication d’Azure vers Azure.
