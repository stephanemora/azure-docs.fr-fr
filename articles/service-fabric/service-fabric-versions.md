---
title: Versions de cluster prises en charge dans Azure Service Fabric
description: Découvrez les versions de cluster dans Azure Service Fabric, notamment un lien vers les versions les plus récentes à partir du blog de l’équipe Service Fabric.
ms.topic: troubleshooting
ms.date: 02/05/2020
ms.openlocfilehash: 0950c93c09c89ad92fc4f77f9f82937deb9e489e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064850"
---
# <a name="supported-service-fabric-versions"></a>Versions de Service Fabric prises en charge

Assurez-vous que votre cluster exécute toujours une version prise en charge d’Azure Service Fabric. La prise en charge de la version précédente se termine sous 60 jours après que nous annonçons le lancement d’une nouvelle version d’Azure Service Fabric. Les nouvelles versions sont annoncées sur le [blog de l’équipe Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Consultez les documents suivants pour savoir plus en détail comment faire en sorte que votre cluster exécute toujours une version prise en charge de Service Fabric :

- [Mettre à niveau un cluster Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Mettre à niveau la version de Service Fabric qui s’exécute sur votre cluster Windows Server autonome](service-fabric-cluster-upgrade-windows-server.md)

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
| 6.5.* | 6.4.617.* |Inférieure ou égale à la version 3.4 |1 mai 2020 |
| 7.0.* | 6.4.664.# |Inférieure ou égale à la version 4.0 |Version actuelle ; par conséquent, pas de date de fin |
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

## <a name="supported-version-names"></a>Noms des versions prises en charge

Le tableau suivant répertorie les noms de version de Service Fabric et leurs numéros de version correspondants.

| Nom de version | Numéro de version de Windows | Numéro de version de Linux |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | N/D |
| 5.3 CU1 | 5.3.204.9494 | N/D |
| 5.3 CU2 | 5.3.301.9590 | N/D |
| 5.3 CU3 | 5.3.311.9590 | N/D |
| 5.4 CU2 | 5.4.164.9494 | N/D |
| 5.5 CU1 | 5.5.216.0    | N/D |
| 5.5 CU2 | 5.5.219.0    | N/D |
| 5.5 CU3 | 5.5.227.0    | N/D |
| 5.5 CU4 | 5.5.232.0    | N/D |
| 5.6 RTO | 5.6.204.9494 | N/D |
| 5.6 CU2 | 5.6.210.9494 | N/D |
| 5.6 CU3 | 5.6.220.9494 | N/D |
| 5.7 RTO | 5.7.198.9494 | N/D |
| 5.7 CU4 | 5.7.221.9494 | N/D |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | N/D |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | N/D |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | N/D |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | N/D |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |