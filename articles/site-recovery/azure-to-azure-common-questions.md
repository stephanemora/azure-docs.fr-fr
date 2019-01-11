---
title: Questions courantes sur la récupération d’urgence d’Azure sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article récapitule les questions courantes concernant la configuration de la récupération d’urgence de machines virtuelles Azure dans une autre région Azure avec Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969945"
---
# <a name="common-questions---azure-to-azure-replication"></a>Questions courantes sur la réplication d’Azure vers Azure

Cet article fournit des réponses aux questions couramment posées concernant le déploiement d’une récupération d’urgence de machines virtuelles Azure dans une autre région Azure. Si, après avoir lu cet article, vous avez des questions, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Généralités
### <a name="how-is-site-recovery-priced"></a>Comment les tarifs Azure Site Recovery sont-ils fixés ?
Pour plus d’informations, consultez [Tarification Site Recovery ](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Comment configurer Site Recovery sur des machines virtuelles Azure. Quelles sont les meilleures pratiques ?
1. [Comprendre l’architecture Azure vers Azure](azure-to-azure-architecture.md)
2. [Examiner les configurations prises en charge et non prises en charge](azure-to-azure-support-matrix.md)
3. [Configurer la récupération d’urgence pour les machines virtuelles Azure](azure-to-azure-how-to-enable-replication.md)
4. [Exécuter un test de basculement](azure-to-azure-tutorial-dr-drill.md)
5. [Basculer et restaurer automatiquement vers la région primaire](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Réplication

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Puis-je répliquer des machines virtuelles pour lesquelles le chiffrement de disque Azure est activé ?
Oui, vous pouvez les répliquer. Pour activer la réplication de machines virtuelles pour lesquelles le chiffrement de disque Azure (ADE) est activé, voir cet [article](azure-to-azure-how-to-enable-replication-ade-vms.md). Veuillez noter que seules des machines virtuelles Azure exécutant le système d’exploitation Windows et pour lesquelles le chiffrement avec l’application Azure AD est activé sont actuellement prises en charge par Azure Site Recovery.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Puis-je répliquer des machines virtuelles vers un autre abonnement ?
Oui, vous pouvez répliquer des machines virtuelles Azure vers un autre abonnement du même locataire Azure Active Directory.
Configuration d’une récupération d’urgence [entre abonnements](https://azure.microsoft.com/blog/cross-subscription-dr) est simple. Vous pouvez sélectionner un autre abonnement au moment de la réplication.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Puis-je répliquer des machines virtuelles Azure épinglées à une zone vers une autre région ?
Oui, vous pouvez [répliquer des machines virtuelles épinglées à une zone](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) vers une autre région.

### <a name="can-i-exclude-disks"></a>Puis-je exclure des disques ?

Oui, vous pouvez exclure des disques au moment de la protection à l’aide de PowerShell. Pour savoir comment exclure un disque, voir l’[Aide de PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).

###<a name="how-often-can-i-replicate-to-azure"></a>À quelle fréquence puis-je répliquer vers Azure ?
La réplication de machines virtuelles Azure vers une autre région Azure est continue. Examinez l’architecture de réplication d’[Azure vers Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) pour comprendre les détails.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>Puis-je répliquer des machines virtuelles au sein d’une même région ? Ai-je besoin de cela pour migrer des machines virtuelles ?
Vous ne pouvez pas utiliser une solution de récupération d’urgence d’Azure vers Azure pour répliquer des machines virtuelles au sein d’une même région.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Puis-je répliquer des machines virtuelles vers toute région Azure ?
Site Recovery vous permet de répliquer et récupérer des machines virtuelles entre deux régions quelconques au sein du même cluster géographique. Les clusters géographiques sont définis en gardant à l’esprit la en souveraineté et la latence des données. Pour plus d’informations, voir la [matrice de prise en charge régionale](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) de Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery nécessite-t-il une connexion Internet ?

Non, Site Recovery ne nécessite pas de connexion Internet mais un accès aux URL de Site Recovery et aux plages d’adresses IP, comme indiqué dans cet [article](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

## <a name="replication-policy"></a>Stratégie de réplication

### <a name="what-is-a-replication-policy"></a>Qu’est-ce qu’une stratégie de réplication ?
définit les paramètres de l’historique de conservation des points de récupération et la fréquence des captures instantanées de cohérence des applications. Par défaut, Azure Site Recovery crée une stratégie de réplication avec les paramètres par défaut de « 24 heures » pour la rétention des points de récupération, et de « 60 minutes » pour la fréquence des captures instantanées de cohérence des applications.

### <a name="what-is-crash-consistent-recovery-point"></a>Qu’est-ce qu’un point de récupération cohérent d’incident ?
Un point de récupération cohérent d’incident représente les données sur disque comme si la machine virtuelle était tombée en panne ou si le cordon d’alimentation avait été débranché du serveur au moment de la prise de l’instantané. Il n’inclut rien de ce qui était en mémoire lors de la prise de l’instantané. Aujourd’hui, la plupart des applications peuvent récupérer correctement à partir d’instantanés de cohérence d’incident. Un point de récupération cohérent d’incident ne suffit généralement pas pour des systèmes d’exploitation de base de données et des applications telles que des serveurs de fichiers, des serveurs DHCP, des serveurs d’impression, etc.

### <a name="what-is-application-consistent-recovery-point"></a>Qu’est-ce qu’un point de récupération cohérent d’application ? 
Des points de récupération cohérents d’application sont créés à partir d’instantanés de cohérence d’application qui capturent les mêmes données que des instantanés de cohérence d’incident, avec l’ajout de toutes les données en mémoire et de toutes les transactions en cours. En raison de leur contenu supplémentaire, les instantanés de cohérence d’application sont davantage impliqués et leur exécution prend plus de temps. Des points de récupération cohérent d’application sont recommandés pour des systèmes d’exploitation de base de données et des applications telles que SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Comment les points de récupération sont-ils générés et enregistrés ?
Pour comprendre comment Site Recovery génère des points de récupération, prenons l’exemple de la stratégie de réplication qui a une fenêtre de rétention de point de récupération de 24 heures et une fréquence de capture instantanée de cohérence d’application de 1 heure.
Site Recovery crée un point cohérent d’incident toutes les 5 minutes, et l’utilisateur ne peut pas modifier cette fréquence. Par conséquent, pour la dernière heure, l’utilisateur aura le choix entre 12 points cohérents d’incident et 1 point de cohérence d’application. Au fil du temps, pour la période antérieure à la dernière heure, Site Recovery n’enregistre qu’un seul point de récupération par heure et écarte tous les autres.
À titre d’illustration, dans la capture d’écran ci-dessous :


1. Pour la dernière heure, il existe des points de récupération qui ont été capturés toutes les 5 minutes.
2. Pour la période antérieure à la dernière heure, nous pouvons voir qu’un seul point de récupération par heure a été conservé.

  ![génération de points de récupération](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jusqu’à quand peut remonter la récupération ?
Le point de récupération le plus ancien que vous pouvez utiliser remonte à 72 heures.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>Que se passe-t-il si j’ai une stratégie de réplication de 24 heures et qu’il n’y a pas de génération de points de récupération en raison d’un problème quelconque pendant plus de 24 heures ? Mes points de récupération antérieurs sont-ils écartés ?
Non, dans ce cas, Site Recovery conserve tous vos points de récupération antérieurs. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Une fois la réplication activée sur une machine virtuelle, comment modifier la stratégie de réplication ? 
Accédez à Coffre Site Recovery > Infrastructure Site Recovery > Stratégies de réplication. Sélectionnez la stratégie à modifier, modifiez-la, puis enregistrez les modifications. Toute modification s’applique également à toutes les réplications existantes. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>Tous les points de récupération sont-ils une copie complète ou différentielle de la machine virtuelle ?
En cas de réplication initiale, le premier point de récupération généré est la copie complète, et les points de récupération subséquents intègrent les modifications d’ordre différentiel.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>L’augmentation des fenêtres de rétention des points de récupération augmente-t-elle le coût de stockage ?
Oui, si vous augmentez la période de rétention de 24 à 72 heures, Site Recovery enregistre les points de récupération pendant 48 heures supplémentaires, ce qui occasionne des frais de stockage. Par exemple, si un point de récupération unique présente 10 Go de modifications d’ordre différentiel dont le coût par Go est de 0,16 USD par mois, les frais supplémentaires s’élèvent à 1,6 * 48 par mois.

## <a name="failover"></a>Basculement

### <a name="is-failover-automatic"></a>Le basculement est-il automatique ?

Le basculement n’est pas automatique. Vous opérez les basculements d’un simple clic dans le portail. Vous pouvez également utiliser [Site Recovery PowerShell](azure-to-azure-powershell.md) pour déclencher un basculement. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>Puis-je conserver l’adresse IP publique après basculement ?

L’adresse IP publique de l’application de production **ne peut pas être conservée lors du basculement**. Les charges de travail montées dans le cadre du processus de basculement doivent être affectées à une ressource d’adresse IP publique Azure disponible dans la région cible. Cette étape peut être effectuée manuellement, ou automatiquement avec des plans de récupération. Pour savoir comment affecter l’adresse IP publique à l’aide d’un plan de récupération, voir cet [article](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-private-ip-address-during-failover"></a>Puis-je conserver l’adresse IP privée pendant le basculement ?
Oui, vous pouvez conserver l’adresse IP privée. Par défaut, quand vous activez la reprise d’activité pour les machines virtuelles Azure, Site Recovery crée des ressources cibles en fonction des paramètres de ressources sources. Pour les machines virtuelles Azure configurées avec des adresses IP statiques, Site Recovery tente de provisionner la même adresse IP pour la machine virtuelle cible, si elle n’est pas en cours d’utilisation. Pour savoir comment conserver l’adresse IP privée dans différentes conditions, voir cet [article](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>Après basculement, le serveur n’a plus la même adresse IP que la machine virtuelle source. Pourquoi une nouvelle adresse IP lui est-elle attribuée ?

Site Recovery tente de fournir l’adresse IP dans la meilleure mesure possible au moment du basculement. Si l’adresse IO est prise par une autre machine virtuelle, l’adresse IP disponible suivante est définie comme cible. Pour une explication complète de la façon dont Site Recovery gère l’adressage, [voir cet article](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>Que signifie l’option Derniers points de récupération (objectif de point de récupération le plus faible) ?
Cette option permet de traiter d’abord toutes les données qui ont été envoyées au service Site Recovery afin de créer un point de récupération pour chaque machine virtuelle avant de basculer vers celui-ci. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle créée après le basculement comporte toutes les données répliquées vers Site Recovery au moment où le basculement a été déclenché.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>L’option Derniers points de récupération (objectif de point de récupération le plus faible) a-t-elle un impact sur l’objectif de délai de récupération (RTO) de basculement ?
Oui, comme Site Recovery traite toutes les données en attente avant le basculement, cette option aura un objectif de délai de récupération supérieur à d’autres.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>Que signifie l’option Dernier point traité dans les points de récupération ?
Cette option bascule toutes les machines virtuelles du plan vers le dernier point de récupération traité par Site Recovery. Pour voir le dernier point de récupération d’une machine virtuelle spécifique, activez Derniers points de récupération dans les paramètres de la machine virtuelle. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Si j’effectue une réplication entre deux régions Azure, que se passe-t-il si ma région primaire subit une panne inattendue ?
Vous pouvez déclencher un basculement après la panne. Site Recovery n’a pas besoin de connectivité de la région primaire pour opérer le basculement.

## <a name="recovery-plan"></a>Plan de récupération

### <a name="what-is-a-recovery-plan-"></a>Qu’est-ce qu’un plan de récupération ?
Les plans de récupération dans Site Recovery orchestrent la récupération par basculement des machines virtuelles. Ils aident également à rendre la récupération toujours précise, répétable et automatisée. Un plan de récupération répond aux besoins suivants de l’utilisateur :

- Définition d’un groupe de virtuel de machines qui basculent ensemble.
- Définition des dépendances entre les machines virtuelles afin que l’application opère avec précision.
- Automatisation de la récupération en même temps qu’actions manuelles personnalisées afin que des tâches autres que le basculement des machines virtuelles puissent également être accomplies.

[Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Comment le séquencement est-il effectué dans un plan de récupération ?

Dans un plan de récupération, vous pouvez créer plusieurs groupes pour effectuer le séquencement. Le basculement intervient groupe après groupe, ce qui signifie que les machines virtuelles d’un même groupe basculent ensemble, suivies de celles d’un autre groupe. Voyez comment [modéliser une application à l’aide du plan de récupération](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps). 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Comment trouver l’objectif de délai de récupération d’un plan de récupération ?
Pour vérifier l’objectif de délai de récupération d’un plan de récupération, testez le basculement du plan de récupération, puis accédez aux tâches de Site Recovery.
Par exemple, comme illustré ci-dessous, un plan de récupération test de SAP a pris 8 minutes 59 secondes pour basculer toutes les machines virtuelles et effectuer les actions spécifiées.

  ![com-error](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Puis-je ajouter des runbooks Automation au plan de récupération ?
Oui, vous pouvez intégrer des runbooks Azure Automation dans votre plan de récupération. [En savoir plus](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Reprotéger et restaurer automatiquement 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>Après basculement de la région primaire vers la région de récupération d’urgence, les machines virtuelles situées dans celle-ci sont-elles protégées automatiquement ?
Non, lorsque vous [basculez](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) des machines virtuelles Azure d’une région vers une autre, les machines virtuelles démarrent dans la région de récupération d’urgence en état non protégé. Pour restaurer automatiquement les machines virtuelles vers la région primaire, vous devez [reprotéger](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) les machines virtuelles dans la région secondaire.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>Au moment de la reprotection, Site Recovery réplique-t-il la totalité des données de la région secondaire vers la région primaire ?
Cela dépend de la situation. Par exemple, si la machine virtuelle de la région source existe, seuls les différences entre le disque source et le disque cible sont synchronisées. Les différences sont calculées en comparant les deux disques, puis transférées. Cette opération prend généralement quelques heures. Pour plus d’informations sur ce qui se passe lors de la reprotection, voir cet [article]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-failback"></a>Combien de temps prend la restauration automatique ?
Une fois la reprotection effectuée, la restauration automatique prend généralement le même temps que le basculement de la région primaire vers la région secondaire. 

## <a name="security"></a>Sécurité
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Les données de réplication sont-elles envoyées vers le service Site Recovery ?
Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les opérations exécutées sur vos machines virtuelles. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.  
Le logiciel Site Recovery est certifié conforme aux normes ISO 27001:2013, 27018, HIPAA et DPA. Il fait actuellement l’objet d’une évaluation de conformité aux exigences SOC2 et JAB FedRAMP.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery chiffre-t-il la réplication ?
Oui, le chiffrement en transit et le [chiffrement dans Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) sont tous deux pris en charge.

## <a name="next-steps"></a>Étapes suivantes
* [Examiner](azure-to-azure-support-matrix.md) les conditions de prise en charge.
* [Configurer](azure-to-azure-tutorial-enable-replication.md) la réplication d’Azure vers Azure.
