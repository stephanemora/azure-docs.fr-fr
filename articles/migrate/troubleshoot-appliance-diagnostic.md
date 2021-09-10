---
title: Résoudre les problèmes de diagnostic de l’appliance Azure Migrate
description: Obtenez de l’aide pour diagnostiquer et résoudre les problèmes qui peuvent se produire avec l’appliance Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.openlocfilehash: 03be4d6a4b5afb15b5820c1c8b334e3fcb80bbbb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533297"
---
# <a name="diagnose-and-solve-issues-with-azure-migrate-appliance"></a>Diagnostiquer et résoudre les problèmes liés à l’appliance Azure Migrate

La capacité **Diagnostiquer et résoudre** sur l’appliance Azure Migrate aide les utilisateurs à identifier et à évaluer eux-mêmes tout problème lié à la configuration de l’appliance qui pourrait bloquer l’initiation de la détection ou des problèmes liés à une opération Migrate en cours telle que la détection, l’évaluation ou la réplication (*dans le cas d’une appliance VMware*) à partir de l’appliance. 

Vous pouvez exécuter **Diagnostiquer et résoudre** à tout moment à partir du gestionnaire de configuration de l’appliance pour générer un rapport de diagnostic. Le rapport fournit des informations sur les vérifications effectuées, leur état, les problèmes identifiés et les étapes recommandées pour résoudre les problèmes. 

> [!IMPORTANT]
> La capacité **Diagnostiquer et résoudre** est actuellement en préversion pour l’appliance Azure Migrate.
> Cette préversion est couverte par le support client et peut être utilisée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="diagnostic-checks"></a>Vérifications de diagnostic

*Diagnostiquer et résoudre* effectue quelques prévalidations pour vérifier que les fichiers config requis ne sont pas manquants ou bloqués par un logiciel antivirus sur l’appliance, puis effectue les vérifications suivantes : 

**Catégorie** | **Vérification des diagnostics** |**Description**
--- | --- | --- |
**Vérifications des prérequis** | Vérifications de la connectivité | Vérifie si l’appliance dispose d’une connectivité à Azure, directement ou via un proxy.
|| Vérification de la synchronisation de l’heure | Vérifie si l’heure du serveur de l’appliance est synchronisée avec l’heure réseau.
|| Vérification de la mise à jour automatique | Vérifie si la mise à jour automatique est activée et si tous les agents exécutés sur l’appliance sont à jour.
||Vérification de VDDK | Vérifie si les fichiers VDDK obligatoires ont été téléchargés et copiés à l’emplacement requis sur le serveur de l’appliance.
**Vérifications de l’intégrité du service** |État opérationnel |Vérifie si les agents sur l’appliance sont en état de fonctionnement. <br>*Si ce n’est pas le cas, l’appliance se résout automatiquement en redémarrant les agents.* 
||Connectivité aux points de terminaison de service |Vérifie si les agents peuvent communiquer avec leurs services respectifs sur Azure, directement ou via un proxy.
**Vérifications spécifiques à Azure** |Disponibilité de l’application AAD* | Vérifie si l’application AAD créée pendant l’inscription de l’appliance est disponible et accessible à partir de l’appliance.
||Disponibilité du projet Migrate* | Vérifie si le projet Migrate auprès duquel l’appliance a été inscrite existe toujours et est accessible à partir de l’appliance.
||Disponibilité des ressources essentielles*| Vérifie si les ressources Migrate créées pendant l’inscription de l’appliance existent toujours et sont accessibles à partir de l’appliance.
**Vérifications spécifiques à l’appliance** | Disponibilité du certificat Key Vault* | Vérifie si le certificat téléchargé à partir de Key Vault lors de l’inscription de l’appliance est toujours disponible sur le serveur de l’appliance. <br> *Si ce n’est pas le cas, l’appliance se résout automatiquement en téléchargeant à nouveau le certificat, à condition que le coffre de clés soit disponible et accessible*.
|| Disponibilité du magasin d’informations d’identification | Vérifie si les ressources du magasin d’informations d’identification sur le serveur de l’appliance n’ont pas été déplacées, supprimées ou modifiées.
|| Composants ASR ou de l’appliance de réplication | Vérifie si le même serveur a également été utilisé pour installer des composants de l’appliance de réplication ou ASR. *Il est actuellement impossible d’installer à la fois Azure Migrate et l’appliance de réplication (pour la migration basée sur un agent) sur le même serveur.*
|| Disponibilité de la licence du système d’exploitation | Vérifie si la licence d’évaluation sur le serveur de l’appliance créé à partir de l’OVA ou du VHD est toujours valide. *La licence d’évaluation de Windows Server 2016 est valide pendant 180 jours.*
|| Utilisation de l’UC et de la mémoire | Vérifie l’utilisation de l’UC et de la mémoire par les agents Migrate sur le serveur de l’appliance.  

