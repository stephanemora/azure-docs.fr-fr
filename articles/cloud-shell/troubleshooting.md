---
title: Résolution des problèmes Azure Cloud Shell | Microsoft Docs
description: Dépannage d’Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 21bc0633a9cc607325b48998791cb12631ecd0d7
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856485"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Résolution des problèmes et limitations d’Azure Cloud Shell

Les solutions connues pour la résolution des problèmes d’Azure Cloud Shell sont les suivantes :

## <a name="general-troubleshooting"></a>Résolution générale des problèmes

### <a name="early-timeouts-in-firefox"></a>Délais d’expiration anticipés dans Firefox
- **Détails** : Cloud Shell utilise un websocket ouvert pour passer les entrées/sorties à votre navigateur. Firefox a des stratégies prédéfinies qui peuvent fermer le websocket prématurément et causer des délais d’expiration anticipés dans Cloud Shell.
- **Résolution** : ouvrez Firefox et accédez à « about:config » dans la barre d’URL. Recherchez « network.websocket.timeout.ping.request » et remplacez la valeur 0 par 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Boîte de dialogue de stockage - Erreur : 403 RequestDisallowedByPolicy
- **Détails** : lorsque vous créez un compte de stockage par le biais de Cloud Shell, l’opération échoue en raison d’une stratégie Azure appliquée par votre administrateur. Le message d’erreur inclut le texte suivant :`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Résolution** : contactez votre administrateur Azure pour supprimer ou mettre à jour la stratégie Azure refusant la création du stockage.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Boîte de dialogue de stockage - Erreur : 400 DisallowedOperation
 - **Détails** : lorsque j’utilise un abonnement Azure Active Directory, je ne peux pas créer de stockage.
 - **Résolution** : pour résoudre ce problème, utilisez un abonnement Azure habilité à créer des ressources de stockage. Les abonnements Azure AD ne peuvent pas créer de ressources Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Sortie du terminal - Erreur : Failed to connect terminal: websocket cannot be established. Press `Enter` to reconnect. (Échec de la connexion du terminal : impossible d’établir la connexion WebSocket. Appuyez sur Entrée pour rétablir la connexion.)
 - **Détails** : Cloud Shell doit pouvoir établir une connexion websocket à l’infrastructure Cloud Shell.
 - **Résolution** : vérifiez vous avez configuré vos paramètres réseau pour activer l’envoi de demandes https et websocket aux domaines sur *. console.azure.com.

## <a name="bash-troubleshooting"></a>Résolution des problèmes de Bash

### <a name="cannot-run-the-docker-daemon"></a>Impossible d’exécuter le démon Docker

- **Détails** : comme Cloud Shell utilise un conteneur pour héberger votre environnement d’interpréteur de commandes, l’exécution du démon est interdite.
- **Résolution** : utilisez l’outil [docker-machine](https://docs.docker.com/machine/overview/), qui est installé par défaut, pour gérer les conteneurs Docker d’un hôte Docker distant.

## <a name="powershell-troubleshooting"></a>Résolution des problèmes de PowerShell

### <a name="gui-applications-are-not-supported"></a>Les applications de l’interface graphique utilisateur ne sont pas prises en charge.

- **Détails** : si un utilisateur lance une application d’interface graphique utilisateur, l’invite ne renvoie rien. Par exemple, lorsqu’un utilisateur clone un dépôt GitHub privé pour lequel l’authentification à deux facteurs est activée, une boîte de dialogue s’affiche, permettant de procéder à l’authentification à deux facteurs.  
- **Résolution** : fermez et rouvrez l’interpréteur de commandes.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online n’ouvre pas la page d’aide

- **Détails** : si un utilisateur tape `Get-Help Find-Module -online`, un message d’erreur semblable au suivant s’affiche  :`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Résolution** : copiez l’url et ouvrez-la manuellement sur votre navigateur.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Résolution des problèmes de gestion à distance des machines virtuelles Azure

- **Détails** : en raison des paramètres de pare-feu Windows par défaut pour WinRM, l’erreur suivante peut s’afficher :`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Résolution** : exécutez `Enable-AzureRmVMPSRemoting` pour activer tous les aspects de la communication à distance PowerShell sur l’ordinateur cible.
 

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` met en cache le résultat dans le lecteur Azure

