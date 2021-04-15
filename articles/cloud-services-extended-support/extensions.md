---
title: Extensions pour Cloud Services (support étendu)
description: Extensions pour Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220915"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Extensions pour Cloud Services (support étendu)

Les extensions sont de petites applications qui proposent des tâches de configuration et d’automatisation post-déploiement sur les rôles. Par exemple, vous pouvez activer une connexion Bureau à distance dans votre rôle pendant le déploiement du service cloud (support étendu) à l’aide de l’extension Bureau à distance.  

## <a name="remote-desktop-extension"></a>Extension Bureau à distance

Le Bureau à distance vous permet d'accéder au bureau d'un rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour résoudre et diagnostiquer les problèmes rencontrés par votre application lorsqu’elle est en cours d’exécution.

Vous pouvez activer une connexion Bureau à distance dans votre rôle pendant le développement en incluant les modules Bureau à distance dans votre définition de service ou par l’intermédiaire de l’extension Bureau à distance. 

Pour plus d’informations, consultez [Configurer le Bureau à distance à partir du portail Azure](enable-rdp.md).

## <a name="windows-azure-diagnostics-extension"></a>Extension Diagnostics Azure pour Windows

Vous pouvez surveiller les principales mesures de performances pour n’importe quel service cloud. Chaque rôle de service cloud collecte des données de base : utilisation du processeur, utilisation du réseau et utilisation du disque. Si le service cloud a l’extension Microsoft.Azure.Diagnostics appliquée à un rôle, ce rôle peut collecter des points de données supplémentaires. 

Avec la surveillance de base, les données des compteurs de performances des instances de rôle sont échantillonnées et collectées toutes les 3 minutes. Ces données de surveillance de base ne sont pas stockées dans votre compte de stockage et ne représentent aucun coût supplémentaire associé. 

Avec la surveillance avancée, des mesures supplémentaires sont prélevées et collectées par intervalles de 5 minutes, 1 heure et 12 heures. Les données consolidées sont stockées dans un compte de stockage et purgées tous les 10 jours. Le compte de stockage utilisé est configuré par rôle. Vous pouvez utiliser différents comptes de stockage pour différents rôles. 

Pour plus d’informations, consultez [Appliquer l’extension Diagnostics Azure pour Windows dans Cloud Services (support étendu)](enable-wad.md).

## <a name="anti-malware-extension"></a>Extension anti-programme malveillant
Une application ou un service Azure peut activer et configurer Microsoft Antimalware pour les services cloud Azure à l’aide des applets de commande PowerShell. Notez que Microsoft Antimalware est installé à l’état désactivé dans la plateforme Cloud Services exécutant Windows Server 2012 R2 ou une version plus ancienne qui nécessite une action d’une application Azure pour être activé. Pour Windows Server 2016 et versions ultérieures, Windows Defender est activé par défaut. Par conséquent, ces applets de commande peuvent être utilisées pour la configuration de Microsoft Antimalware.

Pour plus d’informations, consultez [Ajouter Microsoft Antimalware au service cloud Azure à l’aide du support étendu (CS-ES)](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)

Pour en savoir plus sur Azure Antimalware, consultez [cette page](https://docs.microsoft.com/azure/security/fundamentals/antimalware)



## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
- Déployez une instance Cloud Services (support étendu) avec le [Portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
