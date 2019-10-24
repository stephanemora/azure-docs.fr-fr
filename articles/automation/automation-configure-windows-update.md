---
title: Configurer les paramètres Windows Update pour une utilisation par Azure Update Management
description: Cet article décrit les paramètres Windows Update configurables pour une utilisation par Update Management
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377390"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurer les paramètres Windows Update pour une utilisation par Update Management

Update Management s’appuie sur Windows Update pour télécharger et installer les mises à jour de Windows. Par conséquent, nous respectons la plupart des paramètres utilisés par Windows Update. Si vous utilisez des paramètres pour activer les mises à jour non-Windows, Update Management gérera également ces mises à jour. Si vous souhaitez activer le téléchargement des mises à jour avant le déploiement de mises à jour, le déploiement des mise à jour peut être plus rapide et être moins susceptible de dépasser la fenêtre de maintenance.

## <a name="pre-download-updates"></a>Prétélécharger les mises à jour

Pour configurer automatiquement le téléchargement des mises à jour dans la stratégie de groupe, vous pouvez définir le paramètre [Configurer les mises à jour automatiques](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) sur **3**. Cela télécharge les mises à jour nécessaires en arrière-plan, mais ne les installe pas. Cela permet à Update Management de garder le contrôle de la planification, mais aussi d’autoriser les mises à jour à être téléchargées en dehors de la fenêtre de maintenance d’Update Management. Ceci peut empêcher les erreurs de **fenêtre de maintenance dépassée** dans la Update Management.

Vous pouvez également définir ce paramètre avec PowerShell. Exécutez la commande PowerShell suivante sur le système sur lequel vous souhaitez activer le téléchargement automatique des mises à jour.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Désactiver l’installation automatique

Pour les machines virtuelles Azure, l’installation automatique des mises à jour est activée par défaut. Cela peut entraîner l’installation de mises à jour avant que vous n’en planifiez l’installation par Update Management. Vous pouvez désactiver ce comportement en définissant la clé de Registre `NoAutoUpdate` sur `1`. L’extrait de code PowerShell suivant vous montre une façon de procéder.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Configurer les paramètres de redémarrage

Les clés de Registre listées sous [Configuration des mises à jour automatiques en modifiant le Registre](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) et [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) peuvent entraîner le redémarrage de vos machines, même si vous avez spécifié **Ne jamais redémarrer** dans les paramètres Update Deployment. Vous devez configurer ces clés de Registre comme vous le souhaitez pour votre environnement.

## <a name="enable-updates-for-other-microsoft-products"></a>Activer les mises à jour pour d’autres produits Microsoft

Par défaut, Windows Update fournit uniquement des mises à jour pour Windows. Si vous activez l’option **Me communiquer les mises à jour d’autres produits Microsoft lorsque je mets à jour Windows**, les mises à jour des autres produits vous sont fournies, y compris les correctifs de sécurité pour SQL Server ou d’autres logiciels. Cette option ne peut pas être configurée par la stratégie de groupe. Exécutez la commande PowerShell suivante sur les systèmes sur lesquels vous souhaitez activer d’autres correctifs tiers. Update Management appliquera ce paramètre.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Paramètres de configuration WSUS

**Update Management** respecte les paramètres de configuration WSUS. Les paramètres WSUS configurables pour une utilisation par Update Management sont listés ci-dessous.

### <a name="intranet-microsoft-update-service-location"></a>Emplacement intranet du service de mise à jour Microsoft

Vous pouvez spécifier les sources à utiliser pour la recherche et le téléchargement des mises à jour sous [Emplacement intranet du service de mise à jour Microsoft](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré les paramètres Windows Update, vous pouvez planifier un déploiement de mises à jour en suivant les instructions fournies sous [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md)