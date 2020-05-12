---
title: Vue d’ensemble des fonctionnalités de sécurité
description: Découvrez les fonctionnalités de sécurité de Sauvegarde Azure qui vous aident à protéger vos données de sauvegarde et à répondre aux besoins de votre entreprise en matière de sécurité.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 688573b571c6ce4473f06d4c194795a38a33244b
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743657"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Vue d’ensemble des fonctionnalités de sécurité de Sauvegarde Azure

L’une des mesures les plus importantes que vous puissiez prendre pour protéger vos données consiste à vous doter d’une infrastructure de sauvegarde fiable. Mais il est tout aussi important de vous assurer que vos données sont sauvegardées de façon sécurisée et que vos sauvegardes sont protégées à tout moment. Le service Sauvegarde Azure assure la sécurité de votre environnement de sauvegarde, tant lorsque vos données sont en transit que quand elles sont au repos. Cet article répertorie les fonctionnalités de sécurité de Sauvegarde Azure qui vous aident à protéger vos données de sauvegarde et à répondre aux besoins de votre entreprise en matière de sécurité.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestion et contrôle des identités et des accès utilisateur

Les comptes de stockage utilisés par les coffres Recovery Services sont isolés et ne sont pas accessibles aux utilisateurs à des fins malveillantes. L’accès est autorisé uniquement par le biais d’opérations de gestion de Sauvegarde Azure, telles que la restauration. Le service Sauvegarde Azure vous permet de contrôler les opérations managées avec une finesse de précision au niveau des accès à l’aide de la fonctionnalité [RBAC (contrôle d’accès en fonction du rôle)](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault). Le contrôle d’accès en fonction du rôle vous permet de séparer les tâches au sein de votre équipe, et de n’accorder aux utilisateurs que l’accès nécessaire pour accomplir leur travail.

Le service Sauvegarde Azure fournit trois [rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) pour contrôler les opérations de gestion des sauvegardes :

* Contributeur de sauvegarde : création et gestion de sauvegardes, à l’exception de la suppression du coffre Recovery Services et de l’octroi d’accès à d’autres personnes
* Opérateur de sauvegarde : mêmes prérogatives que celles d’un contributeur, à l’exception de la suppression de sauvegardes et de la gestion des stratégies de sauvegarde
* Lecteur de sauvegarde : autorisation d’afficher toutes les opérations de gestion des sauvegardes

