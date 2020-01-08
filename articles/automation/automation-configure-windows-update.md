---
title: Configurer les paramètres Windows Update pour une utilisation par Azure Update Management
description: Cet article décrit les paramètres Windows Update configurables pour une utilisation avec Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 10/02/2019
ms.topic: conceptual
ms.openlocfilehash: f6377012a2afd0fb36486edf0af0ac3591b5d1f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366853"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurer les paramètres Windows Update pour une utilisation par Update Management

Azure Update Management s’appuie sur Windows Update pour télécharger et installer des mises à jour de Windows. Par conséquent, Update Management respecte la plupart des paramètres utilisés par Windows Update. Si vous utilisez des paramètres pour activer les mises à jour non-Windows, Update Management gérera également ces mises à jour. Si vous souhaitez activer le téléchargement des mises à jour avant le déploiement de mises à jour, le déploiement des mises à jour peut être plus rapide et moins susceptible de dépasser la fenêtre de maintenance.

## <a name="pre-download-updates"></a>Pré-télécharger des mises à jour

Pour configurer automatiquement le téléchargement des mises à jour dans la stratégie de groupe, définissez le [paramètre Configurer les mises à jour automatiques](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) sur **3**. Ce paramètre permet de télécharger les mises à jour requises en arrière-plan, mais ne les installe pas. Cela permet à Update Management de garder le contrôle des planifications, mais les mises à jour peuvent être téléchargées en dehors de la fenêtre de maintenance d’Update Management. Ce comportement peut empêcher les erreurs « Fenêtre de maintenance dépassée » dans Update Management.

Vous pouvez également activer ce paramètre en exécutant la commande PowerShell suivante sur un système que vous souhaitez configurer pour le téléchargement automatique des mises à jour :

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Désactiver l’installation automatique

Par défaut, sur les machines virtuelles Azure, l’installation automatique des mises à jour est activée. Cela peut entraîner l’installation de mises à jour avant que vous n’en planifiez l’installation par Update Management. Vous pouvez désactiver ce comportement en définissant la clé de Registre `NoAutoUpdate` sur `1`. L’extrait de code PowerShell suivant vous montre comment procéder :

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Configurer les paramètres de redémarrage

Les clés de Registre répertoriées sous [Configuration des mises à jour automatiques en modifiant le registre](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) et [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) peuvent entraîner le redémarrage de vos machines, même si vous spécifiez **Ne jamais redémarrer** dans les paramètres **Déploiement des mises à jour**. Vous devez configurer ces clés de Registre pour qu’elles soient mieux adaptées à votre environnement.

## <a name="enable-updates-for-other-microsoft-products"></a>Activer les mises à jour pour d’autres produits Microsoft

Par défaut, Windows Update fournit uniquement des mises à jour pour Windows. Si vous activez le paramètre **Me communiquer les mises à jour d’autres produits Microsoft lorsque je mets à jour Windows**, les mises à jour des autres produits vous sont également fournies, y compris les correctifs de sécurité pour Microsoft SQL Server ou d’autres logiciels Microsoft. Cette option ne peut pas être configurée par la stratégie de groupe. Exécutez la commande PowerShell suivante sur les systèmes sur lesquels vous souhaitez activer d’autres mises à jour Microsoft. Update Management se conformera à ce paramètre.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Paramètres de configuration WSUS

Update Management est conforme aux paramètres de Windows Server Update Services (WSUS). Les paramètres WSUS configurables pour une utilisation par Update Management sont répertoriés ci-dessous.

### <a name="intranet-microsoft-update-service-location"></a>Emplacement intranet du service de mise à jour Microsoft

Vous pouvez spécifier les sources à utiliser pour l’analyse et le téléchargement des mises à jour sous [Spécifier l’emplacement intranet du service de mise à jour Microsoft](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré les paramètres Windows Update, vous pouvez planifier un déploiement de mises à jour en suivant les instructions sous [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
