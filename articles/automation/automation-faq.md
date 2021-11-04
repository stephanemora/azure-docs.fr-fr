---
title: FAQ sur Azure Automation
description: Cet article contient des réponses aux questions les plus fréquemment posées sur Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a245c858c15a8a33b6347c86124a7b45f0c4290
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450891"
---
# <a name="azure-automation-frequently-asked-questions"></a>Questions fréquemment posées sur Azure Automation

Ce FAQ Microsoft est une liste de questions fréquemment posées concernant Azure Automation. Si vous avez d’autres questions sur ses capacités, rendez-vous sur le forum de discussion et publiez vos questions. Lorsqu’une question est fréquemment posée, nous l’ajoutons à cet article pour qu’elle soit facile et rapide à trouver.

## <a name="can-update-management-prevent-unexpected-os-level-upgrades"></a>Update Management peut-il empêcher les mises à niveau inattendues au niveau du système d’exploitation ?

Oui. Pour plus d’informations, consultez [Exclure des mises à jour](./update-management/manage-updates-for-vm.md#exclude-updates).

## <a name="why-arent-criticalsecurity-updates-applied-to-a-linux-machine-with-update-management"></a>Pourquoi les mises à jour critiques/de sécurité ne sont-elles pas appliquées à une machine Linux avec Update Management ?

Lorsque vous déployez des mises à jour sur un ordinateur Linux, vous pouvez sélectionner des classifications. Cette option filtre les mises à jour qui remplissent les critères de classification spécifiés. Pour plus d’informations, consultez [Classifications des mises à jour de Linux](./update-management/manage-updates-for-vm.md#linux-update-classifications).

## <a name="can-update-management-deploy-updates-across-azure-tenants"></a>Update Management peut-il déployer des mises à jour dans tous les locataires Azure ?

Oui. Pour obtenir des instructions, consultez [Déployer des mises à jour dans tous les locataires Azure](./update-management/deploy-updates.md#deploy-updates-across-azure-tenants).

## <a name="which-python-3-version-is-supported-in-azure-automation"></a>Quelle est la version de Python 3 prise en charge dans Azure Automation ?

Pour les tâches cloud, Python 3.8 est pris en charge. Les scripts et les packages de toutes les versions 3.x peuvent fonctionner si le code est compatible avec différentes versions.

Pour les tâches hybrides sur des Runbook Worker hybrides Windows, vous pouvez choisir d’installer la version 3.x que vous souhaitez utiliser. Pour les travaux hybrides sur des Runbook Worker hybrides Linux, nous dépendons de la version Python 3 installée sur la machine pour exécuter DSC OMSConfig et le Worker hybride Linux. Nous vous recommandons d’installer la version 3.6. Toutefois, d’autres versions devraient également fonctionner si aucune modification ne vient casser le mécanisme des signatures de méthode ou de contrats entre les versions de Python 3.

## <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Les runbooks Python 2 et Python 3 peuvent-ils s’exécuter dans le même compte Automation ?

Oui, il n’existe aucune limitation à l’utilisation de runbooks Python 2 et Python 3 dans le même compte Automation.  

## <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Quel est le plan de migration des packages et runbooks Python 2 existants vers Python 3 ?

Azure Automation ne prévoit pas de migrer les packages et runbooks Python 2 vers Python 3. Vous devez effectuer cette migration vous-même. Les runbooks et packages Python 2 existants comme les nouveaux continueront à fonctionner.

## <a name="what-packages-are-supported-by-default-in-python-3-environment"></a>Quels packages sont pris en charge par défaut dans l’environnement Python 3 ?

Azure package 4.0.0. Pour plus d’informations, consultez [Gérer des packagees Python 3](python-3-packages.md).

## <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>Que se passe-t-il si j’exécute un runbook Python 3 qui référence un package Python 2 ou inversement ?

Python 2 et Python 3 utilisent des environnements d’exécution différents. Lors de l’exécution d’un runbook Python 2, seuls les packages Python 2 peuvent être importés. C’est la même chose en ce qui concerne Python 3.

## <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Comment faire la différence entre les packages et runbooks Python 2 et Python 3 ?

Python 3 est une nouvelle définition de runbook qui fait la distinction entre les runbooks Python 2 et Python 3. De même, un autre type de package est introduit pour les packages Python 3.

## <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>Comment un Runbook Worker hybride sait-il quelle version de Python exécuter lorsque Python 2 et Python 3 sont installés ?

Pour un Runbook Worker Windows, lors de l’exécution d’un Runbook Python 2, il recherche d’abord la variable d’environnement `PYTHON_2_PATH` et vérifie si elle pointe vers un fichier exécutable valide. Par exemple, si le dossier d’installation est `C:\Python2`, il vérifie si `C:\Python2\python.exe` correspond à un chemin d’accès valide. Si ce n’est pas le cas, il recherche la variable d’environnement `PATH` pour effectuer une vérification similaire.

Pour Python 3, il recherche d'abord la variable d’environnement `PYTHON_3_PATH`, puis revient à la variable d’environnement `PATH`.

Consultez [Plusieurs versions de Python](automation-runbook-types.md#multiple-python-versions).

## <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>Comment un Runbook Worker hybride localise-t-il l’interpréteur Python ?

La localisation du module Python est contrôlée par les variables d’environnement, comme expliqué précédemment.

## <a name="is-python-3-supported-in-source-control"></a>Python 3 est-il pris en charge dans le contrôle de code source ?

Non. Actuellement, le contrôle de code source n’est pas pris en charge pour Python 3. Par défaut, les runbooks Python sont synchronisés en tant que runbooks Python 2.

## <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Comment un auteur de runbook peut-il connaître les packages Python disponibles dans un bac à sable Azure ?

Consultez [Identifier les packages disponibles dans le bac à sable](python-3-packages.md#identify-available-packages-in-sandbox).

## <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>Comment un auteur de runbook peut-il définir la version d’un module de package à utiliser s’il existe plusieurs modules ?

Consultez [Gérer les packages Python 3](python-3-packages.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous ne trouvez pas de réponse à votre question ici, vous pouvez consulter les sources suivantes pour plus de questions et réponses.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Forum de commentaires](https://feedback.azure.com/d365community/forum/721a322e-bd25-ec11-b6e6-000d3a4f0f1c)
