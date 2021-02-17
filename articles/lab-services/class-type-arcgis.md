---
title: Configurer un labo pour ArcMap/ArcGIS Desktop avec Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un labo pour les classes qui utilisent ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 8d5356f7fd2661d9743d1058a147a0b20f62850e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373706"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Configurer un labo pour ArcMap/ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) est un type de système d’information géographique (SIG).  ArcGIS permet d’établir/analyser des cartes et de travailler avec des données géographiques fournies par [Environmental System Research Institute](https://www.esri.com/en-us/home) (ESRI).  Bien qu’ArcGIS Desktop comprenne plusieurs applications, cet article explique comment configurer des laboratoires afin d’utiliser ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) est destiné à la création, à la modification et à l’analyse de cartes en 2D.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour pouvoir configurer un labo pour l’utilisation d’ArcMap, vous avez besoin d’un abonnement Azure et d’un compte de laboratoire.  Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Dès lors que vous disposez d’un abonnement Azure, vous pouvez créer un compte Lab dans Azure Lab Services.  Pour plus d’informations sur la création d’un compte Lab, consultez [Configuration d’un compte Lab](tutorial-setup-lab-account.md).  Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres de votre compte Lab (cf. tableau suivant).  Pour plus d’informations sur l’activation des images de la Place de marché Azure, consultez [Spécification des images de la Place de marché Azure accessibles aux créateurs d’instances Lab](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images).

| Paramètres du compte lab | Instructions |
| ------------------- | ------------ |
|Image de la Place de marché| Activez l’image Windows 10 Pro ou Windows 10 Pro N pour l’utiliser dans votre compte de laboratoire.|

### <a name="licensing-server"></a>Serveur de licences

