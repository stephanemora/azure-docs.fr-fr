---
title: Sauvegarde et récupération d’urgence dans Azure Australie
description: Sauvegarde et récupération d’urgence dans Microsoft Azure pour les agences du gouvernement australien en ce qui concerne l’ASD Essential 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571137"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Sauvegarde et récupération d’urgence dans Azure Australie

Avoir des plans de sauvegarde et de récupération d’urgence avec l’infrastructure de support en place est essentiel pour toutes les organisations. L’importance d’avoir une solution de sauvegarde est soulignée par son inclusion dans le [Australian Cyber Security Center’s Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

Microsoft Azure fournit deux services qui permettent la résilience : Sauvegarde Azure et Azure Site Recovery. Ces services vous permettent de protéger vos données, tant en local que dans le cloud, pour différents scénarios de conception. Sauvegarde Azure et Azure Site Recovery utilisent une ressource commune de stockage et de gestion : le coffre-fort Azure Recovery Services. Ce coffre-fort permet de gérer, surveiller et séparer les données Sauvegarde Azure et Azure Site Recovery.

Cet article détaille les principaux éléments de conception pour l’implémentation de Sauvegarde Azure et Azure Site Recovery conformément aux contrôles du [Australian Signals Directorate’s (ASD) Information Security Manual (ISM) Controls](https://acsc.gov.au/infosec/ism/index.htm) (Manuel de sécurité de l’information de la direction australienne des signaux).

## <a name="azure-backup"></a>Sauvegarde Azure

![Sauvegarde Azure](media/backup-overview.png)

Le service Sauvegarde Azure ressemble à une solution de sauvegarde traditionnelle en local et offre la possibilité de sauvegarder à la fois les données en local et les données hébergées par Azure. Sauvegarde Azure permet de sauvegarder les types de données suivants dans Azure :

* Fichiers et dossiers
* Les systèmes d’exploitation Windows et Linux pris en charge hébergés sur :
  * Des hyperviseurs Hyper-V et VMWare
  * Du matériel physique
* Des applications Microsoft prises en charge

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery réplique les charges de travail constituées d’une seule machine virtuelle ou d’applications multiniveaux. La réplication est prise en charge depuis l’environnement local dans Azure, entre les régions Azure ou entre des emplacements locaux orchestrés par Azure Site Recovery. Les machines virtuelles locales peuvent être répliquées vers Azure ou vers un hyperviseur local pris en charge. Une fois configuré, Azure Site Recovery orchestre la réplication, le basculement et la restauration automatique.

## <a name="key-design-considerations"></a>Considérations clés en matière de conception

Lors de l’implémentation d’une solution de sauvegarde ou de récupération d’urgence, la solution proposée doit tenir compte des éléments suivants :

* La portée et le volume des données à capturer
* La durée de conservation des données
* Les méthodes de stockage et de gestion sécurisées de ces données
* Les emplacements géographiques de stockage de ces données
* Les procédures de test de routine du système

ISM fournit des conseils sur les considérations relatives à la sécurité qui doivent être prises lors de la conception d’une solution. Microsoft Azure fournit des moyens de résoudre ces problèmes de sécurité.

### <a name="data-sovereignty"></a>Souveraineté des données

Les organisations doivent s’assurer que la souveraineté des données est respectée lorsqu’elles utilisent des emplacements de stockage basés sur le cloud. Azure Policy fournit les moyens de limiter les emplacements autorisés où une ressource Azure peut être créée. La règle Azure Policy intégrée intitulée « Allowed Locations » (« Emplacements autorisés ») permet de s’assurer que toutes les ressources Azure créées sous l’étendue d’une entité Azure Policy affectée peuvent uniquement être créées dans des emplacements géographiques désignés.

Les éléments de restriction géographique d’Azure Policy pour les ressources Azure sont les suivants :

* allowedLocations
* allowedSingleLocation

Ces stratégies permettent aux administrateurs Azure de limiter la création à une liste d’emplacements désignés ou même en tant qu’emplacement géographique unique.

### <a name="redundant-and-geographically-dispersed-storage"></a>Stockage redondant et géographiquement dispersé

Les données stockées dans le coffre-fort Azure Recovery Service sont toujours stockées sur un stockage redondant. Par défaut, le coffre Recovery Services utilise le stockage géographiquement redondant Azure (GRS). Les données stockées à l’aide de GRS sont répliquées vers d’autres centres de données Azure dans la [région jumelée secondaire](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) du coffre-fort Recovery Services. Ces données répliquées sont stockées en lecture seule et sont uniquement accessibles en écriture en cas d’événement de basculement Azure. Dans le centre de données Azure, les données sont répliquées entre les domaines d’erreur et les domaines de mise à niveau distincts pour réduire le risque de panne de matériel ou de maintenance. GRS fournit une disponibilité annuelle d’au moins 99,99999999999999 %.

Le coffre-fort d’Azure Recovery Services peut être configuré pour utiliser le stockage localement redondant (LRS). LRS est une option de stockage à moindre coût avec un compromis de disponibilité réduite. Ce modèle de redondance utilise la même réplication entre les domaines d’erreur et les domaines de mise à niveau distincts, mais n’est pas répliqué entre les régions géographiques. Les données situées sur le stockage LRS, même si elles ne sont pas aussi résilientes que GRS, fournissent toujours une disponibilité d’au moins 99,999999999 % par an.

Contrairement aux technologies de stockage hors site traditionnelles telles que les médias à bandes, les copies supplémentaires des données sont créées automatiquement et ne nécessitent pas de surcharge administrative supplémentaire.

### <a name="restricted-access-and-activity-monitoring"></a>Accès restreint et surveillance des activités

Les données de sauvegarde doivent être protégées contre toute altération, modification et suppression non approuvée. Sauvegarde Azure et Azure Site Recovery utilisent la structure de gestion Azure courante. Cette structure fournit un audit détaillé, une journalisation et un contrôle d’accès en fonction du rôle aux ressources situées dans Azure. L’accès aux données de sauvegarde peut être limité au personnel administratif, et toutes les actions impliquant des données de sauvegarde peuvent être consignées et auditées.

Sauvegarde Azure et Azure Site Recovery ont des fonctionnalités intégrées de journalisation et de création de rapports. Les problèmes qui se produisent lors de la sauvegarde ou de la réplication sont signalés aux administrateurs à l’aide de l’infrastructure de gestion Azure.

Le coffre-fort Azure Recovery Services présente également les mesures de sécurité des données supplémentaires suivantes :

* Les données de sauvegarde sont conservées pendant 14 jours après une opération de suppression
* Des alertes et des notifications pour les opérations critiques, telles que « Arrêter la sauvegarde avec suppression de données »
* Une exigence de code PIN de sécurité pour les opérations critiques
* Une vérification de la durée de rétention minimale en place

Ces vérifications de la durée de rétention minimale sont les suivantes :

* Pour une rétention quotidienne, une rétention de sept jours minimum
* Pour une rétention hebdomadaire, une rétention de quatre semaines minimum
* Pour une rétention mensuelle, une rétention de trois mois minimum
* Pour une rétention annuelle, une rétention d’un an minimum

Toutes les données de sauvegarde stockées dans Azure sont chiffrées au repos à l’aide d’Azure Storage Service Encryption (SSE). Ce chiffrement est activé par défaut pour tous les comptes de stockage nouveaux et existants et ne peut pas être désactivé. Les données chiffrées sont déchiffrées automatiquement pendant la récupération. Par défaut, les données chiffrées à l’aide de SSE sont chiffrées à l’aide d’une clé fournie par et gérée par Microsoft. Les organisations peuvent choisir de fournir et de gérer leur propre clé de chiffrement à utiliser avec SSE. Cela fournit une couche supplémentaire de sécurité facultative pour les données chiffrées. Cette clé peut être stockée par le client en local ou en toute sécurité au sein du coffre de clés Azure.

### <a name="secure-data-transport"></a>Transport de données sécurisé

Données de Sauvegarde Azure chiffrées en transit à l’aide d’AES 256. Ces données sont sécurisées via l’utilisation d’une phrase secrète créée par le personnel administratif lors de la première configuration de la sauvegarde. Microsoft n’a pas accès à cette phrase secrète, ce qui signifie que le client doit s’assurer que cette phrase secrète est stockée en toute sécurité. Le transfert de données s’effectue ensuite entre l’environnement local et le coffre-fort d’Azure Recovery Services via une connexion HTTPS sécurisée.  Les données du coffre-fort Recovery Services sont ensuite chiffrées au repos à l’aide d’Azure SSE.

Les données Azure Site Recovery sont également toujours chiffrées en transit. Toutes les données répliquées sont transportées en toute sécurité vers Azure à l’aide du HTTPS et du TLS. Lorsqu’un client Azure se connecte à Azure à l’aide d’une connexion ExpressRoute, les données Azure Site Recovery sont envoyées via cette connexion privée.  Lorsqu’un client Azure se connecte à Azure à l’aide d’une connexion VPN, les données sont répliquées entre les sites locaux et le coffre-fort Recovery Services en toute sécurité via Internet.

Ce transfert de données réseau sécurisé supprime les risques de sécurité et les exigences d’atténuation pour la gestion des solutions de stockage de sauvegarde hors site traditionnelles, telles que les médias à bandes.

### <a name="data-retention-periods"></a>Période de rétention des données

Une période de rétention de sauvegarde minimale de trois mois est recommandée, toutefois, des périodes de rétention plus longues sont généralement requises. Sauvegarde Azure peut fournir jusqu’à 9 999 copies d’une sauvegarde. Si une Sauvegarde Azure unique d’une instance protégée a été effectuée quotidiennement, cela permettrait la rétention de 27 années de sauvegardes quotidiennes. Les sauvegardes mensuelles individuelles d’une instance protégée permettent jusqu’à 833 ans de rétention. À mesure que les données de sauvegarde sont obsolètes et que les sauvegardes moins granulaires sont conservées au fil du temps, la fenêtre de rétention totale des données de sauvegarde augmente.  Azure ne limite pas la durée pendant laquelle les données peuvent rester dans un coffre-fort Azure Recovery Services, mais uniquement le nombre total de sauvegardes par instance. Il n’y a pas non plus de différence de performances entre la restauration à partir d’anciennes ou de nouvelles sauvegardes : chaque restauration a la même durée.

Le coffre d’Azure Recovery Services dispose d’un certain nombre de stratégies de sauvegarde et de rétention implémentées par défaut.  Le personnel administratif peut également créer des stratégies de sauvegarde et de rétention personnalisées.

![Stratégie du service Sauvegarde Azure](media/create-policy.png)

Vous devez trouver un équilibre entre la fréquence de sauvegarde et les exigences de rétention à long terme lors de la configuration des stratégies de rétention et de Sauvegarde Azure.

### <a name="backup-and-restore-testing"></a>Test de la sauvegarde et de la restauration

ISM recommande de tester les données de sauvegarde pour s’assurer que les données protégées sont valides lorsqu’un basculement ou une restauration est nécessaire. Sauvegarde Azure et Azure Site Recovery offrent également la possibilité de tester les données protégées une fois qu’elles ont été sauvegardées ou répliquées. Les données gérées par Sauvegarde Azure peuvent être restaurées à un emplacement désigné et la cohérence des données peut ensuite être validée.

Azure Site Recovery dispose d’une fonctionnalité intégrée pour effectuer des tests de basculement. Les charges de travail répliquées dans le coffre-fort de Recovery Services peuvent être restaurées dans un environnement Azure nommé. L’environnement de restauration cible peut être totalement isolé des environnements de production pour garantir qu’il n’y a aucun impact sur les systèmes de production lors de l’exécution d’un test. Une fois le test terminé, l’environnement de test et toutes les ressources peuvent être supprimés immédiatement pour réduire les coûts d’exploitation.

Le test de basculement et la validation peuvent être automatisés à l’aide du service Azure Automation intégré à la plateforme Azure. Cela permet de planifier le test de basculement automatiquement pour s’assurer que les données sont correctement répliquées vers Azure.

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article sur la [Conformité aux normes de sécurité d’Azure Policy](azure-policy.md).
