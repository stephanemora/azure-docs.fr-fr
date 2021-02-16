---
title: Mettre à niveau la version de votre cluster Azure Service Fabric
description: Découvrez les versions de cluster dans Azure Service Fabric, notamment un lien vers les versions les plus récentes à partir du blog de l’équipe Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 5abfe83fcb68fcab7df22f1fd266cc695f2b9c80
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549066"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Mettre à niveau la version de votre cluster Azure Service Fabric

Assurez-vous que votre cluster exécute toujours une version prise en charge d’Azure Service Fabric. La prise en charge de la version précédente se termine sous 60 jours après que nous annonçons le lancement d’une nouvelle version d’Azure Service Fabric. Les nouvelles versions sont annoncées sur le [blog de l’équipe Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Pour chaque version du runtime de Service Fabric, vous pouvez utiliser les versions spécifiées ou antérieures des packages SDK/NuGet. Les versions plus récentes des packages peuvent ne pas être en mesure de cibler les clusters plus anciens. Les clusters plus anciens peuvent avoir des modifications de fonctionnalités ou de protocoles que les environnements de package plus récents ne prennent pas en charge.

Consultez les articles suivants pour savoir plus en détail comment faire en sorte que votre cluster exécute toujours une version prise en charge de Service Fabric :

- [Mettre à niveau un cluster Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Mettre à niveau la version de Service Fabric qui s’exécute sur votre cluster Windows Server autonome](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Versions non prises en charge

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Alerte de mise à niveau pour les versions entre 5.7 et 6.3.63.*

Pour améliorer la sécurité et la disponibilité, l’infrastructure Azure a apporté une modification susceptible d’avoir un impact sur les clients de Service Fabric. Cette modification concerne tous les clusters Service Fabric exécutant les versions 5.7 à 6.3.

Une mise à jour du runtime Service Fabric est disponible pour toutes les versions de Service Fabric prises en charge dans l’ensemble des régions. Effectuez une mise à niveau vers l’une des versions les plus récentes prises en charge d’ici le 19 janvier 2021 pour éviter les interruptions de service.

Si vous disposez d’un plan de support et que vous avez besoin d’une aide technique, contactez-nous via les canaux du support Azure. Ouvrez une demande de support pour Azure Service Fabric et mentionnez ce contexte dans le ticket de support.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Si vous n’effectuez pas la mise à niveau vers une version prise en charge

Les clusters Azure Service Fabric qui s’exécutent sur les versions 5.7 à 6.3.63.* ne seront pas disponibles si vous n’avez pas effectué la mise à niveau avant le 19 janvier 2021.

#### <a name="required-action"></a>Action requise

Effectuez une mise à niveau vers une version prise en charge de Service Fabric pour éviter les temps d’arrêt ou la perte de fonctionnalités liées à cette modification. Vérifiez que vos clusters exécutent au minimum les versions suivantes pour éviter tout problème au sein de votre environnement.

> [!Note]
> **Toutes les mises en production de la version 7.2 incluent les modifications nécessaires.**
  
  | Système d’exploitation | Runtime Service Fabric actuel dans le cluster | Date de publication du CU/correctif |
  | --- | --- |--- |
  | Windows | 7.0.* | 7.0.478.9590 |
  | Windows | 7.1.* | 7.1.503.9590 |
  | Windows | 7.2.* | 7.2.* |
  | Ubuntu 16 | 7.0.* | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7.1.* | 7.1.455.1  |
  | Linux Ubuntu 18.04 | 7.1.* | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7.2.* | 7.2.* |
  | Linux Ubuntu 18.04 | 7.2.* | 7.2.* |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Alerte de mise à niveau pour les versions ultérieures à la version 6.3

Pour améliorer la sécurité et la disponibilité, l’infrastructure Azure a apporté une modification susceptible d’avoir un impact sur les clients de Service Fabric. Cette modification concerne tous les clusters Service Fabric qui utilisent le [mode de mise en réseau Ouvert pour les conteneurs](./service-fabric-networking-modes.md#set-up-open-networking-mode) et exécutent les versions 6.3 à 7.0 ou des versions non compatibles prises en charge ultérieures à la version 7.0. Une mise à jour du runtime Service Fabric est disponible pour toutes les versions de Service Fabric prises en charge dans l’ensemble des régions.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Si vous n’effectuez pas la mise à niveau vers une version prise en charge

Les clusters Azure Service Fabric qui s’exécutent sur des versions inchangées ultérieures à la version 6.3 subiront une perte de fonctionnalités ou des interruptions de service s’ils n’ont pas été mis à niveau vers une version prise en charge avant le 19 janvier 2021.
  
  - **Pour les clusters exécutant une version de Service Fabric ultérieure à la version 6.3 n’utilisant PAS la fonctionnalité de mise en réseau Ouvert**, le cluster reste actif.

 - **Pour les clusters exécutant une version de Service Fabric ultérieure à la version 6.3 et utilisant la [fonctionnalité de mise en réseau ouvert pour les conteneurs](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)** , le cluster peut devenir indisponible et cesser de fonctionner, ce qui peut entraîner des interruptions de service pour vos charges de travail.
 
 -   **Pour les clusters exécutant une [version de Windows entre les versions 7.0.457 et 7.0.466 (incluses)](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-version-names) et le système d’exploitation Windows, la fonctionnalité de conteneurs Windows est activée. REMARQUE : Les versions 7.0.457, 7.0.464 et 7.0.465 de Linux ne sont PAS concernées**.
    - **Impact** : Le cluster cesse de fonctionner, ce qui peut entraîner des interruptions de service pour vos charges de travail.
    
#### <a name="required-action"></a>Action requise

Pour éviter les temps d’arrêt ou la perte de fonctionnalité, assurez-vous que vos clusters exécutent l’une des versions suivantes.

Les versions de Service Fabric dans le tableau contiennent les modifications nécessaires pour empêcher la perte de fonctionnalités. Assurez-vous que vous utilisez l’une de ces versions.  

> [!Note]
> **Les clusters Azure Service Fabric s’exécutant sur la version 6.5 doivent effectuer plusieurs mises à niveau en même temps avant que l’infrastructure ne soit modifiée pour éviter la perte de fonctionnalités dans le cluster**. 
>   -   1. Mettre à niveau vers 7.0.466. **Les clusters exécutant le système d’exploitation Windows pour lequel la fonctionnalité de conteneur Windows est activée ne peuvent PAS être sur cette version intermédiaire. Ils doivent effectuer l’étape suivante (ii) ci-dessous, à savoir Mise à niveau vers une version plus sûre et conforme afin d’éviter les interruptions de service**
>   -   2. Effectuez une mise à niveau vers les versions les plus récentes de la version 7.0* (7.0.478) ou de l’une des versions supérieures listées ci-dessous.


> [!Note]
> **Toutes les mises en production de la version 7.2 incluent les modifications nécessaires**.

 | Système d’exploitation | Runtime Service Fabric actuel dans le cluster | Date de publication du CU/correctif |
  | --- | --- |--- |
  | Windows | 7.0.* | 7.0.478.9590 |
  | Windows | 7.1.* | 7.1.503.9590 |
  | Windows | 7.2.* | 7.2.* |
  | Linux Ubuntu 16.04 | 7.0.* | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7.1.* | 7.1.455.1  |
  | Linux Ubuntu 18.04 | 7.1.* | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7.2.* | 7.2.* |
  | Linux Ubuntu 18.04 | 7.2.* | 7.2.* |

## <a name="supported-versions"></a>Versions prises en charge

Le tableau suivant répertorie les versions de Service Fabric et leurs dates de fin de prise en charge.

| Runtime Service Fabric dans le cluster | Version de cluster à partir de laquelle une mise à niveau directe est possible |Version du kit de développement logiciel (SDK)/du package NuGet compatible | Fin de la prise en charge |
| --- | --- |--- | --- |
| Toutes les versions de cluster antérieures à 5.3.121 | 5.1.158.* |Inférieure ou égale à la version 2.3 |20 janvier 2017 |
| 5.3.* | 5.1.158.* |Inférieure ou égale à la version 2.3 |24 février 2017 |
| 5.4.* | 5.1.158.* |Inférieure ou égale à la version 2.4 |10 mai 2017       |
| 5.5.* | 5.4.164.* |Inférieure ou égale à la version 2.5 |10 août 2017    |
| 5.6.* | 5.4.164.* |Inférieure ou égale à la version 2.6 |13 octobre 2017   |
| 5.7.* | 5.4.164.* |Inférieure ou égale à la version 2.7 |15 décembre 2017  |
| 6.0.* | 5.6.205.* |Inférieure ou égale à la version 2.8 |30 mars 2018     |
| 6.1.* | 5.7.221.* |Inférieure ou égale à la version 3.0 |15 juillet 2018      |
| 6.2.* | 6.0.232.* |Inférieure ou égale à la version 3.1 |26 octobre 2018   |
| 6.3.* | 6.1.480.* |Inférieure ou égale à la version 3.2 |31 mars 2019  |
| 6.4.* | 6.2.301.* |Inférieure ou égale à la version 3.3 |15 septembre 2019 |
| 6.5.* | 6.4.617.* |Inférieure ou égale à la version 3.4 |1er août 2020 |
| 7.0.466.* | 6.4.664.* |Inférieure ou égale à la version 4.0|31 janvier 2021  |
| 7.0.466.* | 6.5.* |Inférieure ou égale à la version 4.0|31 janvier 2021 |
| 7.0.470.* | 7.0.466.* |Inférieure ou égale à la version 4.0 |31 janvier 2021  |
| 7.0.472.* | 7.0.466.* |Inférieure ou égale à la version 4.0 |31 janvier 2021  |
| 7.0.478.* | 7.0.466.* |Inférieure ou égale à la version 4.0 |31 janvier 2021  |
| 7.1.409.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.417.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.428.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.456.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.458.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.459.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.503.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.1.510.* | 7.0.466.* |Inférieure ou égale à la version 4.1 |31 mars 2021 |
| 7.2.413.* | 7.0.470.* |Inférieure ou égale à la version 4.2 |Version actuelle ; par conséquent, pas de date de fin |
| 7.2.432.* | 7.0.470.* |Inférieure ou égale à la version 4.2 |Version actuelle ; par conséquent, pas de date de fin |
| 7.2.433.* | 7.0.470.* |Inférieure ou égale à la version 4.2 |Version actuelle ; par conséquent, pas de date de fin |
| 7.2.445.* | 7.0.470.* |Inférieure ou égale à la version 4.2 |Version actuelle ; par conséquent, pas de date de fin |
| 7.2.452.* | 7.0.470.* |Inférieure ou égale à la version 4.2 |Version actuelle ; par conséquent, pas de date de fin |

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau suivant répertorie les systèmes d’exploitation pris en charge pour les versions prises en charge de Service Fabric.

| Système d’exploitation | Version prise en charge de Service Fabric la plus ancienne |
| --- | --- |
| Windows Server 2012 R2 | Toutes les versions |
| Windows Server 2016 | Toutes les versions |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |
| Linux Ubuntu 18.04 | 7.1 |

## <a name="supported-version-names"></a>Noms des versions prises en charge

Le tableau suivant répertorie les noms de version de Service Fabric et leurs numéros de version correspondants.

| Nom de version | Numéro de version de Windows | Numéro de version de Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | Non applicable|
| 5.3 CU1 | 5.3.204.9494 | Non applicable|
| 5.3 CU2 | 5.3.301.9590 | Non applicable|
| 5.3 CU3 | 5.3.311.9590 | Non applicable|
| 5.4 CU2 | 5.4.164.9494 | Non applicable|
| 5.5 CU1 | 5.5.216.0    | Non applicable|
| 5.5 CU2 | 5.5.219.0 | Non applicable|
| 5.5 CU3 | 5.5.227.0 | Non applicable|
| 5.5 CU4 | 5.5.232.0 | Non applicable|
| 5.6 RTO | 5.6.204.9494 | Non applicable|
| 5.6 CU2 | 5.6.210.9494 | Non applicable|
| 5.6 CU3 | 5.6.220.9494 | Non applicable|
| 5.7 RTO | 5.7.198.9494 | Non applicable|
| 5.7 CU4 | 5.7.221.9494 | Non applicable|
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | Non applicable|
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | Non applicable|
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | Non applicable|
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | Non applicable|
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.1 CU10 | 7.1.510.9590 | N/D |
| 7.2 RTO | 7.2.413.9590 | NA |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 CU3 | 7.2.433.9590 | Non applicable|
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7.2 CU5 | 7.2.452.9590 | 7.2.454.1 |