Apprenez-en davantage sur le [contrôle d’accès en fonction du rôle pour gérer la Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Le service Sauvegarde Azure dispose de plusieurs contrôles de sécurité intégrés pour empêcher, détecter et traiter les failles de sécurité. Apprenez-en davantage sur les [contrôles de sécurité pour le service Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Séparation entre l’invité et le service Stockage Azure

Dans Sauvegarde Azure, qui inclut la sauvegarde de machine virtuelle et la sauvegarde SQL et SAP HANA dans une machine virtuelle, les données de sauvegarde sont stockées dans un stockage Azure et l’invité n’a pas d’accès direct au stockage de sauvegarde ou à son contenu.  Lors d’une sauvegarde de machine virtuelle, la création et le stockage de l’instantané de sauvegarde sont effectués par la structure Azure où l’invité n’a aucune implication autre que la suspension de la charge de travail pour les sauvegardes cohérentes d’applications.  Avec SQL et SAP HANA, l’extension de sauvegarde obtient un accès temporaire pour écrire dans des blobs spécifiques.  De cette façon, même dans un environnement compromis, l’invité ne peut ni falsifier ni supprimer des sauvegardes existantes.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Connectivité Internet non requise pour la sauvegarde de machines virtuelles Azure

La sauvegarde de machines virtuelles Azure nécessite un déplacement de données du disque de votre machine virtuelle vers le coffre de Recovery Services. Toutefois, toutes les opérations de communication et de transfert de données requises se produisent uniquement sur le réseau principal Azure sans nécessité d’accéder à votre réseau virtuel. Par conséquent, la sauvegarde de machines virtuelles Azure placées dans des réseaux sécurisés ne vous oblige pas à autoriser l’accès à des adresses IP ou à des noms de domaine complets.

## <a name="private-endpoints-for-azure-backup"></a>Points de terminaison privés pour stockage Azure

Vous pouvez désormais utiliser des [points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) pour sauvegarder en toute sécurité les données des serveurs de votre réseau virtuel vers votre coffre Recovery Services. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre coffre. Vous n’avez donc pas besoin d’exposer vos réseaux virtuels à des adresses IP publiques. Les points de terminaison privés permettent de sauvegarder et de restaurer vos bases de données SQL et SAP HANA actives à l’intérieur de vos machines virtuelles Azure. Ils sont également utilisables pour vos serveurs locaux à l’aide de l’agent MARS.

>[!NOTE]
> La disponibilité de cette fonctionnalité est actuellement limitée. Répondez à [cette enquête](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) et [envoyez-nous un e-mail](mailto:azbackupnetsec@microsoft.com) si vous souhaitez utiliser des points de terminaison privés pour le service Sauvegarde Azure. La possibilité d’utiliser cette fonctionnalité est soumise à l’approbation du service Sauvegarde Azure.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Chiffrement des données en transit et au repos

Le chiffrement protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Dans Azure, les données en transit entre le stockage Azure et le coffre sont protégées par HTTPS. Ces données restent sur le réseau principal Azure.

* Les données de sauvegarde sont automatiquement chiffrées à l’aide de clés gérées par Microsoft. Vous pouvez également chiffrer vos machines virtuelles avec disques managés sauvegardées dans le coffre Recovery Services à l’aide de [clés gérées par le client](backup-encryption.md#encryption-of-backup-data-using-customer-managed-keys) stockées dans Azure Key Vault. Vous n’avez aucune action explicite à effectuer pour activer ce chiffrement. Il s’applique à toutes les charges de travail sauvegardées dans votre coffre Recovery Services.

* Le service Sauvegarde Azure prend en charge la sauvegarde et la restauration des machines virtuelles Azure dont les disques de système d’exploitation ou de données sont chiffrés avec Azure Disk Encryption. [Apprenez-en davantage sur les machines virtuelles Azure chiffrées et le service Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Protection des données de sauvegarde contre les suppressions involontaires

Le service Sauvegarde Azure fournit des fonctionnalités de sécurité pour vous aider à protéger les données de sauvegarde même après leur suppression. Avec la suppression réversible, si l’utilisateur supprime la sauvegarde d’une machine virtuelle, les données de sauvegarde sont conservées pendant 14 jours supplémentaires, ce qui permet la récupération de cette sauvegarde sans perte de données. La conservation des données de sauvegarde pendant 14 jours supplémentaires dans l’état « suppression réversible » n’occasionne pas de frais pour le client. [Apprenez-en davantage sur la suppression réversible](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Surveillance et alertes d’activité suspecte

le service Sauvegarde Azure intègre des [fonctionnalités de surveillance et d’alerte](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) permettant d’afficher et de configurer des actions pour des événements liés au service Sauvegarde Azure. Les [Rapports de sauvegarde](https://docs.microsoft.com/azure/backup/configure-reports) font office de destination unique pour le suivi de l’utilisation, l’audit des sauvegardes et des restaurations, ainsi que l’identification de tendances clés à différents niveaux de précision. Les outils de surveillance et signalement de Sauvegarde Azure peuvent vous avertir d’activités non autorisées, suspectes ou malveillantes dès qu’elles se produisent.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Fonctionnalités de sécurité pour la protection de sauvegardes hybrides

Le service Sauvegarde Azure utilise l’agent Microsoft Azure Recovery Services (MARS) pour sauvegarder et restaurer des fichiers, des dossiers ainsi que le volume ou l’état du système d’un ordinateur local sur Azure. L’agent MARS offre désormais des fonctionnalités de sécurité pour vous aider à protéger des sauvegardes hybrides. Voici quelques fonctionnalités :

* Une couche supplémentaire d’authentification est ajoutée à chaque fois qu’une opération critique (par exemple, Modifier la phrase secrète) est effectuée. Cette validation permet de garantir que ces opérations ne peuvent être effectuées que par les utilisateurs ayant des informations d’identification Azure valides. [Apprenez-en davantage sur les fonctionnalités qui empêchent les attaques](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* Les données de sauvegarde supprimées sont conservées pendant 14 jours à compter de la date de suppression. Cela garantit la possibilité de les récupérer dans un délai donné afin d’éviter toute perte, même en cas d’attaque. En outre, les points de récupération minimum sont conservés en plus grand nombre pour offrir une protection contre les données corrompues. [Apprenez-en davantage sur la récupération des données de sauvegarde supprimées](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* Pour les données sauvegardées à l’aide de l’agent Microsoft Azure Recovery Services (MARS), une phrase secrète est utilisée pour garantir que les données sont chiffrées avant leur chargement vers Sauvegarde Azure et déchiffrées uniquement après leur téléchargement à partir du service. Les détails de la phrase secrète ne sont accessibles qu’à l’utilisateur qui l’a créée et à l’agent associé configuré. Rien n’est transmis ou partagé avec le service. Cela garantit une sécurité totale de vos données, car toutes les données exposées par inadvertance (par exemple, une attaque de l’intercepteur sur le réseau) sont inutilisables sans la phrase secrète, et celle-ci n’est pas envoyée sur le réseau.

## <a name="compliance-with-standardized-security-requirements"></a>Conformité aux exigences de sécurité standardisées

Pour aider les entreprises à répondre aux exigences nationales, régionales et sectorielles régissant la collecte et l’utilisation des données des personnes, Microsoft Azure et le service Stockage Azure offrent un ensemble complet de certifications et d’attestations. [Consultez la liste des certifications de conformité](compliance-offerings.md)

## <a name="next-steps"></a>Étapes suivantes

* [Fonctionnalités de sécurité pour protéger les charges de travail cloud utilisant le service Sauvegarde Azure](backup-azure-security-feature-cloud.md)
* [Fonctionnalités de sécurité pour la protection des sauvegardes hybrides ayant recours au service Sauvegarde Azure](backup-azure-security-feature.md)
