---
title: Établir une connexion 5G ou LTE avec Azure Percept DK avec un modem USB
description: Cet article explique comment connecter Azure Percept DK via des réseaux 5G ou LTE avec un modem USB.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e9fb2ae534d11fb0a85199baf7256ab174e3f25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007348"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-usb-modem"></a>Établir une connexion 5G ou LTE avec Azure Percept DK avec un modem USB

Vous trouverez ci-dessous les étapes à suivre pour préparer et connecter Azure Percept DK à l’aide de modems USB à des réseaux 5G ou LTE. Ces instructions s’appliquent uniquement au logiciel Azure Percept DK spécial que vous pouvez télécharger en suivant les instructions ci-dessous. Cette image Azure Percept spéciale comprend un logiciel open source ModemManager qui prend en charge une grande variété de modems USB. Notez que cette image ne prend pas en charge les mises à jour OTA du système d’exploitation ou autres logiciels. Avec ce logiciel, vous pouvez utiliser un modem standard LTE USB économique ou des modems 5G plus sophistiqués pour connecter votre Azure Percept à Internet et à Azure. 

> [!Note]
> Ces instructions sont destinées à être utilisées avec les modems USB qui prennent en charge l’interface MBIM. Avant d’acquérir un modem USB, vérifiez qu’il prend en charge l’interface MBIM. Vérifiez ensuite qu’il est répertorié dans la liste des modems pris en charge par ModemManager. Le logiciel ModemManager peut être utilisé avec d’autres interfaces, mais nous nous sommes concentrés sur l’interface MBIM. Vous pouvez retrouver plus d’informations sur ModemManager ici : https://www.freedesktop.org/wiki/Software/ModemManager/


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems.png" alt-text="Modems USB connectés à Azure Percept.":::

## <a name="setting-up-the-devkit-for-using-usb-modem"></a>Configuration du kit de développement pour l’utilisation d’un modem USB

