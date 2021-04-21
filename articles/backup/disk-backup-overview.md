---
title: Vue d’ensemble de la sauvegarde des disques Azure
description: Découvrez la solution de sauvegarde des disques Azure.
ms.topic: conceptual
ms.date: 04/09/2021
ms.openlocfilehash: 42f37c1f500be719e0bd79bad41226ab3ab2d911
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285137"
---
# <a name="overview-of-azure-disk-backup"></a>Vue d’ensemble de la sauvegarde de disques Azure

La sauvegarde des disques Azure est une solution de sauvegarde cloud native qui protège vos données sur des disques managés. Il s’agit d’une solution simple, sécurisée et économique qui vous permet de configurer la protection des disques managés en quelques étapes. Elle vous garantit de pouvoir récupérer vos données dans un scénario d’urgence.

La sauvegarde des disques Azure offre une solution clé en main qui fournit une gestion du cycle de vie des instantanés pour les disques managés en automatisant la création périodique d’instantanés et en les conservant pour une durée configurée à l’aide d’une stratégie de sauvegarde. Vous pouvez gérer les instantanés des disques sans aucun coût d’infrastructure et sans avoir recours à aucun script personnalisé ni aucune surcharge de gestion. Il s’agit d’une solution de sauvegarde avec cohérence en cas de plantage qui effectue une sauvegarde ponctuelle d’un disque managé au moyen d’[instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) avec une prise en charge de plusieurs sauvegardes par jour. Il s’agit également d’une solution sans agent qui n’a pas d’impact sur les performances des applications de production. Elle prend en charge la sauvegarde et la restauration des disques du système d’exploitation et des données (y compris des disques partagés), qu’ils soient ou non actuellement attachés à une machine virtuelle Azure en cours d’exécution.

Si vous avez besoin d’une sauvegarde cohérente avec les applications de la machine virtuelle, y compris des disques de données, ou d’une option permettant de restaurer l’intégralité d’une machine virtuelle à partir d’une sauvegarde, de restaurer un fichier ou un dossier, ou de restaurer dans une région secondaire, utilisez la solution de [sauvegarde des machines virtuelles Azure](backup-azure-vms-introduction.md). La sauvegarde Azure offre une prise en charge côte à côte pour la sauvegarde des disques managés à l’aide de la sauvegarde des disques en plus des solutions de [sauvegarde des machines virtuelles Azure](./backup-azure-vms-introduction.md). Elle est utile lorsque vous avez besoin de sauvegardes cohérentes avec les applications une fois par jour et de sauvegardes plus fréquentes des disques de système d’exploitation ou d’un disque de données spécifique qui soient cohérentes en cas de plantage et qui n’aient pas d’impact sur les performances des applications de production.

La sauvegarde des disques Azure est intégrée au Centre de sauvegarde, qui fournit une **expérience de gestion unifiée unique** dans Azure pour permettre aux entreprises de régir, superviser, exploiter et analyser les sauvegardes à grande échelle.

## <a name="key-benefits-of-disk-backup"></a>Principaux avantages de la sauvegarde des disques

La sauvegarde des disques Azure est une solution sans agent avec cohérence en cas de plantage, qui utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) et offre les avantages suivants :

- Sauvegardes plus fréquentes et rapides sans interruption de la machine virtuelle.
- N’affecte pas les performances de l’application de production.
- Aucun problème de sécurité, car elle ne nécessite pas l’exécution de scripts personnalisés ni l’installation d’agents.
- Solution économique pour sauvegarder des disques spécifiques par rapport à la sauvegarde d’une machine virtuelle entière.

La solution de sauvegarde des disques Azure est utile dans les scénarios suivants :

- Besoin de sauvegardes fréquentes par jour sans que l’application soit inactive.
- Applications en cours d’exécution dans un scénario de cluster : les clusters de basculement Windows Server et Linux écrivent sur des disques partagés.
- Besoin spécifique d’une sauvegarde sans agent en raison de problèmes de sécurité ou de performances sur l’application.
- La sauvegarde cohérente des applications de la machine virtuelle n’est pas possible, car les applications métier ne prennent pas en charge le service de cliché instantané des volumes (VSS).

Envisagez la sauvegarde des disques Azure dans les scénarios suivants :

- Une application stratégique s’exécute sur une machine virtuelle Azure qui exige plusieurs sauvegardes par jour pour atteindre l’objectif de point de récupération, mais sans avoir d’impact sur les performances de l’environnement de production ou de l’application.
- La réglementation de votre organisation ou de votre secteur d’activité restreint l’installation d’agents pour des raisons de sécurité.
- L’exécution de pré-scripts ou de post-scripts personnalisés et l’appel d’un gel-dégel sur les machines virtuelles Linux pour bénéficier d’une sauvegarde cohérente avec les applications entraînent une surcharge excessive au niveau de la disponibilité des charges de travail de production.
- Les applications conteneurisées qui s’exécutent sur Azure Kubernetes Service (cluster AKS) utilisent des disques managés comme stockage persistant. Aujourd’hui, vous devez sauvegarder le disque managé via des scripts d’automatisation difficiles à gérer.
- Un disque managé comporte des données commerciales critiques, est utilisé comme un partage de fichiers ou contient des fichiers de sauvegarde de base de données, et vous souhaitez optimiser le coût de la sauvegarde en n’investissant pas dans la sauvegarde des machines virtuelles Azure.
- Vous avez de nombreuse machines virtuelles Linux et Windows à disque unique (autrement dit, une machine virtuelle dotée d’un seul disque de système d’exploitation et d’aucun disque de données attaché) qui hébergent un serveur web ou des machines sans état ou qui servent d’environnement intermédiaire avec des paramètres de configuration d’application, et vous avez besoin d’une solution de sauvegarde économique pour protéger le disque du système d’exploitation. Par exemple, pour déclencher une sauvegarde rapide à la demande avant la mise à niveau ou la mise à jour corrective de la machine virtuelle.
- Une machine virtuelle exécute une configuration de système d’exploitation qui n’est pas prise en charge par la solution de sauvegarde des machines virtuelles Azure (par exemple, la version 32 bits de Windows Server 2008).