- **Détails** : le résultat de `dir` est mis en cache dans le lecteur Azure.
- **Résolution** : après avoir créé ou supprimé une ressource dans la vue du lecteur Azure, exécutez `dir -force` pour mettre à jour ce dernier.

## <a name="general-limitations"></a>Limitations générales
Azure Cloud Shell a les limitations connues suivantes :

### <a name="system-state-and-persistence"></a>État du système et persistance

La machine qui fournit votre session Cloud Shell est temporaire. En effet, elle est recyclée lorsque votre session reste inactive pendant 20 minutes. Cloud Shell requiert qu’un partage de fichiers Azure soit monté. Par conséquent, votre abonnement doit être en mesure de configurer des ressources de stockage pour accéder à Cloud Shell. Autres éléments à prendre en compte :

* Avec le stockage monté, seules les modifications apportées à votre répertoire `clouddrive` sont conservées. Dans Bash, votre répertoire `$Home` est également conservé.
* Les partages de fichiers Azure peuvent être montés uniquement depuis votre [région affectée](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Dans Bash, exécutez `env` pour rechercher votre région définie en tant que `ACC_LOCATION`.
* Azure Files prend uniquement en charge les comptes de stockage localement redondant et les comptes de stockage géoredondant.

### <a name="browser-support"></a>Prise en charge des navigateurs

Cloud Shell prend en charge les dernières versions de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox et Apple Safari. Safari en mode privé n’est pas pris en charge.

### <a name="copy-and-paste"></a>Copier et coller

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

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

Si l’utilisateur exécute une commande susceptible de créer une boîte de dialogue Windows, comme `Connect-AzureAD` ou `Connect-AzureRmAccount`, un message d’erreur apparaît tel que : `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-crashes-psreadline"></a>La saisie semi-automatique via la touche Tab bloque PSReadline

Si le mode EditMode de l’utilisateur dans PSReadline est défini sur Emacs, l’utilisateur tente d’afficher toutes les possibilités par le biais de la saisie semi-automatique via la touche Tab et la taille de la fenêtre est trop petite pour afficher toutes les possibilités, donc PSReadline se bloque.

### <a name="large-gap-after-displaying-progress-bar"></a>Écart important après avoir affiché la barre de progression

Si l’utilisateur exécute une action qui affiche une barre de progression, comme le renseignement d’un onglet sur le lecteur `Azure:`, il est possible que le curseur ne soit pas défini correctement et un écart s’affiche dans là où la barre de progression se situait précédemment.

### <a name="random-characters-appear-inline"></a>Des caractères aléatoires apparaissent en ligne

Les codes de la séquence de position du curseur, par exemple `5;13R`, peuvent apparaître dans l’entrée utilisateur.  Les caractères peuvent être supprimés manuellement.

## <a name="personal-data-in-cloud-shell"></a>Données personnelles dans Cloud Shell

Azure Cloud Shell prend très au sérieux vos données personnelles ; les données capturées et stockées par le service Azure Cloud Shell sont utilisées pour vous fournir un environnement par défaut, tel que l’interpréteur de commandes le plus récemment utilisé, les taille et type de police par défaut, ainsi que les détails des partages de fichiers pour le lecteur cloud. Si vous souhaitez exporter ou supprimer ces données, nous avons inclus les instructions suivantes.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportation
Pour **exporter** les paramètres utilisateur que Cloud Shell enregistre pour vous, tels que l’interpréteur de commandes, la taille de police et le type de police par défaut, exécutez les commandes suivantes.

1. Lancer Bash dans Cloud Shell
2. Exécutez les commandes suivantes :
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Supprimer
Pour **supprimer** vos paramètres utilisateur que Cloud Shell enregistre pour vous, tels que l’interpréteur de commandes, la taille de police et le type de police par défaut, exécutez les commandes suivantes. Au prochain démarrage de Cloud Shell, vous serez invité à réintégrer un partage de fichiers. 

Le partage Azure Files proprement dit n’est pas supprimé si vous supprimez vos paramètres utilisateur ; accédez à Azure Files pour effectuer cette action.

1. Lancer Bash dans Cloud Shell
2. Exécutez les commandes suivantes :
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
