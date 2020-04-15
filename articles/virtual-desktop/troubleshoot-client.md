---
title: Détecter un problème de Bureau à distance client dans Windows Virtual Desktop – Azure
description: Comment résoudre des problèmes lorsque vous configurez des connexions à un client dans un environnement de locataire Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473848"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Résoudre des problèmes du client Bureau à distance

Cet article décrit les problèmes courants liés au client Bureau à distance et leur résolution.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Le client Bureau à distance de Windows 7 ou Windows 10 ne répond plus ou ne peut pas être ouvert

Depuis la version 1.2.790, vous pouvez réinitialiser les données utilisateur à partir de la page À propos de ou à l’aide d’une commande.

Utilisez la commande suivante pour supprimer vos données utilisateur, restaurer les paramètres par défaut et annuler l’abonnement à tous les espaces de travail.

```cmd
msrdcw.exe /reset [/f]
```

Si vous utilisez une version antérieure du client Bureau à distance, nous vous recommandons de désinstaller et réinstaller le client.

## <a name="web-client-wont-open"></a>Le client Web ne s’ouvre pas

Tout d’abord, testez votre connexion Internet en ouvrant un autre site web dans votre navigateur. par exemple, [www.bing.com](https://www.bing.com).

Utilisez **nslookup** pour vérifier que DNS peut résoudre le nom de domaine complet :

```cmd
nslookup rdweb.wvd.microsoft.com
```

Essayez de vous connecter avec un autre client, comme le client Bureau à distance pour Windows 7 ou Windows 10, puis vérifiez si vous pouvez ouvrir le client web.

### <a name="opening-another-site-fails"></a>échec de l’ouverture d’un autre site

Cela est généralement dû à des problèmes de connexion réseau ou à une panne du réseau. Nous vous recommandons de contacter le support réseau.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup ne peut pas résoudre le nom

Cela est généralement dû à des problèmes de connexion réseau ou à une panne du réseau. Nous vous recommandons de contacter le support réseau.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Votre client ne peut pas se connecter, mais d’autres clients de votre réseau peuvent se connecter

Si votre navigateur commence à se comporter bizarrement ou cesse de fonctionner pendant que vous utilisez le client Web, suivez ces instructions pour résoudre le problème :

1. Redémarrez le navigateur.
2. Effacez les cookies du navigateur. Consultez l’article [How to delete cookie files in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer) (Suppression des fichiers de cookie dans Internet Explorer).
3. Videz le cache du navigateur. Consultez l’article [Clear browser cache for your browser](https://binged.it/2RKyfdU) (Vider le cache du navigateur).
4. Ouvrez le navigateur en mode privé.

## <a name="web-client-stops-responding-or-disconnects"></a>Le client web cesse de répondre ou se déconnecte

Essayez de vous connecter à l’aide d’un autre navigateur ou client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>d’autres navigateurs et clients connaissent également un dysfonctionnement ou ne parviennent pas à s’ouvrir

Si les problèmes se poursuivent même après avoir changé de navigateurs, le problème peut ne pas être lié à votre navigateur, mais à votre réseau. Nous vous recommandons de contacter le support réseau.

## <a name="web-client-keeps-prompting-for-credentials"></a>Le client web ne cesse de demander les informations d’identification

Si le client web ne cesse de demander les informations d’identification, suivez ces instructions :

1. Vérifiez que l’URL du client web est correcte.
2. Vérifiez que les informations d’identification que vous utilisez correspondent à l’environnement Windows Virtual Desktop lié à l’URL.
3. Effacez les cookies du navigateur. Pour plus de détails, consultez l’article [How to delete cookie files in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer) (Suppression des fichiers de cookie dans Internet Explorer).
4. Videz le cache du navigateur. Pour plus de détails, consultez l’article [Clear browser cache for your browser](https://binged.it/2RKyfdU) (Vider le cache du navigateur).
5. Ouvrez votre navigateur en mode privé.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une vue d’ensemble de la résolution des problèmes Windows Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes de création d’un pool de locataires et d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un pool de locataires et d’hôtes](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).