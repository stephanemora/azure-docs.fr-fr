---
title: Créer un plan de migration avec Azure Migrate | Microsoft Docs
description: Fournit des conseils sur la création d’un plan de migration avec Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: e80ec71bef8c34331fac076b7b08be48a9571c2e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107697"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Créer un plan de migration avec Azure Migrate

Cet article fournit un guide pratique pour vous aider à créer votre plan de migration vers Azure à l’aide d’[Azure Migrate](migrate-services-overview.md). Si vous avez d’autres questions, consultez les ressources suivantes :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- Questions sur l’[appliance Azure Migrate](common-questions-appliance.md)
- Questions sur la [migration de serveur](common-questions-server-migration.md)
- Obtenez des réponses à vos questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="define-the-goals-of-cloud-migration"></a>Définir les objectifs de la migration dans le cloud

Avant de créer un plan de migration, il est important de comprendre et d’évaluer la [motivation](/azure/cloud-adoption-framework/strategy/motivations) de migrer vers le cloud, ce qui peut contribuer à produire des résultats métier plus probants. Comme l’explique le [Framework d’adoption du cloud Microsoft pour Azure](/azure/cloud-adoption-framework), il peut exister des déclencheurs et des approches de migration différents adaptés à votre entreprise :  

**Événements commerciaux stratégiques** | **Résultat de la migration**
--- | ---
Sortie du centre de données | Réduction des coûts
Fusion, acquisition ou cession | Réduction de la complexité liée aux fournisseurs ou aux contraintes techniques
Réduction des dépenses d’investissement | Optimisation des opérations internes
Fin de la prise en charge de technologies stratégiques | Augmentation de l’agilité de l’entreprise
Réponse aux changements liés à la conformité réglementaire | Préparation à de nouvelles capacités techniques
Nouvelles exigences de souveraineté des données | Adaptation à de nouvelles demandes du marché
Réduction des interruptions et amélioration de la stabilité informatique | Adaptation à de nouvelles demandes géographiques

Votre motivation en faveur de la migration peut également vous aider à réfléchir aux objectifs stratégiques et aux résultats que vous souhaitez obtenir en migrant vers Azure. L’étape suivante consiste à identifier et à planifier un chemin de migration vers Azure, adapté à vos charges de travail. Azure Migrate : L’outil Server Assessment vous aide à évaluer les charges de travail locales, et il fournit des conseils et des outils pour vous aider à effectuer la migration.

## <a name="understand-your-digital-estate"></a>Comprendre votre patrimoine numérique

Commencez par comprendre votre infrastructure locale, vos applications et vos dépendances pour vous aider à identifier les charges de travail que vous souhaitez migrer vers Azure et à effectuer des projections de coûts optimisées. L’outil Server Assessment vous aidera à répondre aux questions suivantes :

### <a name="what-workloads-are-in-use"></a>Quelles sont les charges de travail en cours d’utilisation ?

Utilisez l’appliance légère Azure Migrate pour effectuer la découverte de vos machines virtuelles VMware locales, de vos machines virtuelles Hyper-V et de vos serveurs physiques sans recourir à un agent. La découverte continue collecte les métadonnées de configuration et de performances de l’ordinateur et peut également être utilisée pour obtenir l’inventaire des applications installées et des rôles/fonctionnalités exécutés sur vos machines locales. L’appliance Azure Migrate collecte les éléments suivants :

- Détails des métadonnées des machines, des disques et des cartes réseau

- Applications installées, notamment les applications et les rôles/fonctionnalités  

- Données de performances, notamment l’utilisation de l’UC et de la mémoire, les IOPS de disque et le débit

Ensuite, exportez la liste d’inventaire des applications pour découvrir toutes les instances SQL Server s’exécutant sur vos charges de travail et utilisez l’outil d’évaluation Azure Migrate : Database Assessment pour comprendre leur disponibilité.

 ![Inventaire des applications sur le Portail](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Exporter l’inventaire des applications](./media/concepts-migration-planning/application-inventory-export.png)

