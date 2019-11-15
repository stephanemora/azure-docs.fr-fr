---
title: Matrice de prise en charge Azure Migrate pour l’évaluation et la migration de serveurs physiques
description: Résume les paramètres et les limites de l'évaluation de serveur physique et de la migration Hyper-V à l'aide du service Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 902e89305aa24e8cbb6d2dd84d6a61c710d11637
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715377"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Matrice de prise en charge pour l’évaluation et la migration de serveurs physiques

Vous pouvez utiliser le [service Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article récapitule les paramètres et les limites de la prise en charge de l’évaluation et la migration de serveurs physiques locaux.

> [!NOTE]
> Si vous ne voyez pas encore dans le portail Azure Migrate des fonctionnalités mentionnées ici, patientez un peu. Elles apparaîtront au cours de la semaine suivante ou un peu plus tard.


## <a name="physical-server-scenarios"></a>Scénarios avec serveurs physiques

Le tableau récapitule les scénarios pris en charge pour les serveurs physiques.

**Déploiement** | **Détails***
--- | ---
**Évaluer les serveurs physiques locaux** | [Configurez](tutorial-prepare-physical.md) votre première évaluation.
**Migrate physical servers to Azure (Migrer des serveurs physiques vers Azure)** | [Testez](tutorial-migrate-physical-virtual-machines.md) une migration vers Azure.


## <a name="azure-migrate-projects"></a>Projets Azure Migrate

**Support** | **Détails**
--- | ---
**Autorisations Azure** | Vous avez besoin d’autorisations Contributeur ou Propriétaire dans l’abonnement pour créer un projet Azure Migrate.
**Serveurs physiques** | Évaluez jusqu’à 250 serveurs physiques dans un même projet. Vous pouvez avoir plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des serveurs physiques et des machines virtuelles VMware et Hyper-V, jusqu’aux limites d’évaluation.
**Zone géographique** | Vous pouvez créer un projet Azure Migrate dans un certain nombre de zones géographiques. Même si vous pouvez créer des projets dans des zones géographiques spécifiques, vous pouvez néanmoins évaluer ou migrer des machines pour d’autres emplacements cibles. La zone géographique du projet est uniquement utilisée pour stocker les métadonnées détectées.

  **Zone géographique** | **Emplacement de stockage des métadonnées**
  --- | ---
  Azure Government | Gouvernement américain - Virginie
  Asie-Pacifique | Asie Est ou Asie Sud-Est
  Australie | Australie Est ou Australie Sud-Est
  Brésil | Brésil Sud
  Canada | Canada Centre ou Canada Est
  Europe | Europe Nord ou Europe Ouest
  France | France Centre
  Inde | Inde Centre ou Inde Sud
  Japon |  Japon Est ou Japon Ouest
  Corée du Sud | Corée Centre ou Corée Sud
  Royaume-Uni | Royaume-Uni Sud ou Royaume-Uni Ouest
  États-Unis | USA Centre ou USA Ouest 2


 > [!NOTE]
 > La prise en charge d’Azure Government est actuellement disponible pour l’[ancienne version](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate uniquement.


## <a name="assessment-physical-server-requirements"></a>Exigences en matière d'évaluation des serveurs physiques

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement de serveur physique**       | Le serveur physique peut être autonome ou déployé dans un cluster. |
| **autorisations**           | **Windows :** Configurez un compte d’utilisateur local sur tous les serveurs Windows que vous souhaitez inclure dans la découverte. Le compte d’utilisateur doit être ajouté à ces utilisateurs du Bureau à distance, utilisateurs de l’Analyseur de performances et utilisateurs du journal de performances. <br/> **Linux :** Vous devez disposer d’un compte racine sur les serveurs Linux que vous souhaitez découvrir. |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure. |


## <a name="assessment-appliance-requirements"></a>Évaluation - Exigences relatives à l’appliance

Pour l’évaluation, Azure Migrate exécute une appliance légère pour découvrir les serveurs physiques et envoyer les métadonnées et les données de performances du serveur à Azure Migrate. L’appliance peut s’exécuter sur un serveur physique ou une machine virtuelle que vous configurez à l’aide d’un script PowerShell téléchargé sur le portail Azure. Le tableau suivant récapitule les exigences de l’appliance.

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Étapes de déploiement d’appliance**   |  Vous déployez l’appliance sur un serveur physique ou une machine virtuelle.<br/>  La machine hôte doit exécuter Windows Server 2012 R2 ou une version ultérieure.<br/> L’hôte a besoin de suffisamment d’espace pour allouer 16 Go de RAM, 8 processeurs virtuels, environ 80 Go d’espace de stockage et un commutateur externe à la machine virtuelle de l’appliance.<br/> L'appliance nécessite une adresse IP statique ou dynamique et un accès Internet.
| **Projet Azure Migrate**  |  Une appliance peut être associée à un seul projet.<br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> Vous pouvez évaluer jusqu’à 35 000 machines par projet.
| **Découverte**              | Une seule appliance peut découvrir jusqu’à 200 serveurs.
| **Groupe d’évaluation**       | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.
| **Évaluation**             | Vous pouvez évaluer jusqu’à 35 000 machines par évaluation.


## <a name="assessment-appliance-url-access"></a>Évaluation - accès à l’URL de l’appliance

Pour évaluer des machines virtuelles, l'appliance Azure Migrate a besoin d'une connectivité Internet.

- Quand vous déployez l’appliance, Azure Migrate effectue un contrôle de connectivité aux URL récapitulées dans le tableau ci-dessous.
- Si vous utilisez un proxy basé sur URL, autorisez l'accès aux URL du tableau, en vérifiant que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.
- Si vous disposez d'un proxy d'interception, vous devrez peut-être importer le certificat de serveur depuis le serveur proxy vers l'appliance.


**URL** | **Détails**  
--- | ---
*. portal.azure.com | Accès au portail Azure
\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | Connectez-vous à votre abonnement Azure :
*.microsoftonline.com <br/> *.microsoftonline-p.com | Création d'applications Azure Active Directory pour les communications de l’appliance au service.
management.azure.com | Création d'applications Azure Active Directory pour les communications de l’appliance au service.
dc.services.visualstudio.com | Enregistrement et surveillance
*.vault.azure.net | Gérez les secrets dans Azure Key Vault lorsque vous communiquez entre l'appliance et le service.
aka.ms/* | Autorisez l’accès à des liens aka.
https://download.microsoft.com/download/* | Autorise les téléchargements à partir du site de téléchargement Microsoft.



## <a name="assessment-port-requirements"></a>Évaluation - Exigences relatives aux ports

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ``` https://<appliance-ip-or-name>:44368 ```<br/> Connexions sortantes sur les ports 443, 5671 et 5672 pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
**Serveurs physiques** | **Windows :** Connexions entrantes sur les ports 443 et 5989 pour extraire les métadonnées de configuration et de performance des serveurs Windows. <br/> **Linux :**  Connexions entrantes sur le port 22 (UDP) pour extraire les métadonnées de configuration et de performance des serveurs Linux. |


## <a name="next-steps"></a>Étapes suivantes

[Préparer les serveurs physiques en vue de leur évaluation](tutorial-prepare-physical.md) pour l’évaluation et la migration des serveurs physiques.
