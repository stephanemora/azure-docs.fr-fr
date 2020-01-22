---
title: Prise en charge pour l’évaluation de serveurs physiques à l’aide d’Azure Migrate
description: Découvrez la prise en charge pour l’évaluation de serveurs physiques à l’aide d’Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028765"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Tableau de prise en charge pour l’évaluation de serveurs physiques 

Vous pouvez utiliser le [service Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article récapitule les paramètres et les limites de la prise en charge de l’évaluation et la migration de serveurs physiques locaux.


## <a name="overview"></a>Vue d’ensemble

Pour évaluer des machines locales pour une migration vers Azure avec cet article, vous ajoutez l’outil Azure Migrate : Server Assessment à un projet Azure Migrate. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure. Après la découverte des machines, vous rassemblez les machines découvertes dans des groupes et effectuez l’évaluation d’un groupe

## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation**| Découvrez et évaluez jusqu’à 35 000 serveurs physiques dans un même [projet](migrate-support-matrix.md#azure-migrate-projects).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des machines virtuelles VMware et Hyper-V, et des serveurs physiques, jusqu’aux limites d’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 250 serveurs physiques.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35 000 machines par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.




## <a name="physical-server-requirements"></a>Conditions requises des serveurs physiques

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement de serveur physique**       | Le serveur physique peut être autonome ou déployé dans un cluster. |
| **autorisations**           | **Windows :** Configurez un compte d’utilisateur local sur tous les serveurs Windows que vous souhaitez inclure dans la découverte. Le compte d’utilisateur doit être ajouté à ces groupes : utilisateurs du Bureau à distance, utilisateurs de l’Analyseur de performances et utilisateurs du journal des performances. <br/> **Linux :** Vous devez disposer d’un compte racine sur les serveurs Linux que vous souhaitez découvrir. |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) sont pris en charge, à l’exception des suivants :<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. L’appliance pour les serveurs physiques peut s’exécuter sur une machine virtuelle ou sur une machine physique. Vous la configurez à l’aide d’un script PowerShell que vous téléchargez à partir du portail Azure.

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---physical) pour les serveurs physiques.
- En savoir plus sur les [URL](migrate-appliance.md#url-access) auxquelles l’appliance doit accéder.

## <a name="port-access"></a>Accès au port

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ``` https://<appliance-ip-or-name>:44368 ```<br/> Connexions sortantes sur les ports 443, 5671 et 5672 pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**Serveurs physiques** | **Windows :** Connexions entrantes sur le port 443, les ports WinRM 5985 (HTTP) et 5986 (HTTPS) pour extraire les métadonnées de configuration et de performance des serveurs Windows. <br/> **Linux :**  Connexions entrantes sur le port 22 (UDP) pour extraire les métadonnées de configuration et de performance des serveurs Linux. |


## <a name="next-steps"></a>Étapes suivantes

[Préparer l’évaluation des serveurs physiques](tutorial-prepare-physical.md).
