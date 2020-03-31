---
title: Configurer les paramètres Windows Update pour une utilisation par Azure Update Management
description: Cet article décrit les paramètres Windows Update configurables pour une utilisation avec Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79235637"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurer les paramètres Windows Update pour une utilisation par Update Management

Azure Update Management s’appuie sur le [client Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) pour télécharger et installer des mises à jour de Windows. Des paramètres spécifiques sont utilisés par le client Windows Update lors de la connexion à Windows Server Update Services (WSUS) ou à Windows Update. Vous pouvez gérer une grande partie de ces paramètres par les moyens suivants :

- Éditeur de stratégie de groupe locale
- Stratégie de groupe
- PowerShell
- Modification directe du Registre

Update Management respecte un grand nombre des paramètres spécifiés pour contrôler le client Windows Update. Si vous utilisez des paramètres pour activer les mises à jour non-Windows, Update Management gérera également ces mises à jour. Si vous souhaitez activer le téléchargement des mises à jour avant le déploiement de mises à jour, le déploiement des mises à jour peut être plus rapide et moins susceptible de dépasser la fenêtre de maintenance.

## <a name="pre-download-updates"></a>Pré-télécharger des mises à jour

Pour configurer le téléchargement automatique des mises à jour, sans les installer automatiquement, vous pouvez utiliser une stratégie de groupe pour définir le [paramètre Configurer les mises à jour automatiques](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) sur **3**. Ce paramètre permet de télécharger les mises à jour nécessaires en arrière-plan, puis de vous avertir que les mises à jour sont prêtes à être installées. Cela permet à Update Management de garder le contrôle des planifications, mais les mises à jour peuvent être téléchargées en dehors de la fenêtre de maintenance d’Update Management. Ce comportement peut empêcher les erreurs de type **Fenêtre de maintenance dépassée** dans Update Management.

Vous pouvez activer ce paramètre à l’aide de PowerShell en exécutant la commande suivante :

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurer les paramètres de redémarrage

Les clés de Registre répertoriées sous [Configuration des mises à jour automatiques en modifiant le registre](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) et [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) peuvent entraîner le redémarrage de vos machines, même si vous spécifiez **Ne jamais redémarrer** dans les paramètres **Déploiement des mises à jour**. Configurez ces clés de Registre pour qu’elles s’adaptent au mieux à votre environnement.

## <a name="enable-updates-for-other-microsoft-products"></a>Activer les mises à jour pour d’autres produits Microsoft

Par défaut, le client Windows Update est configuré pour fournir des mises à jour uniquement pour Windows. Si vous activez le paramètre **Me communiquer les mises à jour d’autres produits Microsoft lorsque je mets à jour Windows**, les mises à jour des autres produits vous sont également fournies, y compris les correctifs de sécurité pour Microsoft SQL Server ou d’autres logiciels Microsoft. Vous pouvez configurer cette option si vous avez téléchargé et copié les derniers [fichiers de modèles d’administration](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) disponibles pour Windows 2016 et versions ultérieures.

Si vous exécutez Windows Server 2012 R2, ce paramètre ne peut pas être configuré par une stratégie de groupe. Exécutez la commande PowerShell suivante sur ces machines. Update Management se conforme à ce paramètre.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Paramètres de configuration WSUS

Update Management prend en charge les paramètres WSUS. Les paramètres WSUS configurables pour une utilisation par Update Management sont répertoriés ci-dessous.

### <a name="intranet-microsoft-update-service-location"></a>Emplacement intranet du service de mise à jour Microsoft

Vous pouvez spécifier les sources à utiliser pour l’analyse et le téléchargement des mises à jour sous [Spécifier l’emplacement intranet du service de mise à jour Microsoft](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Par défaut, le client Windows Update est configuré pour télécharger les mises à jour à partir de Windows Update. Quand vous spécifiez un serveur WSUS comme source pour vos machines, si les mises à jour ne sont pas approuvées dans WSUS, leur déploiement échoue. 

Pour limiter les machines à ce seul service de mise à jour interne, configurez [Ne pas se connecter à des emplacements Internet Windows Update](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré les paramètres Windows Update, vous pouvez planifier un déploiement de mises à jour en suivant les instructions sous [Gérer les mises à jour et les correctifs pour vos machines virtuelles Azure](automation-tutorial-update-management.md).
