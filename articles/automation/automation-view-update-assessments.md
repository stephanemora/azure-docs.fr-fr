---
title: Affichage des évaluations des mises à jour Azure Automation Update Management
description: Cet article explique comment voir les évaluations des mises à jour pour Update Management.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617386"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Affichage des évaluations des mises à jour Azure Automation Update Management

Dans votre compte Azure Automation, sélectionnez **Update Management** pour voir l’état de vos machines.

Cet affichage fournit des informations sur vos ordinateurs, les mises à jour manquantes, les déploiements de mises à jour et les déploiements de mises à jour planifiés. Dans la colonne **CONFORMITÉ**, vous pouvez voir quand l’ordinateur a été évalué pour la dernière fois. Dans la colonne **METTRE À JOUR LA DISPONIBILITÉ DE L’AGENT**, vous pouvez voir l’intégrité de l’agent de mise à jour. En cas de problème, sélectionnez le lien pour accéder à la documentation de résolution des problèmes, qui peut vous aider à remédier au problème.

Pour exécuter une recherche dans les journaux qui permet de retourner des informations sur l’ordinateur, la mise à jour ou le déploiement, sélectionnez l’élément correspondant dans la liste. Le volet Recherche dans les journaux s’ouvre sur une requête portant sur l’élément sélectionné.

![Vue par défaut de Update Management](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Afficher les mises à jour manquantes

Sélectionnez **Mises à jour manquantes** pour afficher la liste des mises à jour qui manquent à vos ordinateurs. Chaque mise à jour est répertoriée et peut être sélectionnée. Les informations sur le nombre de machines qui nécessitent la mise à jour, les détails sur le système d’exploitation et un lien permettant d’obtenir plus d’informations sont tous affichés. Le volet Recherche dans les journaux présente également d’autres informations sur les mises à jour.

![Mises à jour manquantes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classifications des mises à jour

Les tableaux suivants répertorient les classifications des mises à jour prises en charge dans Update Management, avec une définition de chaque classification.

### <a name="windows"></a> Windows

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques     | Mises à jour relatives à des problèmes spécifiques concernant des bogues critiques non liés à la sécurité.        |
|Mises à jour de sécurité     | Mises à jour relatives à des problèmes de sécurité propres à un produit.        |
|Correctifs cumulatifs     | Ensembles de correctifs logiciels regroupés pour faciliter leur déploiement.        |
|Packs de fonctionnalités     | Nouvelles fonctionnalités de produit distribuées en dehors d’une version de produit.        |
|Service Packs     | Ensembles de correctifs logiciels appliqués à une application.        |
|Mises à jour de définitions     | Mises à jour de fichiers de définition de virus ou autre.        |
|Outils     | Utilitaires ou fonctionnalités permettant d’effectuer une ou plusieurs tâches.        |
|Mises à jour     | Mises à jour d’applications ou de fichiers actuellement installés.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|classification ;  |Description  |
|---------|---------|
|Mises à jour critiques et de sécurité     | Mises à jour pour un problème spécifique ou un problème de sécurité propre à un produit.         |
|Autres mises à jour     | Toutes les autres mises à jour qui ne sont ni critiques par nature, ni des mises à jour de sécurité.        |

Pour Linux, Update Management peut faire la différence entre les mises à jour critiques et les mises à jour de sécurité dans le cloud, tout en affichant les données d’évaluation. (Cette précision est possible en raison de l’enrichissement des données dans le cloud.) Pour la mise à jour corrective, Update Management s’appuie sur les données de classification disponibles sur l’ordinateur. Contrairement à d’autres distributions, CentOS n’a pas accès à ces informations dans les versions RTM du produit. Si vous disposez de machines CentOS configurées pour retourner les données de sécurité de la commande suivante, Update Management peut appliquer la mise à jour corrective en fonction des classifications :

```bash
sudo yum -q --security check-update
```

Il n’existe actuellement aucune méthode prise en charge permettant d’activer la disponibilité des données de classification natives sur CentOS. Pour le moment, seule une prise en charge optimale est proposée aux clients qui ont activé eux-mêmes cette fonctionnalité.

Pour classifier les mises à jour sur Red Hat Enterprise version 6, vous devez installer le plug-in yum-security. Sur Red Hat Enterprise Linux 7, le plug-in faisant déjà partie de yum lui-même, il est inutile d'installer quoi que ce soit. Pour plus d'informations, consultez l'[article de base de connaissances](https://access.redhat.com/solutions/10021) Red Hat suivant.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez examiné les évaluations des mises à jour, vous pouvez planifier un déploiement des mises à jour en suivant les étapes décrites dans [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
