---
title: Configurer les journaux syslog VMware pour Azure VMware Solution
description: Découvrez comment configurer les paramètres de diagnostic afin de collecter les journaux syslog VMware pour votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 983a62eb5b094c94048e7580fd53558df002d816
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700568"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Configurer les journaux syslog VMware pour Azure VMware Solution

Les paramètres de diagnostic permettent de configurer l’exportation en continu des journaux et des métriques de la plateforme pour une ressource vers la destination de votre choix. Vous pouvez créer jusqu’à cinq paramètres de diagnostic différents pour envoyer différents journaux et métriques à des destinations indépendantes. 

Dans cette rubrique, vous allez configurer un paramètre de diagnostic afin de collecter les journaux syslog VMware pour votre cloud privé Azure VMware Solution. Vous allez stocker le journal syslog dans un compte de stockage pour afficher les journaux vCenter et les analyser à des fins de diagnostic. 

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous disposez d’un cloud privé Azure VMware Solution avec accès aux interfaces vCenter et NSX-T Manager. 

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

1. À partir de votre cloud privé Azure VMware Solution, sélectionnez **Paramètres de diagnostic**, puis **Ajouter des paramètres de diagnostic**.
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="Capture d’écran montrant où configurer les journaux syslog VMware." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. Sélectionnez les options **vmwaresyslog**, **Allmetrics** et **Archiver dans un compte de stockage**.

   >[!IMPORTANT]
   >L’option **Envoyer à l’espace de travail Log Analytics** ne fonctionne pas actuellement.
 
1. Sélectionnez le compte de stockage dans lequel vous souhaitez stocker les journaux, puis sélectionnez **Enregistrer**.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Capture d’écran montrant les options à sélectionner pour le stockage des journaux syslog." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

1. Accédez à votre compte et à votre stockage, vérifiez que les **journaux Insight vmwarelog** ont été créés, et sélectionnez-les. 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Capture d’écran montrant l’option vmwarelog des journaux Insight créée et disponible." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


1. Accédez à l’emplacement et téléchargez le fichier json pour afficher les journaux.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Capture d’écran montrant le chemin du fichier json." lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

