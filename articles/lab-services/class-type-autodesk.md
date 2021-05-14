---
title: Configurer un labo avec Autodesk en utilisant Azure Lab Services
description: Découvrez comment configurer des labos pour donner des cours d’ingénierie avec Autodesk.
author: nicolela
ms.topic: article
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 6cc983907349ca8eab0731b18c18d526f2b75ba5
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108281114"
---
# <a name="set-up-labs-for-autodesk"></a>Configurer des labos pour Autodesk

Cet article explique comment configurer les logiciels Autodesk Inventor et Autodesk Revit pour des cours d’ingénierie :
- Les logiciels de [conception assistée par ordinateur (CAO) Inventor](https://www.autodesk.com/products/inventor/new-features) et de [fabrication assistée par ordinateur (CAM)](https://www.autodesk.com/products/inventor-cam/overview) assurent une modélisation 3D et sont utilisés dans la conception technique.
- [Revit](https://www.autodesk.com/products/revit/overview) est utilisé en architecture pour modéliser les données de bâtiments en 3D (BIM).

Autodesk est souvent utilisé dans les universités et les établissements d’enseignement primaire.  Par exemple, aux États-Unis, AutoDesk fait partie du programme scolaire [Project Lead the Way (PLTW)](./class-type-pltw.md).

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d’informations sur la création d’un compte de laboratoire, consultez notre tutoriel sur [la configuration d’un compte de labo](./tutorial-setup-lab-account.md). Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres de votre compte Lab (cf. tableau suivant). Pour plus d’informations sur l’activation des images de la Place de marché Azure, consultez [Spécification des images de la Place de marché Azure accessibles aux créateurs d’instances Lab](./specify-marketplace-images.md).

| Paramètres du compte lab | Instructions |
| -------------------- | ----- |
| Image de la Place de marché | Activez l’image Windows 10 pour l’utiliser dans votre compte de labo. |

### <a name="lab-settings"></a>Paramètres du labo
La taille de la machine virtuelle que nous vous recommandons dépend des types de charges de travail qui sont soumises aux élèves.  Nous préconisons l’utilisation de la petite taille de GPU (visualisation).

| Paramètre de labo | Valeur et description |
| ------------ | ------------------ |
| Taille de la machine virtuelle | **GPU de petite taille (visualisation)**<br>Idéal pour la visualisation à distance, le streaming, les jeux et l’encodage avec des infrastructures de type OpenGL et DirectX. | 

> [!NOTE]
> La machine virtuelle de **GPU de petite taille (visualisation)** est configurée pour permettre une expérience graphique très performante. Pour plus d’informations sur cette taille de machine virtuelle, consultez l’article sur [la configuration d’un laboratoire avec des GPU](./how-to-setup-lab-gpu.md).

### <a name="license-server"></a>Serveur de licences
Si vous envisagez d’utiliser le modèle de licence réseau Autodesk, vous serez amené à accéder à un serveur de licences.  Pour plus d’informations, lisez l’article d’Autodesk sur l’[administration des licences réseau](https://knowledge.autodesk.com/customer-service/network-license-administration/network-deployment/preparing-for-deployment/determining-installation-type).

Pour utiliser des licences réseau avec le logiciel Autodesk, [AutoDesk explique de manière détaillée](https://knowledge.autodesk.com/customer-service/network-license-administration/install-and-configure-network-license) comment installer le gestionnaire de licences réseau Autodesk sur un serveur de licences.  En général, ce serveur de licences se trouve sur le réseau local ou est hébergé sur une machine virtuelle Azure au sein du réseau virtuel Azure.

Une fois votre serveur de licences configuré, vous devez procéder au [peering entre le réseau virtuel](./how-to-connect-peer-virtual-network.md) et votre [compte Lab](./tutorial-setup-lab-account.md). Cet appairage doit être effectué *avant* de créer l’instance Lab afin que les machines virtuelles Lab puissent accéder au serveur de licences et inversement.

Les fichiers de licence générés par Autodesk incorporent l’adresse MAC du serveur de licences.  Si vous décidez d’héberger votre serveur de licences à l’aide d’une machine virtuelle Azure, il est important de vérifier que l’adresse MAC de votre serveur de licences ne change pas. Si elle évolue, vous devrez regénérer vos fichiers de licence. Pour éviter qu’elle ne change, procédez comme suit :

- [Définissez une adresse IP privée et une adresse MAC statiques](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) pour la machine virtuelle Azure qui héberge votre serveur de licences.
- Veillez à configurer votre compte Lab et le réseau virtuel du serveur de licences dans une région ou à un emplacement qui dispose d’une capacité de machine virtuelle suffisante pour ne pas avoir à déplacer par la suite ces ressources dans une nouvelle région ou un nouvel emplacement.

Pour plus d’informations, consultez [Configuration d’un serveur de licences comme ressource partagée](./how-to-create-a-lab-with-shared-resource.md).

> [!WARNING]
> N’oubliez pas d’[appairer le réseau virtuel](./how-to-connect-peer-virtual-network.md) du compte de labo au réseau virtuel du serveur de licences **avant** de créer le labo.

### <a name="template-machine"></a>Machine modèle
Les étapes de cette section montrent comment configurer le modèle de machine virtuelle :

1. Démarrez la machine virtuelle de modèle et connectez-vous à la machine.

1. Téléchargez et installez Inventor et Revit en suivant les [instructions d’AutoDesk](https://knowledge.autodesk.com/customer-service/download-install/install-software).  Quand vous y êtes invité, spécifiez le nom d’ordinateur de votre serveur de licences.

1.  Enfin, publiez la machine virtuelle de modèle pour créer les machines virtuelles des élèves.

## <a name="cost"></a>Coût
Penchons-nous sur un exemple d’estimation du coût pour ce cours.  Cette estimation n’inclut pas le coût d’exécution d’un serveur de licences. Prenons une classe de 25 élèves, chacun ayant 20 heures de cours planifiées.  Chaque élève dispose également d’un quota de 10 heures pour faire ses devoirs et activités à la maison en dehors des heures de cours prévues.  La taille de machine virtuelle que nous avons choisie était avec un **GPU de petite taille (visualisation)** , soit 160 unités Lab.

- 25 élèves &times; (20 heures planifiées + 10 heures de quota) &times; 160 unités Lab &times; 0,01 USD par heure = 1 200,00 USD

> [!IMPORTANT] 
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour connaître les tarifs actuels, consultez [Tarifs Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Étapes suivantes

Au fil de la configuration de votre instance Lab, consultez les articles suivants :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir les quotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users) 