En plus des données de découverte de l’outil Server Assessment, utilisez vos données CMDB existantes pour créer l’affichage de votre patrimoine de serveurs et de bases de données et comprendre la répartition des serveurs entre les unités commerciales, les propriétaires d’applications, les zones géographiques, etc., qui peuvent à leur tour aider à hiérarchiser les charges de travail à migrer.

### <a name="what-dependencies-exist-between-workloads"></a>Quelles sont les dépendances qui existent entre les charges de travail ?

Après avoir découvert vos serveurs, utilisez le mappage de dépendance sans agent pour visualiser et identifier les dépendances entre serveurs et les stratégies d’optimisation pour déplacer des serveurs interdépendants vers Azure. La visualisation permet de déterminer si certaines machines sont en cours d’utilisation ou si elles peuvent être mises hors service au lieu d’être migrées.  Veillez à analyser les dépendances pour vous assurer que rien n’est laissé de côté et éviter les pannes inopinées durant la migration. Une fois que vous avez effectué l’inventaire des applications et le mappage des dépendances, vous pouvez créer des groupes à confiance élevée et commencer à évaluer vos serveurs.

 ![Mappage des dépendances](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Si oui, sont-ils correctement optimisés et dimensionnés ?

Étant donné qu’Azure offre la flexibilité nécessaire pour redimensionner votre capacité cloud au fil du temps, la migration vous permet d’optimiser les ressources processeur et mémoire allouées à vos serveurs. Créez une évaluation sur le groupe identifié avant de comprendre l’historique des performances des charges de travail, ce qui sera très important dans les références SKU correctement dimensionnées et les recommandations relatives aux disques sur Azure.

## <a name="assess-your-readiness-for-migration"></a>Évaluer votre niveau de préparation à la migration

### <a name="readiness-and-suitability-analysis-for-azure"></a>Analyse de la préparation et de la pertinence pour Azure
Exportez le rapport d’évaluation des machines virtuelles Azure et filtrez les catégories de disponibilité suivantes pour comprendre la préparation des machines virtuelles pour Azure :

- **Disponible pour Azure** : Vous pouvez migrer ces machines en l’état vers Azure sans aucune modification  

- **Préparé pour Azure sous condition**: Vous pouvez migrer ces machines vers Azure, mais vous aurez besoin de modifications mineures sur ces serveurs conformément aux instructions de correction fournies dans le cadre de l’évaluation

- **Non disponible pour Azure** : Vous ne pouvez pas migrer ces machines vers Azure en l’état et devez résoudre les problèmes en fonction des recommandations de correction avant la migration

- **État de la préparation inconnu** : Azure Migrate ne peut pas déterminer l’état de préparation d’une machine en raison de métadonnées insuffisantes

Grâce aux évaluations des bases de données, vous pouvez estimer l’état de préparation à la migration de votre patrimoine de données SQL Server vers la base de données Azure SQL ou les instances managées Azure SQL. Vous pouvez consulter le pourcentage d’état de préparation à la migration pour chacune de vos instances SQL Server. En outre, pour chacune des instances, vous pouvez voir la cible recommandée dans Azure, les bloqueurs de migration potentiels, le nombre de changements cassants, le niveau de préparation pour Azure SQL DB/Azure SQL VM et le niveau de compatibilité. Vous pouvez approfondir l’étude de l’impact des bloqueurs de migration et des recommandations pour les résoudre.

 ![Évaluations des bases de données](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Recommandations de dimensionnement

Une fois qu’une machine est marquée comme prête pour Azure, Server Assessment fait des recommandations de dimensionnement afin d’identifier la référence SKU de machine virtuelle Azure et de disque pour vos machines virtuelles. Vous pouvez choisir d’afficher la recommandation de dimensionnement en fonction de l’historique des performances (afin d’optimiser les ressources au fil de la migration) ou en fonction de leur configuration locale sans prendre en compte l’historique des performances. Pour les bases de données, vous pouvez consulter les recommandations relatives à la référence SKU de base de données, au niveau tarifaire et au niveau de calcul dans votre évaluation de base de données.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Évaluations de calcul pour obtenir les coûts estimés pour l’exécution des charges de travail dans Azure

L’option de dimensionnement correct *basée sur les performances* dans les évaluations vous permet d’optimiser les charges de travail pour Azure. Outre le dimensionnement correct, il existe peu d’autres moyens de réduire les coûts :

- **Instances réservées** : Avec les instances réservées, vous pouvez réduire considérablement les coûts par rapport au tarif de paiement à l’utilisation avec un délai de 1 an ou 3 ans sur Windows et Linux (machines virtuelles)

- **Avantages Azure Hybrid** : Vous pouvez placer les licences Windows Server sur site avec Software Assurance sur Azure et les combiner avec les options d’instances réservées

- **Offres Accord Entreprise (EA)**  : L’Accord Entreprise offre des économies intégrées applicables à votre abonnement

- **Offres** : Il existe plusieurs offres Azure, par exemple, Dev/Test - Paiement à l’utilisation et Enterprise Dev/Test dont les tarifs sont inférieurs pour les machines virtuelles de développement ou de test

- **Durée de fonctionnement des machines virtuelles** : vous pouvez mentionner la durée en jours par mois et en heures par jour pendant laquelle les machines virtuelles Azure seront exécutées pour réduire vos coûts (non applicable à RI)

- **Région cible** : Vous pouvez créer plusieurs évaluations dans des régions différente pour déterminer si la migration vers une certaine région dans une zone géographique peut être plus rentable

- **Recommandations basées sur les performances** : Nous vous recommandons d’essayer d’utiliser des recommandations de machine virtuelle Azure basées sur les performances afin de pouvoir réaliser des économies sur les accès au cloud

### <a name="visualize-data"></a>Visualiser les données

Vous pouvez voir le rapport d’évaluation du serveur avec le niveau de préparation Azure et la répartition mensuelle des coûts dans le portail, ainsi que l’exporter pour appliquer plus de visualisations sur les données de découverte et d’évaluation en vue d’enrichir le plan de migration. Vous pouvez créer plusieurs évaluations pour différentes combinaisons de propriétés et choisir l’ensemble de propriétés convenant le mieux à votre entreprise.  

 ![Vue d’ensemble des évaluations](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Évaluer les lacunes et les blocages potentiels

Tout en déterminant les applications et l’infrastructure sous-jacente à migrer, identifiez les contraintes de temps d’arrêt pour ces applications et recherchez les dépendances opérationnelles entre vos applications et l’infrastructure sous-jacente. Cette analyse peut vous aider à planifier des migrations qui répondent à votre objectif de délai de récupération (RTO) et à garantir une perte de données minime voire nulle. Avant de procéder à la migration, nous vous recommandons de passer en revue et d’atténuer les problèmes de compatibilité ou les fonctionnalités non prises en charge qui peuvent bloquer la migration de vos serveurs et bases de données SQL en vous aidant du rapport d’évaluation des serveurs et d’Azure Migrate : recommandations relatives à l’évaluation de base de données.

### <a name="first-workloads-to-target-and-approach"></a>Premières charges de travail à cibler et approcher

Maintenant que vous disposez de toutes les informations essentielles pour prendre votre décision de migration, vous devez définir la priorité des applications et des charges de travail qui doivent être migrées en premier. Développez une approche « appliquer et apprendre » pour migrer vos applications ciblées de manière systématique et contrôlable afin de pouvoir éliminer les failles de cette stratégie avant de lancer une migration à grande échelle. Vous pouvez également utiliser des facteurs stratégiques tels que la complexité et le temps de migration, l’urgence de l’entreprise, l’environnement de production/hors production, la conformité et les exigences de sécurité, la connaissance des applications, etc. pour hiérarchiser les groupes d’applications à migrer.

Voici quelques stratégies de migration recommandées :

- **Hiérarchisez vos actions à effet rapide** : Vous pouvez utiliser les rapports d’évaluation pour identifier les ressources à portée de main, notamment les serveurs et les bases de données qui sont entièrement prêts et nécessitent un effort minimal pour migrer vers Azure :
    - Azure Ready : Exportez votre rapport d’évaluation et filtrez tous les ordinateurs qui sont « Azure Ready » (« prêts pour Azure »). Il peut s’agir de votre premier groupe d’ordinateurs pour lequel vous pouvez effectuer une migration « lift-and-shift » avec l’outil de migration serveur d’Azure Migrate.
    - Fin du support du système d’exploitation : Exportez votre rapport d’évaluation et filtrez tous les ordinateurs qui exécutent les systèmes d’exploitation Windows Server 2008 et Windows Server 2008 R2. Le support de ces références SKU n’est plus assuré et seul Azure vous fournit des mises à jour de sécurité gratuites pendant trois ans lorsque vous les migrez vers Azure. Lorsque vous combinez Azure Hybrid Benefit aux instances réservées, les économies peuvent être bien plus élevées.
    - Migration de SQL Server : Utilisez les recommandations d’évaluation de base de données afin de migrer les bases de données prêtes pour les bases de données SQL Azure à l’aide d’Azure Migrate : Migration de base de données et bases de données prêtes pour la machine virtuelle Azure SQL à l’aide de l’outil Azure Migrate : Server Migration.
    - Fin du support logiciel : Exportez l’inventaire de votre application et filtrez les logiciels/extensions susceptibles d’atteindre la fin de leur prise en charge. Vous devez classer ces applications par ordre de priorité.
    - Machines virtuelles sur-approvisionnées : Exportez votre rapport d’évaluation et filtrez les ordinateurs par utilisation faible du processeur (%) et utilisation faible de la mémoire (%).  Cette opportunité permet de migrer vers une machine virtuelle bien dimensionnée dans Azure et de réaliser des économies sur les ressources sous-utilisées.
    - Contraintes de capacité : Exportez votre rapport d’évaluation et filtrez les ordinateurs par utilisation élevée du processeur (%) et utilisation élevée de la mémoire (%).  Vous pouvez empêcher les machines virtuelles dépendantes de s’arrêter et améliorer les performances en migrant celles-ci vers Azure et en utilisant la fonctionnalité de mise à l’échelle automatique pour répondre à la demande. Vous pouvez également consulter le rapport d’évaluation pour comprendre vos contraintes de stockage en analysant les IOPS et le débit de disque et trouver le type de disque recommandé qui correspond le mieux à vos besoins.

- **Commencez petit, puis voyez les choses en grand** : Commencez par déplacer des applications et des charges de travail qui présentent un risque minime et moins complexe, afin que votre stratégie de migration soit digne de confiance. Vous pouvez également croiser vos recommandations d’évaluation Azure Migrate avec le référentiel CMDB de votre organisation pour rechercher et migrer des charges de travail d’environnement Dev/Test dans vos migrations pilotes. Les apprentissages tirés de ces migrations pilotes peuvent être utilisés lors de la migration de charges de travail de production.  

- **Respectez les exigences réglementaires/de votre secteur d’activité** : Azure offre la plus grande gamme de solutions de conformité dans le secteur d’activité en termes d’offres horizontales et verticales. Utilisez cette possibilité pour hiérarchiser vos migrations vers Azure et vous conformer aux normes et lois nationales, régionales et sectorielles. Cela est particulièrement vrai pour les organisations qui gèrent des informations critiques, qui détiennent des informations sensibles ou qui se trouvent dans des secteurs très réglementés, où les normes et les réglementations sont nombreuses et, dans certains cas, changent souvent, ce qui complique le suivi.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Finaliser le plan de migration et préparer la migration

Avant de finaliser votre plan de migration, assurez-vous que ces considérations essentielles relatives à la migration n’entravent pas la planification de votre migration :

- Évaluez les contraintes de bande passante et de latence réseau, ce qui peut entraîner des retards imprévus et perturber la vitesse de réplication de la migration.

- Prenez le temps nécessaire pour effectuer des tests d’acceptation des utilisateurs et de performances sur les applications migrées, ou pour apporter des ajustements post-migration aux applications, telles que la mise à jour de chaînes de connexion à la base de données et la mise à jour des configurations de serveur Web, le basculement et le nettoyage, etc.

- Passez en revue les autorisations Azure recommandées, ainsi que les rôles d’accès serveur/base de données et le modèle d’autorisations nécessaires à la migration.

- Préparez votre organisation et assurez-vous que le personnel est prêt à effectuer la transformation numérique. Une formation solide est essentielle à la réussite des changements organisationnels. Consultez la formation gratuite disponible sur [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), comprenant des cours sur les notions de base d’Azure, l’architecture de la solution et la sécurité. Encouragez votre équipe à étudier également la [certification Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF) .  

- Bénéficiez d’un support pour votre implémentation, si nécessaire. De nombreuses organisations choisissent une aide externe pour prendre en charge la migration vers le cloud. Pour passer à Azure rapidement et en toute confiance avec une assistance personnalisée, envisagez de recourir à [un fournisseur de services managés Azure](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF) ou [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Créez un plan de migration cloud efficace contenant des informations détaillées sur la liste/les groupes d’applications à migrer, les contraintes de disponibilité et de temps d’arrêt de votre application et de vos bases de données, ainsi que les jalons de migration souhaités. Votre plan de migration doit également prendre en compte la durée de la copie des données et inclure une mémoire tampon à considérer pour les activités de test et de basculement après la migration. Les tests post-migration doivent inclure des cas d’utilisation de test fonctionnel, d’intégration, de sécurité et de performances pour s’assurer que les applications migrées fonctionnent comme prévu et que tous les objets de base de données et les relations de données ont été transférés vers le cloud. Les tests post-migration doivent inclure des cas d’utilisation de test fonctionnel, d’intégration, de sécurité et de performances pour s’assurer que les applications migrées fonctionnent comme prévu et que tous les objets de base de données et les relations de données ont été transférés vers le cloud.  

Utilisez cette analyse pour créer une feuille de route de migration et déclarer une fenêtre de maintenance afin de migrer vos applications et bases de données avec un temps d’arrêt minimal nul et limiter l’impact opérationnel/commercial potentiel pendant la migration.  

Nous vous recommandons de toujours tester et de continuer à utiliser la fonctionnalité *Tester la migration* d’Azure Migrate avant de lancer des migrations à grande échelle vers Azure. Ces données réelles vous aideront à estimer le temps réel nécessaire et à apporter les ajustements nécessaires à votre plan de migration. Un test de migration offre les moyens de détecter tous les problèmes éventuels du plan de migration et de les résoudre avant de procéder à la migration réelle.  

Lorsque vous êtes prêt à migrer, utilisez l’*outil de migration de serveur* d’Azure Migrate et le *service de migration de données* d’Azure Migrate pour une expérience de migration transparente et intégrée avec suivi de bout en bout. L’outil de migration du serveur prend en charge la migration des machines virtuelles et des serveurs hébergés localement dans le centre de centres des clients ou dans tout autre cloud privé ou public, notamment AWS, GCP, etc., sans aucune interruption de l’activité. Azure Database Migration Service est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime.  

> [!NOTE]
> Pour les machines virtuelles VMware, Server Assessment utilise le système d’exploitation spécifié pour la machine virtuelle dans vCenter Server pour gérer l’analyse du système d’exploitation invité. Pour les machines virtuelles Linux s’exécutant sur VMware, il n’identifie actuellement pas la version exacte du noyau du système d’exploitation invité.

## <a name="next-steps"></a>Étapes suivantes

- Examinez le  [parcours de migration cloud](/azure/architecture/cloud-adoption/getting-started/migrate) dans le framework d’adoption du cloud Azure.
- [Effectuez la prise en main](https://youtu.be/wFfq3YPxYHE) avec Azure Migrate.
- Créer une évaluation pour des [machines virtuelles VMware](tutorial-assess-vmware.md) ou des [machines virtuelles Hyper-V](tutorial-assess-hyper-v.md).
