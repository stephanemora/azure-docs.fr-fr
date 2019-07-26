---
title: Résolution des problèmes de sauvegarde lente de fichiers et de dossiers dans Azure Backup
description: Apporte des conseils visant à vous aider à diagnostiquer la cause des problèmes de performances d’Azure Backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 3a39d39412c8b64d1851ea0fc9511d116f3b232a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465336"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Résolution des problèmes de sauvegarde lente de fichiers et de dossiers dans Azure Backup
Cet article apporte des conseils visant à vous aider à diagnostiquer la cause du ralentissement des performances de sauvegarde des fichiers et des dossiers avec Azure Backup. Lorsque vous utilisez l’agent Azure Backup pour sauvegarder des fichiers, le processus de sauvegarde peut prendre plus longtemps que prévu. Ce ralentissement peut être dû à une ou plusieurs des raisons suivantes :

* [Des goulots d’étranglement de performances ralentissent l’ordinateur en cours de sauvegarde.](#cause1)
* [Un autre processus ou logiciel antivirus interfère avec le processus Azure Backup.](#cause2)
* [L’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure](#cause3)  
* [Vous sauvegardez un grand nombre de fichiers (plusieurs millions).](#cause4)

Avant de commencer à résoudre le problème, nous vous recommandons de télécharger et d’installer le [dernier agent Azure Backup](https://aka.ms/azurebackup_agent). Nous apportons des mises à jour fréquentes à l’agent de sauvegarde afin de résoudre différents problèmes, d’ajouter des fonctionnalités et d’améliorer les performances.

Par ailleurs, nous vous recommandons vivement de passer en revue le [Forum aux questions sur le service Azure Backup](backup-azure-backup-faq.md) pour vous assurer que vous ne rencontrez pas des problèmes de configuration courants.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Cause : goulots d'étranglement au niveau des performances sur l'ordinateur
La présence de goulots d’étranglement sur l’ordinateur en cours de sauvegarde peut engendrer des retards. Par exemple, la capacité de l’ordinateur à lire le disque ou à écrire dessus ou encore la disponibilité de la bande passante pour l’envoi de données peuvent créer des goulots d’étranglement.

Windows fournit un outil intégré appelé [Analyseur de performances](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Performance Monitor ou Perfmon) pour détecter les goulots d’étranglement.

Voici quelques compteurs de performances et plages qui peuvent être utiles pour diagnostiquer les goulots d’étranglement et permettre des sauvegardes optimales.

| Compteur | Statut |
| --- | --- |
| Disque logique (disque physique)--% temps d’inactivité |• 100 % à 50 % d’inactivité = sain</br>• 49 % à 20 % d’inactivité = avertissement ou analyse</br>• 19 % à 0 % d’inactivité = critique ou hors spécifications |
| Disque logique (disque physique)--% moy. disque s/lecture ou disque s/écriture |• 0,001 ms à 0,015 ms = sain</br>• 0,015 ms à 0,025 ms = avertissement ou analyse</br>• 0,026 ms ou plus = critique ou hors spécifications |
| Disque logique (disque physique)--Taille de file d’attente du disque actuelle (pour toutes les instances) |80 demandes pendant plus de 6 minutes |
| Mémoire--Octets de réserve non paginée |• Moins de 60 % de la réserve utilisée = sain<br>• 61 à 80 % du pool consommé = avertissement ou analyse</br>• Plus de 80 % de la réserve utilisée = critique ou hors spécifications |
| Mémoire--Octets de réserve paginée |• Moins de 60 % de la réserve utilisée = sain</br>• 61 à 80 % du pool consommé = avertissement ou analyse</br>• Plus de 80 % de la réserve utilisée = critique ou hors spécifications |
| Mémoire--Mégaoctets disponibles |• 50 % de mémoire disponible ou plus = sain</br>• 25 % de mémoire disponible = analyse</br>• 10 % de mémoire disponible = avertissement</br>• Moins de 100 Mo ou 5 % de mémoire disponible = critique ou hors spécifications |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processeur--\`temps processeur (toutes les instances)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues

author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Moins de 60 % utilisés = sain</br>• 61 à 90 % utilisés = analyse ou attention</br>• 91 % à 100 % utilisés = critique |

> [!NOTE]
> Si vous déterminez que l’infrastructure est la cause du problème, nous vous recommandons de défragmenter les disques régulièrement pour de meilleures performances.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Cause : un autre processus ou logiciel antivirus interfère avec Sauvegarde Azure
Nous avons vu plusieurs exemples dans lesquels d’autres processus au sein du système Windows ont dégradé les performances du processus de l’agent Azure Backup. Par exemple, si vous utilisez l’agent Azure Backup et un autre programme pour sauvegarder des données ou si un logiciel antivirus est en cours d’exécution et qu’il verrouille les fichiers à sauvegarder, plusieurs verrouillages sur les fichiers peuvent provoquer des conflits. Dans ce cas, la sauvegarde risque d’échouer ou l’opération peut prendre plus longtemps que prévu.

La meilleure recommandation dans ce scénario consiste à désactiver l’autre programme de sauvegarde pour déterminer si le temps de sauvegarde de l’agent Azure Backup évolue. En règle générale, s’assurer que plusieurs opérations de sauvegarde n’interfèrent pas l’une sur l’autre est suffisant pour empêcher les conflits.

Pour les programmes antivirus, nous vous recommandons d’exclure les fichiers et emplacements suivants :

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Nouvel emplacement (si l’emplacement standard n’est pas utilisé)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Cause : l'agent de sauvegarde est en cours d'exécution sur une machine virtuelle Azure
Si vous exécutez l’agent de sauvegarde sur une machine virtuelle, les performances seront plus lentes par rapport à une exécution sur un ordinateur physique. Cela s’explique par les limitations liées aux opérations d’E/S par seconde.  Toutefois, vous pouvez optimiser les performances en basculant les lecteurs de données sauvegardées vers un stockage premium Azure. Nous travaillons à la résolution de ce problème. Le correctif sera disponible dans une version ultérieure.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Cause : vous sauvegardez un grand nombre de fichiers (plusieurs millions)
Le déplacement d’un volume important de données prend plus de temps que le déplacement d’un volume plus restreint de données. Dans certains cas, la durée de la sauvegarde dépend non seulement de la taille des données, mais également du nombre de fichiers ou de dossiers. Cela se vérifie plus particulièrement lorsque des millions de petits fichiers (de quelques octets à quelques kilo-octets) sont sauvegardés.

Ce comportement se produit parce qu’Azure catalogue vos fichiers pendant que vous sauvegardez les données et les déplacez dans Azure. Dans certains cas rares, l’opération de catalogage peut prendre plus longtemps que prévu.

Les indicateurs suivants peuvent vous aider à identifier le goulot d’étranglement et appliquer en conséquence les étapes suivantes :

* **L’interface utilisateur affiche la progression du transfert de données**. Les données sont toujours en cours de transfert. La bande passante réseau ou la taille des données engendrent peut-être un ralentissement.
* **L’interface utilisateur n’affiche pas la progression du transfert de données**. Ouvrez les journaux d’activité sous C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, puis recherchez l’entrée FileProvider::EndData dans les journaux d’activité. Cette entrée signifie que le transfert de données est terminé et que l’opération de catalogage est en cours. N’annulez pas les tâches de sauvegarde. Attendez un peu plus longtemps que l’opération de catalogage soit terminée. Si le problème persiste, contactez le [support Azure](https://portal.azure.com/#create/Microsoft.Support).