**vérifié et signalé uniquement si l’appliance a déjà été inscrite. Ces vérifications sont exécutées dans le contexte de l’utilisateur Azure actuel connecté à l’appliance.*

## <a name="running-diagnostic-checks"></a>Exécution des vérifications de diagnostic

Si vous rencontrez des problèmes avec l’appliance pendant sa configuration ou si vous constatez des problèmes avec les opérations Migrate en cours comme la détection, l’évaluation ou la réplication (*dans le cas d’une appliance VMware*) sur le portail, vous pouvez accéder au gestionnaire de configuration de l’appliance et exécuter les diagnostics.

> [!NOTE]
> Actuellement, **Diagnostiquer et résoudre** peut effectuer des vérifications liées à la connectivité de l’appliance à Azure, à la disponibilité des ressources requises sur le serveur de l’appliance ou à Azure. Les problèmes de connectivité ou de détection de l’environnement source, comme vCenter Server/les hôtes ESXi/les hôtes Hyper-V/les machines virtuelles/les serveurs physiques, ne sont actuellement pas couverts par **Diagnostiquer et résoudre**.
 
1. Sélectionnez **Diagnostiquer et résoudre** dans le ruban situé en haut du gestionnaire de configuration.

    ![Sélectionner Diagnostiquer et résoudre](./media/troubleshoot-appliance-diagnostic-solve/appliance-configuration-manager-diagnose-solve.png)
    
    Après avoir sélectionné **Diagnostiquer et résoudre**, l’appliance commence automatiquement à exécuter les vérifications de diagnostic. Cela peut prendre environ cinq minutes.
    *Vous verrez le timestamp du dernier rapport de diagnostic si vous avez exécuté les vérifications auparavant.*
     
    ![Rapport de diagnostic](./media/troubleshoot-appliance-diagnostic-solve/diagnostic-report.png)

1. Une fois les vérifications de diagnostic terminées, vous pouvez soit afficher le rapport dans un autre onglet où vous pouvez choisir de l’enregistrer au format PDF, soit vous rendre à l’emplacement suivant sur le serveur de l’appliance : **C:\Users\Public\Desktop\DiagnosticsReport**. Le rapport y est enregistré automatiquement au format HTML.

    ![Afficher le rapport de diagnostic](./media/troubleshoot-appliance-diagnostic-solve/view-diagnostic-report.png)

1. Le rapport fournit des informations sur les vérifications effectuées, leur état, les problèmes identifiés et les étapes recommandées pour résoudre les problèmes.

    ![Afficher l’état du rapport de diagnostic](./media/troubleshoot-appliance-diagnostic-solve/view-status.png)

1. Vous pouvez suivre les étapes de correction du rapport pour résoudre un problème. Si vous ne parvenez pas à résoudre le problème, il est recommandé de joindre le rapport de diagnostic au cas de support Microsoft que vous créez afin d’en accélérer la résolution.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes qui ne sont pas couverts par **Diagnostiquer et résoudre**, vous pouvez consulter [Résoudre les problèmes de l’appliance Azure Migrate](./troubleshoot-appliance.md) pour rechercher les étapes de correction.