L’un des types de licences proposés par ArcGIS Desktop est la [licence d’utilisation simultanée](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Pour cela, vous devez installer ArcGIS License Manager sur votre serveur de licences.  Le gestionnaire de licences garde une trace du nombre de copies du logiciel qui peuvent être exécutées en même temps.  Pour plus d’informations sur la configuration du gestionnaire de licences sur votre serveur, consultez le [guide de License Manager](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

En général, le serveur de licences se trouve sur le réseau local ou est hébergé sur une machine virtuelle Azure au sein d’un réseau virtuel Azure.  Une fois votre serveur de licences configuré, vous devez procéder au [Peering entre le réseau virtuel](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network) et votre [compte de laboratoire](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account).  Cet appairage doit être effectué avant de créer l’instance Lab afin que les machines virtuelles Lab puissent accéder au serveur de licences et inversement.

Pour plus d’informations, consultez [Configuration d’un serveur de licences comme ressource partagée](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Paramètres du labo

La taille de la machine virtuelle que nous recommandons d’utiliser pour ArcGIS Desktop dépend des applications, des extensions et des versions spécifiques que les étudiants utiliseront.  La taille de la machine virtuelle dépend également des charges de travail que les étudiants sont censés exécuter.  Pour vous aider à déterminer la taille de la machine virtuelle, consultez la [configuration requise d’ArcGIS Desktop](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm).  Une fois que vous avez identifié la taille potentielle de la machine virtuelle, nous vous recommandons de tester les charges de travail de vos étudiants afin de vous assurer que les performances sont adéquates.

Dans cet article, nous vous recommandons d’utiliser la [taille **moyenne** de machine virtuelle](administrator-guide.md#vm-sizing) pour la version [10.7.1 d’ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), en supposant qu’aucune autre extension d’ArcGIS Desktop n’est utilisée.  Toutefois, selon les besoins de votre classe, vous pouvez avoir besoin d’une taille de machine virtuelle **Grande** ou même **GPU de petite/moyenne taille (visualisation)** .  Par exemple, l’[extension Spatial Analyst](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) qui est incluse avec ArcGIS Desktop prend en charge un GPU pour améliorer les performances, mais ne nécessite pas l’utilisation d’un GPU.

| Paramètre de labo | Valeur et description |
| ------------ | ------------------ |
|Taille de la machine virtuelle| **Moyen**.  Taille idéale pour les bases de données relationnelles, la mise en cache en mémoire et l’analyse.|  

### <a name="template-machine"></a>Machine modèle

Les étapes de cette section montrent comment configurer le modèle de machine virtuelle :

1.  Démarrez la machine virtuelle de modèle et connectez-vous à l’ordinateur à l’aide du protocole RDP.

2.  Téléchargez et installez les composants d’ArcGIS Desktop en suivant les instructions d’ESRI.  Ces étapes incluent l’attribution du gestionnaire de licences pour les licences d’utilisation simultanée : 
    - [Introduction à l’installation et à la configuration d’ArcGIS Desktop](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Configurez le stockage de sauvegarde externe pour les étudiants.  Les étudiants peuvent enregistrer des fichiers directement sur la machine virtuelle qui leur a été attribuée, car toutes les modifications qu’ils apportent sont enregistrées d’une session à l’autre.  Toutefois, nous recommandons aux étudiants de sauvegarder leur travail sur un stockage externe à leur machine virtuelle pour plusieurs raisons :
    - Pour permettre aux étudiants d’accéder à leur travail après la fin du cours et du labo.  
    - Au cas où la machine virtuelle de l’étudiant serait dans un mauvais état et que son image devrait être [réinitialisée](how-to-set-virtual-machine-passwords.md#reset-vms).

    Avec ArcGIS, chaque étudiant doit sauvegarder les fichiers suivants à la fin de chaque session de travail :

    - Le fichier mxd, qui stocke les informations de mise en page d’un projet.
    - Les géodatabases fichier, qui stockent toutes les données générées par ArcGIS.
    - Toutes les autres données que l’étudiant peut utiliser, telles que les fichiers raster, les fichiers de forme, les fichiers GeoTIFF, etc.

    Nous vous recommandons d’utiliser OneDrive pour le stockage de sauvegarde.  Pour configurer OneDrive sur la machine virtuelle du modèle, suivez les étapes décrites dans l’article [Installer et configurer OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Enfin, [publiez](how-to-create-manage-template.md#publish-the-template-vm) la machine virtuelle de modèle pour créer la machine virtuelle des étudiants.

### <a name="auto-shutdown-and-disconnect-settings"></a>Paramètres d’arrêt automatique et de déconnexion

Les [paramètres d’arrêt automatique et de déconnexion](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) d’un labo permettent de s’assurer que la machine virtuelle d’un étudiant est arrêtée lorsqu’elle n’est pas utilisée.  Ces paramètres doivent être définis en fonction des types de charges de travail que vos étudiants effectueront afin que leur machine virtuelle ne s’arrête pas au milieu de leur travail.  Par exemple, le paramètre **Déconnecter les utilisateurs lorsque les machines virtuelles sont inactives** déconnecte l’étudiant de sa session RDP après qu’aucune entrée de souris ou de clavier n’ait été détectée pendant un laps de temps spécifié.  Ce paramètre doit accorder suffisamment de temps aux charges de travail où l’étudiant n’utilise pas activement la souris ou le clavier, par exemple pour exécuter des requêtes longues ou attendre le rendu.

Pour ArcGIS, nous vous recommandons d’utiliser les valeurs suivantes pour ces paramètres :
- Déconnecter les utilisateurs quand les machines virtuelles sont inactives
    - 30 minutes après la détection de l’état d’inactivité
- Arrêter les machines virtuelles quand les utilisateurs se déconnectent
    - 15 minutes après la déconnexion de l’utilisateur

## <a name="cost"></a>Coût

Nous allons aborder une estimation de coût possible pour cette classe. Cette estimation n’inclut pas le coût d’utilisation du serveur de licences. Nous allons utiliser une classe de 25 élèves. Nous prévoyons 20 heures de temps de classe. En outre, chaque étudiant obtient un quota de 10 heures pour les devoirs ou travaux en dehors des heures de cours planifiées. La taille de machine virtuelle que nous avons choisie était **Moyenne**, soit 42 unités Lab.

25 étudiants \* (20 heures planifiées + 10 heures de quota) \* 42 unités Lab * 0,01 USD par heure = 315 USD

>[!IMPORTANT]
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour en savoir plus sur les tarifs actuels, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo.

- [Créer et gérer un modèle](how-to-create-manage-template.md)
- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)