## <a name="how-the-backup-and-restore-process-works"></a>Fonctionnement du processus de sauvegarde et de restauration

- La première étape de la configuration de la sauvegarde des disques managés Azure consiste à créer un [coffre de sauvegarde](backup-vault-overview.md). Ce coffre vous offre une vue consolidée des sauvegardes configurées sur différentes charges de travail.

- Ensuite, créez une stratégie de sauvegarde vous permettant de configurer la fréquence de sauvegarde et la durée de rétention.

- Pour configurer la sauvegarde, accédez au coffre de sauvegarde, affectez une stratégie de sauvegarde, sélectionnez le disque managé à sauvegarder et fournissez un groupe de ressources dans lequel les instantanés seront stockés et gérés. La sauvegarde Azure déclenche automatiquement les tâches de sauvegarde planifiées qui créent un instantané incrémentiel du disque en fonction de la fréquence de sauvegarde. Les instantanés plus anciens sont supprimés en fonction de la durée de conservation spécifiée par la stratégie de sauvegarde.

- La sauvegarde Azure utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md#restrictions) du disque managé. Les instantanés incrémentiels sont une sauvegarde ponctuelle économique des disques managés qui sont facturés pour les modifications d’ordre différentiel apportées au disque depuis le dernier instantané. Ils sont toujours stockés dans le stockage le plus économique, le stockage de disque dur standard, quel que soit le type de stockage des disques parents. Le premier instantané du disque occupe la taille utilisée du disque et les instantanés incrémentiels suivants stockent les modifications d’ordre différentiel apportées au disque depuis le dernier instantané.

- Une fois que vous avez configuré la sauvegarde d’un disque managé, une instance de sauvegarde est créée dans le coffre de sauvegarde. Avec cette instance de sauvegarde, vous pouvez trouver l’intégrité des opérations de sauvegarde, déclencher des sauvegardes à la demande et effectuer des opérations de restauration. Vous pouvez également consulter l’intégrité des sauvegardes dans plusieurs coffres et instances de sauvegarde à l’aide du Centre de sauvegarde, qui fournit une seule et unique vue.

- Pour effectuer la restauration, il vous suffit de sélectionner le point de récupération à partir duquel vous souhaitez restaurer le disque. Indiquez le groupe de ressources dans lequel le disque restauré doit être créé à partir de l’instantané. La sauvegarde Azure fournit une expérience de restauration instantanée puisque les instantanés sont stockés localement dans votre abonnement.

- Le coffre de sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Pour configurer la sauvegarde d’un disque managé et restaurer à partir d’une sauvegarde antérieure, l’identité managée du coffre de sauvegarde nécessite un ensemble d’autorisations sur le disque source, le groupe de ressources d’instantanés où les instantanés sont créés et gérés, ainsi que le groupe de ressources cibles où vous souhaitez restaurer la sauvegarde. Vous pouvez accorder des autorisations à l’identité managée à l’aide du contrôle d’accès en fonction du rôle Azure (RBAC Azure). L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Découvrez-en plus sur les [identités managées](../active-directory/managed-identities-azure-resources/overview.md).

- Actuellement, la sauvegarde des disques Azure prend en charge la sauvegarde opérationnelle des disques managés et ne copie pas les sauvegardes dans le stockage du coffre de sauvegarde. Reportez-vous à la [matrice de prise en charge ](disk-backup-support-matrix.md) pour obtenir la liste détaillée des scénarios pris en charge et non pris en charge, ainsi que pour connaître la disponibilité dans les régions.

## <a name="pricing"></a>Tarifs

La sauvegarde Azure offre une solution de gestion du cycle de vie des instantanés pour la protection des disques Azure. Les instantanés de disque créés par la sauvegarde Azure sont stockés dans le groupe de ressources de votre abonnement Azure et occasionnent des frais de **stockage d’instantanés**. Vous pouvez consulter [Tarification des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/) pour plus d’informations sur les tarifs des instantanés.<br></br>Comme les instantanés ne sont pas copiés dans le coffre de sauvegarde, la sauvegarde Azure ne facture aucuns frais d’**instance protégée** et le coût de **stockage de sauvegarde** ne s’applique pas. De plus, les instantanés incrémentiels occupent les modifications d’ordre différentiel comme le dernier instantané et sont toujours stockés dans le stockage standard, quel que soit le type de stockage des disques managés parents, et sont facturés selon la tarification du stockage standard. C’est pourquoi la sauvegarde des disques Azure une solution économique.

## <a name="next-steps"></a>Étapes suivantes

[Matrice de prise en charge de sauvegarde de disques Azure](disk-backup-support-matrix.md)
