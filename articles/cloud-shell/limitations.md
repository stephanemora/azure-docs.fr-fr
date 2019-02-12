---
title: Limitations d’Azure Cloud Shell | Microsoft Docs
description: Vue d’ensemble des limites d’Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 1f2c218ed9ba2f5f9285c60b8d4c11704825c0f5
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563879"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitations d’Azure Cloud Shell

Azure Cloud Shell a les limitations connues suivantes :

## <a name="general-limitations"></a>Limitations générales

### <a name="system-state-and-persistence"></a>État du système et persistance

La machine qui fournit votre session Cloud Shell est temporaire. En effet, elle est recyclée lorsque votre session reste inactive pendant 20 minutes. Cloud Shell requiert qu’un partage de fichiers Azure soit monté. Par conséquent, votre abonnement doit être en mesure de configurer des ressources de stockage pour accéder à Cloud Shell. Autres éléments à prendre en compte :

* Avec le stockage monté, seules les modifications apportées à votre répertoire `$Home` sont conservées.
* Les partages de fichiers Azure peuvent être montés uniquement depuis votre [région affectée](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Dans Bash, exécutez `env` pour rechercher votre région définie en tant que `ACC_LOCATION`.

### <a name="browser-support"></a>Prise en charge des navigateurs

Cloud Shell prend en charge les dernières versions de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox et Apple Safari. Safari en mode privé n’est pas pris en charge.

### <a name="copy-and-paste"></a>Copier et coller

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pour un utilisateur donné, un seul interpréteur de commandes peut être actif

Les utilisateurs peuvent lancer uniquement un seul type d’interpréteur de commandes à la fois, soit **Bash** soit **PowerShell**. Toutefois, plusieurs instances de Bash ou de PowerShell peuvent s’exécuter simultanément. Le fait de basculer de Bash à PowerShell entraîne le redémarrage de Cloud Shell, ce qui met fin aux sessions existantes.

### <a name="usage-limits"></a>Limites d’utilisation

Cloud Shell est destiné aux cas d’usage interactif. De fait, les sessions non interactives longues se terminent sans avertissement.

## <a name="bash-limitations"></a>Limites de Bash

### <a name="user-permissions"></a>Autorisations utilisateur

Les autorisations sont définies en tant qu’utilisateurs standards sans accès sudo. Les installations en dehors du répertoire `$Home` ne sont pas conservées.

### <a name="editing-bashrc"></a>Modifier .bashrc

Faites attention lorsque vous modifiez .bashrc : cela peut entraîner des erreurs inattendues dans Cloud Shell.

## <a name="powershell-limitations"></a>Limites de PowerShell

### <a name="azuread-module-name"></a>Nom du module `AzureAD`

Le nom du module `AzureAD` est actuellement `AzureAD.Standard.Preview`, le module fournit les mêmes fonctionnalités.

### <a name="sqlserver-module-functionality"></a>Fonctionnalités du module `SqlServer`

Le module `SqlServer` inclus dans Cloud Shell n’offre qu’une prise en charge préliminaire pour PowerShell Core. En particulier, `Invoke-SqlCmd` n’est pas encore disponible.

### <a name="default-file-location-when-created-from-azure-drive"></a>Emplacement du fichier par défaut lors de sa création à partir du lecteur Azure :

Les utilisateurs ne peuvent pas créer de fichiers sous le lecteur Azure à l’aide des cmdlets PowerShell. Si les utilisateurs créent des fichiers à l’aide d’autres outils, tels que vim ou nano, les fichiers sont enregistrés dans le dossier `$HOME` par défaut. 

### <a name="gui-applications-are-not-supported"></a>Les applications de l’interface graphique utilisateur ne sont pas prises en charge.

Si l'utilisateur exécute une commande susceptible de créer une boîte de dialogue Windows, comme `Connect-AzureAD`, `Connect-AzureRmAccount` ou `Connect-AzAccount`, un message d'erreur semblable au suivant apparaît : `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-crashes-psreadline"></a>La saisie semi-automatique via la touche Tab bloque PSReadline

Si le mode EditMode de l’utilisateur dans PSReadline est défini sur Emacs, l’utilisateur tente d’afficher toutes les possibilités par le biais de la saisie semi-automatique via la touche Tab et la taille de la fenêtre est trop petite pour afficher toutes les possibilités, donc PSReadline se bloque.

### <a name="large-gap-after-displaying-progress-bar"></a>Écart important après avoir affiché la barre de progression

Si l’utilisateur exécute une action qui affiche une barre de progression, comme le renseignement d’un onglet sur le lecteur `Azure:`, il est possible que le curseur ne soit pas défini correctement et un écart s’affiche dans là où la barre de progression se situait précédemment.

### <a name="random-characters-appear-inline"></a>Des caractères aléatoires apparaissent en ligne

Les codes de la séquence de position du curseur, par exemple `5;13R`, peuvent apparaître dans l’entrée utilisateur.  Les caractères peuvent être supprimés manuellement.

## <a name="next-steps"></a>Étapes suivantes

[Dépannage de Cloud Shell](troubleshooting.md) <br>
[Démarrage rapide pour Bash](quickstart.md) <br>
[Démarrage rapide pour PowerShell](quickstart-powershell.md)
