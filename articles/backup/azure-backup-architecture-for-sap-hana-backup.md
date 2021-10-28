---
title: Architecture de Sauvegarde Azure pour la sauvegarde de SAP HANA
description: Apprenez-en davantage sur l’architecture de Sauvegarde Azure pour la sauvegarde de SAP HANA.
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: df8bb6adfeff88eafba19bcc459f887f075aa75b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244575"
---
# <a name="azure-backup-architecture-for-sap-hana-backup"></a>Architecture de Sauvegarde Azure pour la sauvegarde de SAP HANA

Le [service Sauvegarde Azure](./backup-overview.md) vous permet de sauvegarder des données à partir de bases de données SAP HANA dans une application de manière cohérente. Cet article décrit l’architecture, les composants et les processus de Sauvegarde Azure.

## <a name="how-does-azure-backup-work-with-sap-hana-databases"></a>Comment le service Sauvegarde Azure fonctionne-t-il avec des bases de données SAP HANA ?

Le service Sauvegarde Azure fournit une solution de sauvegarde par diffusion en continu pour sauvegarder les bases de données SAP HANA s’exécutant sur une machine virtuelle Azure. Cette offre de sauvegarde nécessite une configuration sans infrastructure, ce qui élimine la nécessité de déployer et de gérer une infrastructure de sauvegarde.

Le service Sauvegarde Azure [certifié Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) par SAP assure la prise en charge de sauvegarde native à l’aide d’API natives de SAP HANA. Cette solution vous permet de sauvegarder et restaurer en toute transparence les bases de données SAP HANA s’exécutant sur des machines virtuelles Azure, ainsi que d’utiliser les fonctionnalités de gestion d’entreprise de Sauvegarde Azure.

