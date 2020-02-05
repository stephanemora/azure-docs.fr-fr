---
title: Tableau de prise en charge de sauvegarde SAP HANA
description: Dans cet article,découvrez les scénarios et limitations pris en charge lorsque vous utilisez Sauvegarde Azure pour sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 82d844385290ab0dc2953537c1f9a3387dd7b2b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842629"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matrice de prise en charge pour la sauvegarde des bases de données SAP HANA sur des machines virtuelles Azure

Le service Sauvegarde Azure prend en charge la sauvegarde des bases de données SAP HANA sur Azure. Cet article résume les scénarios pris en charge et les limitations qui se présentent lorsque vous utilisez Sauvegarde Azure pour sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure.

## <a name="onboard-to-the-public-preview"></a>Intégration à la préversion publique

Exécutez la procédure d’intégration à la préversion publique ci-après :

* Dans le portail, inscrivez votre ID d’abonnement auprès du fournisseur de services Recovery Services en [suivant les instructions de cet article](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Pour PowerShell, exécutez cette cmdlet. Ce processus doit s’achever en présentant la mention « Inscrit ».

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> La fréquence de sauvegarde de fichier journal peut désormais être définie sur un minimum de 15 minutes. Les sauvegardes de fichiers journaux ne commencent à s’effectuer qu’en cas de réussite d’une sauvegarde complète de la base de données.

## <a name="scenario-support"></a>Prise en charge du scénario

| **Scénario**               | **Configurations prises en charge**                                | **Configurations non prises en charge**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | SAP HANA s’exécutant sur des machines virtuelles Azure Linux uniquement                    | Grandes instances HANA (HLI)                                   |
| **Zones géographiques**                   | **Disponibilité générale :**<br />**Europe** – Europe Ouest, Europe Nord, France Centre, Royaume-Uni Sud, Royaume-Uni Ouest, Allemagne Nord, Allemagne Centre-Ouest, Suisse Nord, Suisse Ouest<br />**Asie-Pacifique** – Australie Centre, Australie Centre 2, Australie Est, Australie Sud-Est, Japon Est, Japon Ouest, Corée Centre, Corée Sud<br /><br>**Préversion :**<br />**Amériques**  – USA Centre, USA Est 2, USA Est, USA Centre Nord, USA Centre Sud, USA Ouest 2, USA Centre-Ouest, USA Ouest, Canada Centre, Canada Est <br />**Asie-Pacifique** – Asie Est, Asie Sud-Est, Inde Centre, Inde Sud | Chine Est, Chine Nord, Chine Est 2, Chine Nord 2, Inde Ouest, Suisse Nord, Afrique du Sud Nord, Afrique du Sud Nord, Afrique du Sud Ouest, Émirats arabes unis Nord, Émirats arabes unis Centre, régions Azure Government, France Sud, Brésil Sud |
| **Versions du système d’exploitation**            | SLES 12 avec SP2, SP3 ou SP4                                | SLES 15, RHEL                                                |
| **Versions HANA**          | SDC sur HANA 1.x, MDC sur HANA 2.x  <= SPS04 rév. 44            | -                                                            |
| **Déploiements HANA**       | SAP HANA sur une machine virtuelle Azure unique - Montée en puissance uniquement               | Montée en charge                                                    |
| **Instances HANA**         | Une seule SAP HANA instance sur une seule machine virtuelle Azure - montée en puissance uniquement | Plusieurs instances de SAP HANA sur une seule machine virtuelle                  |
| **Types de bases de données HANA**    | Conteneur de base de données unique (SDC) sur 1.x, conteneur de bases de données multiples (MDC) sur 2.x | MDC dans HANA 1. x                                              |
| **Taille de la base de données HANA**     | Taille de sauvegarde complète de 2 To, comme indiquée par HANA                   |                                                              |
| **Types de sauvegarde**           | Sauvegardes complètes, différentielles et de fichier journal                          | Incrémentielle, instantanés                                       |
| **Types de restaurations**          | Reportez-vous à la note SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) pour connaître les types de restauration pris en charge |                                                              |
| **Limites de sauvegarde**          | Jusqu’à 2 To de taille de sauvegarde complète par instance de SAP HANA         |                                                              |
| **Configurations spéciales** |                                                              | SAP HANA + Dynamic Tiering <br>  Clonage via LaMa        |

------

> [!NOTE]
> Les opérations de sauvegarde et de restauration à partir de clients natifs SAP HANA (SAP HANA Studio/Cockpit/DBA Cockpit) ne sont pas prises en charge actuellement.



## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Sauvegarder des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Découvrez comment [Restaurer des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Découvrez comment [gérer des bases de données SAP HANA sauvegardées à l’aide de Sauvegarde Azure](sap-hana-db-manage.md)
* Découvrez comment [résoudre les problèmes courants lors de la sauvegarde de bases de données SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
