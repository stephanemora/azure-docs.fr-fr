---
title: FAQ sur Azure Automation | Microsoft Docs
description: Cet article contient des réponses aux questions les plus fréquemment posées sur Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e9168f7ef3cf49f4c13fdc67807061f23b6b402
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984011"
---
# <a name="azure-automation-frequently-asked-questions"></a>Questions fréquemment posées sur Azure Automation

Ce FAQ Microsoft est une liste de questions fréquemment posées concernant Azure Automation. Si vous avez d’autres questions sur ses capacités, rendez-vous sur le forum de discussion et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="update-management"></a>Update Management

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Puis-je empêcher les mises à niveau inattendues au niveau du système d’exploitation ?

Sur certaines variantes Linux, comme Red Hat Enterprise Linux, les mises à niveau du système d’exploitation peuvent se produire par le biais de packages. Celles-ci peuvent alors entraîner des exécutions Update Management dans lesquelles le numéro de version du système d’exploitation change. Étant donné que la solution Update Management utilise les mêmes méthodes pour mettre à jour des packages qu’un administrateur sur une machine Linux locale, ce comportement est volontaire.

Pour éviter de mettre à jour la version du système d’exploitation par le biais de déploiements Update Management, utilisez la fonctionnalité **Exclusion**.

Dans Red Hat Enterprise Linux, le nom du package à exclure est `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Pourquoi les mises à jour critiques et de sécurité ne sont-elles pas appliquées ?

Lorsque vous déployez des mises à jour sur un ordinateur Linux, vous pouvez sélectionner des classifications. Cette option filtre les mises à jour qui remplissent les critères de classification spécifiés. Ce filtre est appliqué localement sur l’ordinateur lorsque la mise à jour est déployée.

Comme Update Management enrichit les mises à jour dans le cloud, vous pouvez signaler certaines mises à jour dans Update Management comme ayant un impact sur la sécurité quand bien même l’ordinateur local n’a pas ces informations. Si vous appliquez des mises à jour critiques à un ordinateur Linux, certaines mises à jour, non signalées comme ayant un impact sur la sécurité pour cet ordinateur, peuvent ne pas être appliquées. Toutefois, Update Management peut quand même signaler cet ordinateur comme étant non conforme, car il contient des informations supplémentaires sur la mise à jour concernée.

Le déploiement de mises à jour par classification ne fonctionne pas sur les versions RTM de CentOS. Pour déployer correctement les mises à jour pour CentOS, sélectionnez toutes les classifications pour garantir que les mises à jour sont appliquées. Pour SUSE, le fait de sélectionner UNIQUEMENT **Autres mises à jour** en tant que classification peut installer d’autres mises à jour de sécurité si elles sont associées à zypper (gestionnaire de package) ou à ses dépendances doivent être d’abord installées. Il s’agit d’une limitation de zypper. Dans certains cas, vous aurez peut-être à réexécuter le déploiement des mises à jour, puis à vérifier le déploiement via le journal des mises à jour.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Puis-je déployer des mises à jour dans tous les locataires Azure ?

Si des machines devant être mises à jour se trouvent dans un autre rapport de tenant Azure pour Update Management, vous devez utiliser une solution de contournement pour planifier l’opération. Vous pouvez utiliser la cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) avec le paramètre `ForUpdateConfiguration` pour créer une planification. Vous pouvez utiliser la cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) et transférer les ordinateurs de l’autre locataire vers le paramètre `NonAzureComputer`. L’exemple suivant vous montre comment procéder.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automatisation du processus - Runbooks Python

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Quelle est la version de Python 3 prise en charge dans Azure Automation ?

Pour les tâches cloud, Python 3.8 est pris en charge. Les scripts et les packages de toutes les versions 3.x peuvent fonctionner si le code est compatible avec différentes versions.

Pour les tâches hybrides sur des Runbook Worker hybrides Windows, vous pouvez choisir d’installer la version 3.x que vous souhaitez utiliser. Pour les travaux hybrides sur des Runbook Worker hybrides Linux, nous dépendons de la version Python 3 installée sur la machine pour exécuter DSC OMSConfig et le Worker hybride Linux. Nous vous recommandons d’installer la version 3.6. Toutefois, d’autres versions devraient également fonctionner si aucune modification ne vient casser le mécanisme des signatures de méthode ou de contrats entre les versions de Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Les runbooks Python 2 et Python 3 peuvent-ils s’exécuter dans le même compte Automation ?

Oui, il n’existe aucune limitation à l’utilisation de runbooks Python 2 et Python 3 dans le même compte Automation.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Quel est le plan de migration des packages et runbooks Python 2 existants vers Python 3 ?

Azure Automation ne prévoit pas de migrer les packages et runbooks Python 2 vers Python 3. Vous devez effectuer cette migration vous-même. Les runbooks et packages Python 2 existants comme les nouveaux continueront à fonctionner.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Quels sont les packages pris en charge par défaut dans l’environnement Python 3 ?

Le package Azure 4.0.0 est installé par défaut dans l’environnement d’automatisation Python 3. Vous pouvez importer manuellement une version plus récente du package Azure pour remplacer la version par défaut.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>Que se passe-t-il si j’exécute un runbook Python 3 qui référence un package Python 2 ou inversement ?

Python 2 et Python 3 utilisent des environnements d’exécution différents. Lors de l’exécution d’un runbook Python 2, seuls les packages Python 2 peuvent être importés. C’est la même chose en ce qui concerne Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Comment faire la différence entre les packages et runbooks Python 2 et Python 3 ?

Python 3 est une nouvelle définition de runbook qui fait la distinction entre les runbooks Python 2 et Python 3. De même, un autre type de package est introduit pour les packages Python 3.

### <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>Comment un Runbook Worker hybride sait-il quelle version de Python exécuter lorsque Python 2 et Python 3 sont installés ?

Pour un Runbook Worker Windows, lors de l’exécution d’un Runbook Python 2, il recherche d’abord la variable d’environnement `PYTHON_2_PATH` et vérifie si elle pointe vers un fichier exécutable valide. Par exemple, si le dossier d’installation est `C:\Python2`, il vérifie si `C:\Python2\python.exe` correspond à un chemin d’accès valide. Si ce n’est pas le cas, il recherche la variable d’environnement `PATH` pour effectuer une vérification similaire.

Pour Python 3, il recherche d'abord la variable d’environnement `PYTHON_3_PATH`, puis revient à la variable d’environnement `PATH`.

Lorsque vous utilisez une seule version de Python, vous pouvez ajouter le chemin d’installation à la variable `PATH`. Si vous souhaitez utiliser les deux versions sur le Runbook Worker, définissez `PYTHON_2_PATH` et `PYTHON_3_PATH` sur l’emplacement du module correspondant à ces versions.

### <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>Comment un Runbook Worker hybride localise-t-il l’interpréteur Python ?

La localisation du module Python est contrôlée par les variables d’environnement, comme expliqué précédemment.

### <a name="is-python-3-supported-in-source-control"></a>Python 3 est-il pris en charge dans le contrôle de code source ?

Non. Actuellement, le contrôle de code source n’est pas pris en charge pour Python 3. Par défaut, les runbooks Python sont synchronisés en tant que runbooks Python 2.

### <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Comment un auteur de runbook peut-il connaître les packages Python disponibles dans un bac à sable Azure ?

Utilisez le code suivant pour répertorier les modules installés par défaut :

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

### <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>Comment un auteur de runbook peut-il définir la version d’un module de package à utiliser s’il existe plusieurs modules ?

La version par défaut peut être remplacée en important les packages Python dans le compte Automation. La préférence est donnée à la version importée dans le compte Automation.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne trouvez pas de réponse à votre question ici, vous pouvez consulter les sources suivantes pour plus de questions et réponses.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Forum de commentaires](https://feedback.azure.com/forums/905242-update-management)