[En savoir plus](./sap-hana-db-about.md#added-value) sur les valeurs ajoutées qu’apporte Sauvegarde Azure pour SAP HANA.

## <a name="where-is-the-data-backed-up"></a>Où les données sont-elles sauvegardées ?

Sauvegarde Azure stocke les données sauvegardées dans des coffres Recovery Services. Un coffre est une entité de stockage en ligne dans Azure qui permet de stocker des données telles que des copies de sauvegarde, des points de récupération et des stratégies de sauvegarde.

[En savoir plus](./backup-azure-backup-faq.yml) sur le coffre Recovery Services.

## <a name="backup-agents"></a>Agents de sauvegarde

Pour sauvegarder des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure, vous devez autoriser l’installation du plug-in (agent de sauvegarde SAP HANA) sur la machine virtuelle Azure. Ce plug-in se connecte à HANA Backint et aide le service Sauvegarde Azure à déplacer les données vers le coffre. Il permet également au service Sauvegarde Azure d’effectuer des restaurations.

## <a name="backup-types"></a>Types de sauvegarde

[En savoir plus](./backup-architecture.md#sap-hana-backup-types) sur les types de sauvegardes SAP HANA.

## <a name="about-architecture"></a>À propos de l’architecture

Examinez l’[architecture de haut niveau de Sauvegarde Azure pour les bases de données SAP HANA](./sap-hana-db-about.md#backup-architecture). Pour une compréhension détaillée du processus de sauvegarde, examinez le processus suivant :

:::image type="content" source="./media/sap-hana-db-about/backup-architecture.png" alt-text="Diagramme montrant le processus de sauvegarde de base de données SAP HANA.":::

1. Pour commencer le processus de sauvegarde, [créez un coffre Recovery Services](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) dans Azure. Ce coffre sera utilisé pour stocker les sauvegardes et les points de récupération créés au fil du temps.

1. La machine virtuelle Azure exécutant un serveur SAP HANA est inscrite auprès du coffre, et les bases de données à sauvegarder sont [découvertes](./tutorial-backup-sap-hana-db.md#discover-the-databases). Pour permettre au service Sauvegarde Azure de découvrir les bases de données, vous devez exécuter ce [script de préinscription](https://go.microsoft.com/fwlink/?linkid=2173610) sur le serveur HANA en tant qu’utilisateur racine. 
   >[!Note]
   >Assurez-vous que l’instance HANA est en cours d’exécution pendant la découverte des bases de données dans cette instance.

1. Assurez-vous également que les [autres conditions préalables](./tutorial-backup-sap-hana-db.md#prerequisites) sont remplies.

   >[!Important]
   >Assurez-vous que la condition préalable pour configurer la connectivité réseau appropriée est remplie. Consultez la recommandation relative à la [configuration des machines virtuelles Azure s’exécutant dans SAP Hana avec des composants réseau supplémentaires pour utiliser l’offre de sauvegarde](./tutorial-backup-sap-hana-db.md#set-up-network-connectivity).

1. Examinez les détails de [ce que fait le script de préinscription](./tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). Si vous tentez de configurer une sauvegarde pour des bases de données SAP HANA sans exécuter ce script, il se peut que vous receviez l’erreur _UserErrorHanaScriptNotRun_.

1. Le service Sauvegarde Azure installe à présent le plug-in Sauvegarde Azure pour HANA sur le serveur SAP HANA inscrit. Ce plug-in se sert de l’utilisateur de Sauvegarde créé par le script de préinscription pour effectuer toutes les opérations de sauvegarde et de restauration.

1. Pour [configurer une sauvegarde](./tutorial-backup-sap-hana-db.md#configure-backup) sur les bases de données découvertes, choisissez la stratégie de sauvegarde requise, puis activez les sauvegardes.

1. Le service Sauvegarde Azure pour SAP HANA (solution certifiée par BackInt), ne dépend pas des types de disque ou de machine virtuelle sous-jacents. La sauvegarde est effectuée via des flux générés par HANA.

## <a name="backup-flow"></a>Flux de sauvegarde

1. Les sauvegardes planifiées sont gérées par des entrées crontab créées sur la machine virtuelle HANA, tandis que les sauvegardes à la demande sont déclenchées directement par le service Sauvegarde Azure.

1. Lorsque le moteur de sauvegarde/Backint SAP HANA reçoit la demande de sauvegarde, il prépare la base de données SAP HANA pour une sauvegarde en créant un point d’enregistrement et en déplaçant les données vers les volumes de stockage sous-jacents.

1. Backint exécute ensuite l’opération de lecture à partir des volumes de données sous-jacents : le serveur d’index et le moteur XS pour la base de données de locataire, et le serveur de noms pour la SYSTEMDB. Les disques SSD Premium peuvent fournir un débit d’E/S optimal pour l’opération de diffusion en continu de sauvegarde. Toutefois, l’utilisation de disques non mis en cache avec M64Is peut offrir des vitesses supérieures.

1. Pour diffuser en continu les données de sauvegarde, Backint crée jusqu’à trois canaux qui écrivent directement dans le coffre Recovery Services de Sauvegarde Azure.

   Si vous n’utilisez pas de pare-feu/appliance virtuelle réseau dans votre configuration, le flux de sauvegarde est transféré via le réseau Azure vers le coffre Recovery Services. Par ailleurs, vous pouvez configurer un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) ou un [point de terminaison privé](../private-link/private-endpoint-overview.md) pour autoriser SAP HANA à envoyer le trafic de sauvegarde directement au service Stockage Azure, en ignorant l’appliance virtuelle réseau et le Pare-feu Azure. En outre, lorsque vous utilisez le pare-feu ou l’appliance virtuelle réseau, le trafic vers Azure Active Directory et le coffre Recovery Services transite par le pare-feu ou l’appliance virtuelle réseau sans affecter les performances globales de sauvegarde. 

1. Le service Sauvegarde Azure tente d’atteindre des vitesses jusqu’à 420 Mo/s pour les sauvegardes autres que de journaux et jusqu’à 100 Mo/s pour les sauvegardes de journaux. [En savoir plus](./tutorial-backup-sap-hana-db.md#understanding-backup-and-restore-throughput-performance) sur les performances en matière de débit de sauvegarde et de restauration.

1. Des journaux détaillés sont écrits dans les fichiers _backup.log_ et _backint.log_ sur l’instance SAP HANA.

1. Une fois la diffusion en continu de la sauvegarde terminée, le catalogue est diffusé vers le coffre Recovery Services. Si la sauvegarde (complète/différentielle/incrémentielle/de journal) et le catalogue pour cette sauvegarde sont correctement diffusés et enregistrés dans le coffre Recovery Services, le service Sauvegarde Azure considère que l’opération de sauvegarde est réussie.

Référez-vous aux configurations SAP HANA suivantes et observez l’exécution de l’opération de sauvegarde mentionnée ci-dessus :

**Scénario de configuration de SAP HANA : réseau Azure - sans appliance virtuelle réseau/Pare-feu Azure**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-without-nva-or-azure-firewall.png" alt-text="Diagramme montrant la configuration de SAP HANA si le réseau Azure est sans appliance virtuelle réseau/Pare-feu Azure.":::

**Scénario de configuration de SAP HANA : réseau Azure - avec itinéraire défini par l’utilisateur (UDR) + appliance virtuelle réseau/Pare-feu Azure**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall.png" alt-text="Diagramme montrant la configuration de SAP HANA si le réseau Azure est avec itinéraire défini par l’utilisateur (UDR) + appliance virtuelle réseau/Pare-feu Azure.":::

>[!Note]
>L’appliance virtuelle réseau ou le Pare-feu Azure peuvent ajouter une surcharge quand SAP HANA diffuse la sauvegarde vers le service Stockage Azure/coffre Recovery Services (plan de données). Regarde le _point 6_ dans le diagramme ci-dessus.

**Scénario de configuration de SAP HANA : réseau Azure - avec itinéraire défini par l’utilisateur (UDR) + appliance virtuelle réseau/Pare-feu Azure + Point de terminaison privé ou Point de terminaison de service**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall-and-private-endpoint-or-service-endpoint.png" alt-text="Diagramme montrant la configuration de SAP HANA si le réseau Azure est avec itinéraire défini par l’utilisateur (UDR) + appliance virtuelle réseau/Pare-feu Azure + Point de terminaison privé ou Point de terminaison de service.":::

## <a name="next-steps"></a>Étapes suivantes

[Sauvegarder des bases de données SAP HANA dans des machines virtuelles Azure](./backup-azure-sap-hana-database.md).