- **Télécharger l’image logicielle Azure Percept qui prend en charge ModemManager** - [Fichiers d’image logicielle Azure percept 5G zippés](https://aka.ms/azpercept5gimage) pour télécharger les 3 fichiers nécessaires à la mise à jour de votre logiciel Azure Percept pour pouvoir prendre en charge les modems USB
- **Mettez à jour votre Azure Percept avec les fichiers téléchargés à l’aide de la méthode USB** - Suivez [How-to-update-via-USB](./how-to-update-via-usb.md) pour mettre à jour votre kit de développement avec le logiciel Azure Percept spécial compatible LTE/5G que vous avez téléchargé à l’étape précédente. N’oubliez pas d’utiliser UNIQUEMENT les fichiers téléchargés à *l’étape précédente* et non ceux qui sont mentionnés dans la rubrique sur la mise à jour via USB !
- **Suivez le processus de configuration normal** - [quickstart-percept-dk-set-up](./quickstart-percept-dk-set-up.md) pour suivre la procédure d’installation si vous ne la connaissez pas bien. L’expérience d’installation n’est pas différente sur cette version d’Azure Percept avec ModemManager.
- **Connectez-vous à votre Azure Percept sur SSH** - [how-to-ssh-into-percept-dk](./how-to-ssh-into-percept-dk.md) pour obtenir des instructions

## <a name="step-by-step-instructions-for-connecting-three-different-modems"></a>Instructions pas à pas pour la connexion de trois modems différents

Vous trouverez ci-dessous des instructions pour trois modems USB différents. Tout d’abord, il s’agit d’un simple dongle USB LTE CAT-4 (Vodafone) qui n’a pas de fonctionnalités spéciales. Les instructions pour ce modem peuvent être utilisées pour les modems USB simples et économiques. La deuxième (Multitech) est un exemple de modem USB avec différents modes de fonctionnement USB. Pour ce type de modem, vous devez d’abord activer le mode USB approprié pour activer l’interface MBIM prise en charge par ModemManager. La troisième est un modem 5G (Quectel DK) qui présente également des modes différents. Vous devez d’abord activer le mode MBIM approprié.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Modem Vodafone USB Connect 4G v2
:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Modem USB Vodafone":::

Voici les instructions à suivre pour connecter votre Azure Percept DK à l’aide d’un modem USB simple, tel que le modem Vodafone USB Connect 4G v2.

[Établir une connexion avec un modem Vodafone Connect 4G ou un modem USB](./connect-over-cellular-usb-vodafone.md).   

### <a name="multitech-multiconnect-usb-modem"></a>Modem MultiTech Multiconnect USB
:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="Modem USB Multitech":::

Voici les instructions à suivre pour connecter votre Azure Percept DK à l’aide d’un modem USB simple, tel que le modem MultiTech USB modem (MTCM-LNA3-B03).

[Établir une connexion avec un modem USB Multitech](./connect-over-cellular-usb-multitech.md).   

### <a name="quectel-5g-developer-kit"></a>Kit de développement Quectel 5G
:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Quectel 5G DK":::

Voici les instructions à suivre pour connecter votre Azure Percept DK à l’aide d’un modem USB 5G comme le modem Quectel RM500Q-GL.

[Établir une connexion avec Quectel 5G Developer Kit](./connect-over-cellular-usb-quectel.md). 

## <a name="how-to-make-your-lte5g-connection-recover-from-reboot"></a>Comment récupérer votre connexion LTE/5G au redémarrage 
Avec les instructions ci-dessus, vous pouvez configurer le modem USB pour qu’il se connecte au réseau, mais si vous redémarrez votre appareil, vous devez vous reconnecter manuellement. Nous travaillons à une solution pour améliorer cette situation. Si vous souhaitez obtenir plus d’informations, envoyez un e-mail à azpercept5G@microsoft.com avec une brève note de référence. 

## <a name="debugging-information"></a>Informations de débogage 
N’oubliez pas de vérifier que votre carte SIM fonctionne sur le matériel que vous avez l’intention d’utiliser. Plusieurs opérateurs limitent les cartes SIM IoT de données pour fonctionner sur un seul appareil. Si c’est le cas, vous devez vous assurer que l’IMEI/le numéro de série de l’appareil est répertorié dans la carte SIM « liste des appareils autorisés » par l’opérateur.

### <a name="modemmanager-debug-mode"></a>Mode débogage ModemManager

Le mode débogage de ModemManager peut être activé en modifiant le fichier `/lib/systemd/system/ModemManager.service` à la ligne : `ExecStart=/usr/sbin/ModemManager [...]` en ajoutant `--debug`, comme dans l’exemple ci-dessous :
```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```
Vous devrez ensuite recharger les services et redémarrer ModemManager pour que les modifications soient prises en compte :
```
systemctl daemon-reload
systemctl restart ModemManager
```
Et voici quelques commandes qui vous permettent de voir les journaux et de nettoyer les fichiers journaux :
```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="reliability-and-stability-enhancement"></a>Amélioration de la fiabilité et de la stabilité

#### <a name="strict-mode"></a>Mode strict
Pour empêcher ModemManager d’interagir avec les interfaces série autres que celles du modem, vous pouvez limiter les interfaces qui sont détectées (pour déterminer quelles osnt celles des modems) en modifiant les [stratégies de filtre](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html). Nous vous recommandons d’utiliser le mode `STRICT`.

Pour ce faire, vous devez modifier `/lib/systemd/system/ModemManager.service` à la ligne : `ExecStart=/usr/sbin/ModemManager [...]` et ajouter `--filter-policy=STRICT` comme dans l’exemple ci-dessous :
```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
Vous devrez ensuite recharger les services et redémarrer ModemManager pour que les modifications soient prises en compte :
```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>Étapes suivantes
À présent, consultez les instructions pour les différents modems USB :

[Établir une connexion avec Vodafone Connect 4G ou un modem USB](./connect-over-cellular-usb-vodafone.md)

[Établir une connexion avec un modem USB Multitech](./connect-over-cellular-usb-multitech.md)

[Établir une connexion avec Quectel 5G Developer Kit](./connect-over-cellular-usb-quectel.md)

Retour à l’article principal sur 5G ou LTE :

[Établir une connexion 5G ou LTE](./connect-over-cellular.md).

[Établir une connexion avec une passerelle mobile](./connect-over-cellular-gateway.md).
