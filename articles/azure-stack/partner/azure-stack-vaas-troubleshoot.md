---
title: Résoudre les problèmes de validation en tant que service Azure Stack | Microsoft Docs
description: Résolvez les problèmes de validation en tant que service pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ed070ac4fdf9ccca1b1b4b99b8031bc3fd035779
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160147"
---
# <a name="troubleshoot-validation-as-a-service"></a>Résoudre les problèmes de validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Voici les problèmes courants, sans lien avec les versions logicielles, et leurs solutions.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>Le portail affiche l’agent local en mode débogage

Cela vient probablement du fait que l’agent ne peut pas envoyer de pulsations au service, car la connexion réseau est instable. Une pulsation est envoyée toutes les cinq minutes. Si le service ne reçoit pas de pulsation pendant 15 minutes, il considère que l’agent est inactif et plus aucun test n’est planifié pour ce service. Vérifiez le message d’erreur dans le fichier *Agenthost.log*, il se trouve dans le répertoire où l’agent a été démarré.

> [!Note] 
> Tous les tests déjà en cours d’exécution sur l’agent continuent de s’exécuter, mais si la pulsation n’est pas restaurée avant la fin du test, l’agent ne peut pas mettre à jour l’état du test, ni charger les journaux. Le test apparaît toujours comme étant **en cours d’exécution** et il doit être annulé.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Le processus de l’agent sur la machine a été arrêté pendant l’exécution du test. À quoi s’attendre ?

Si le processus de l’agent est arrêté de manière anormale, par exemple un redémarrage de la machine avec suppression du processus (un CTRL+C dans la fenêtre de l’agent est considéré comme un arrêt correct), le test en cours d’exécution sur cette machine continue de s’afficher **en cours d’exécution**. Si l’agent est redémarré, il met à jour l’état du test qui est **annulé**. Si l’agent n’est pas redémarré, le test s’affiche comme étant **en cours d’exécution** et vous devez annuler manuellement le test

> [!Note] 
> Les tests au sein d’un workflow sont planifiés pour une exécution séquentielle. Les tests **en attente** ne sont pas exécutés tant que les tests affichant l’état **en cours d’exécution** dans ce même workflow ne sont pas terminés.

## <a name="handle-slow-network-connectivity"></a>Gérer les problèmes de lenteur de la connexion réseau

Vous pouvez télécharger l’image PIR sur un partage dans votre centre de données local. Vous pouvez ensuite vérifier l’image.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Télécharger l’image PIR sur un partage local en cas de trafic réseau lent

1. Téléchargez AzCopy à partir de : [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Extrayez AzCopy.zip et accédez au répertoire contenant AzCopy.exe

3. Ouvrez Windows PowerShell à partir d’une invite de commandes avec élévation des privilèges. Exécutez les commandes suivantes :

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare représente le chemin de partage ou le chemin local.

### <a name="verifying-pir-image-file-hash-value"></a>Vérification de la valeur de hachage du fichier Image PIR

Vous pouvez utiliser l’applet de commande **Get-HashFile** pour obtenir la valeur de hachage des fichiers image du dépôt d’images publiques téléchargées pour vérifier l’intégrité des images.

| Nom de fichier | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Validation en tant que service Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
