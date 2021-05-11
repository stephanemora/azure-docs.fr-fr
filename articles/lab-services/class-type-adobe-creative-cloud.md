---
title: Configurer un labo avec Adobe Creative Cloud à l'aide d'Azure Lab Services | Microsoft Docs
description: Apprenez à configurer un labo dans les cours dédiés aux arts numériques et au multimédia qui utilisent Adobe Creative Cloud.
author: nicolela
ms.topic: article
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: ef4245a3c17f95cf7e04b866939d4e92e39411c8
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108281060"
---
# <a name="set-up-a-lab-for-adobe-creative-cloud"></a>Configurer un labo pour Adobe Creative Cloud
[Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) est un ensemble d'applications de bureau et de services web utilisés pour la photographie, la conception graphique, le montage vidéo, le développement web, etc.  Des universités et des établissements scolaires utilisent Creative Cloud dans le cadre de leurs cours dédiés aux arts numériques et au multimédia.  Certains des processus multimédias de Creative Cloud peuvent nécessiter une puissance de calcul et de visualisation (GPU) supérieure à celle d'une tablette, d'un ordinateur portable ou d'une station de travail classique.  Azure Lab Services vous permet de faire votre choix parmi différentes tailles de machine virtuelle (tailles GPU comprises).

Dans cet article, nous allons vous apprendre à configurer un cours qui utilise Creative Cloud.

## <a name="licensing"></a>Licences
Pour utiliser Creative Cloud sur une machine virtuelle de labo, vous devez utiliser une [Licence d'utilisateur nommé](https://helpx.adobe.com/enterprise/kb/technical-support-boundaries-virtualized-server-based.html#main_Licensing_considerations), qui est le seul type de licence à prendre en charge le déploiement sur une machine virtuelle.  Chaque machine virtuelle de labo dispose d'un accès à Internet pour permettre à vos étudiants d'activer les applications Creative Cloud en se connectant au logiciel.  Une fois qu'un étudiant s'est connecté, son jeton d'authentification est mis en cache dans le profil utilisateur afin qu'il n'ait pas à se reconnecter sur sa machine virtuelle.  Pour plus d'informations, consultez l'[article d'Adobe consacré aux licences](https://helpx.adobe.com/enterprise/using/licensing.html).

## <a name="lab-configuration"></a>Configuration du laboratoire
Pour configurer ce labo, vous avez besoin d’un abonnement Azure et d’un compte Lab pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services. Pour plus d'informations sur la création d'un compte Lab, consultez notre tutoriel consacré à la [configuration d'un compte Lab](./tutorial-setup-lab-account.md). Vous pouvez également utiliser un compte Lab existant.

### <a name="lab-account-settings"></a>Paramètres du compte Lab

Activez les paramètres décrits dans le tableau ci-dessous pour le compte lab. Pour plus d’informations sur l’activation des images de la Place de marché, consultez l’article [Spécifier les images de la Place de marché accessibles aux créateurs de labo](./specify-marketplace-images.md).

| Paramètres du compte lab | Instructions |
| ------------------- | ------------ |
|Image de la Place de marché| Activez l'image Windows 10 pour l'utiliser dans votre compte Lab.|

### <a name="lab-settings"></a>Paramètres du labo

La taille de machine virtuelle à utiliser pour votre labo dépendra des types de projets que vos étudiants créeront.  La plupart des [applications Creative Cloud](https://helpx.adobe.com/creative-cloud/system-requirements.html) prennent en charge l'accélération basée sur le GPU et nécessitent un GPU pour que les fonctionnalités soient opérationnelles.  Pour sélectionner la taille de machine virtuelle appropriée et bénéficier de performances adéquates, nous vous recommandons de tester les projets créés par vos étudiants.  Le tableau ci-dessous indique la [taille de machine virtuelle](https://docs.microsoft.com/azure/lab-services/administrator-guide#vm-sizing) qu'il est recommandé d'utiliser avec Creative Cloud.  

| Paramètres du labo | Valeur/instructions |
| ------------ | ------------------ |
|Taille de la machine virtuelle| **GPU de petite taille (visualisation)** .  Cette machine virtuelle est optimisée pour la visualisation à distance, la diffusion en continu, les jeux et l’encodage avec des infrastructures comme OpenGL ou DirectX.|  
|Image de machine virtuelle| Windows 10 |

> [!NOTE]
> La machine virtuelle **GPU de petite taille (visualisation)** est configurée pour offrir des performances optimales en termes d'expérience graphique, et elle est conforme à la [configuration requise par Adobe pour chaque application](https://helpx.adobe.com/creative-cloud/system-requirements.html).  Veillez à choisir GPU de petite taille (visualisation) et non GPU de petite taille (calcul).  Pour plus d’informations sur cette taille de machine virtuelle, consultez l’article sur [la configuration d’un laboratoire avec des GPU](./how-to-setup-lab-gpu.md).

## <a name="template-virtual-machine-configuration"></a>Configuration de la machine virtuelle de modèle

### <a name="creative-cloud-deployment-package"></a>Package de déploiement Creative Cloud
Pour installer Creative Cloud, un package de déploiement est nécessaire. En général, le package de déploiement est créé par votre service informatique à l'aide de la console d'administration d'Adobe.  Lorsque le service informatique crée le package de déploiement, il a également la possibilité d'activer le libre-service.  Il existe plusieurs façons d'activer le libre-service pour le package de déploiement :
-    Créez un package en libre-service.
-    Créez un package managé dans lequel les privilèges élevés relatifs au libre-service sont activés.

Lorsque le libre-service est activé, vous n'installez pas l'intégralité de l'ensemble d'applications Creative Cloud.  Au lieu de cela, les étudiants peuvent installer eux-mêmes les applications à l'aide de l'application de bureau Creative Cloud.  Les principaux avantages de cette approche sont les suivants :
- L'installation complète de Creative Cloud représente environ 25 Go.  Lorsque les étudiants n'installent que les applications dont ils ont besoin à la demande, l'espace disque est optimisé. La taille de disque des machines virtuelles Lab est de 128 Go.
- Vous pouvez choisir d'installer un sous-ensemble d'applications sur le modèle de machine virtuelle avant de le publier.  Certaines applications seront ainsi installées par défaut sur les machines virtuelles des étudiants, et ceux-ci pourront en ajouter d'autres par eux-mêmes si nécessaire.
- Vous n'avez pas à republier le modèle de machine virtuelle, car les étudiants peuvent à tout moment installer des applications supplémentaires sur leur machine virtuelle pendant toute la durée du labo.  Dans le cas contraire, le service informatique ou l'enseignant doit installer des applications supplémentaires sur le modèle de machine virtuelle et le republier.  Toute nouvelle publication entraîne la réinitialisation des machines virtuelles des étudiants, et tout travail non enregistré en externe est perdu.

Si vous utilisez un package de déploiement managé dans lequel l'option libre-service est désactivée, les étudiants n'ont pas la possibilité d'installer leurs propres applications.  Dans ce cas, le service informatique doit spécifier les applications Creative Cloud à installer.

Pour plus d'informations, consultez la [procédure Adobe de création d'un package](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/create-nul-packages.ug.html).

### <a name="install-creative-cloud"></a>Installer Creative Cloud
Une fois le modèle de machine virtuelle créé, suivez les étapes ci-dessous pour configurer votre modèle de machine virtuelle de labo avec Creative Cloud.
1. Démarrez le modèle de machine virtuelle et connectez-vous à l'aide du protocole RDP.
1. Pour installer Creative Cloud, téléchargez le package de déploiement fourni par le service informatique, ou téléchargez-le directement depuis la [console d'administration d'Adobe](https://adminconsole.adobe.com/).
1. Exécutez le fichier du package de déploiement.  Selon que le libre-service est activé ou non, l'application de bureau Creative Cloud et/ou les applications Creative Cloud spécifiées sont installées.
Pour plus d'informations, consultez la [procédure de déploiement d'Adobe](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/deploy-packages.ug.html).
1. Une fois le modèle de machine virtuelle configuré, [publiez l'image du modèle de machine virtuelle](how-to-create-manage-template.md) à utiliser pour créer toutes les machines virtuelles des étudiants dans le cadre du labo.

### <a name="storage"></a>Stockage
Comme mentionné précédemment, la taille de disque des machines virtuelles Azure Lab est de 128 Go.  Si vos étudiants ont besoin d'espace de stockage supplémentaire pour enregistrer des éléments multimédias volumineux ou s'ils ont besoin d'accéder à des éléments multimédias partagés, n'hésitez pas à avoir recours à un espace de stockage de fichiers externe.  Pour plus d’informations, consultez les articles suivants :
-    [Utilisation du stockage de fichiers externes dans Lab Services](how-to-attach-external-storage.md)
-    [Installer et configurer OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive)

### <a name="save-template-vm-image"></a>Enregistrer l'image d'un modèle de machine virtuelle
Pensez à enregistrer votre modèle de machine virtuelle en vue d'une utilisation ultérieure.  Pour enregistrer le modèle de machine virtuelle, consultez [Enregistrer une image dans Shared Image Gallery](./how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery).
- Lorsque le libre-service est *activé*, le bureau Creative Cloud est installé dans l'image du modèle de machine virtuelle.  Les enseignants peuvent ensuite réutiliser cette image pour créer des labos et choisir les applications Creative Cloud à installer.  Cela permet de réduire les frais informatiques, car les enseignants peuvent configurer des labos en toute indépendance et bénéficier d'un contrôle total sur l'installation des applications Creative Cloud nécessaires à leurs cours.
- Lorsque le libre-service est *désactivé*, les applications Creative Cloud spécifiées sont déjà installées dans l'image du modèle de machine virtuelle.  Les enseignants peuvent réutiliser cette image pour créer des labos, mais ils ne peuvent pas installer d'autres applications Creative Cloud.

## <a name="cost"></a>Coût

Dans cette section, nous allons examiner une estimation de coût possible pour ce cours.  Nous nous baserons sur une classe de 25 étudiants avec 20 heures de cours programmées.  En outre, chaque étudiant obtient un quota de 10 heures pour les devoirs ou travaux en dehors des heures de cours planifiées. La taille de machine virtuelle que nous avons choisie était avec un **GPU de petite taille (visualisation)** , soit 160 unités Lab.

25 étudiants *\* (20 heures planifiées + 10 heures de quota) *\* 160 unités Lab * 0,01 USD par heure = 1200,00 USD

>[!IMPORTANT]
> L’estimation du coût est fournie à titre d’exemple uniquement.  Pour en savoir plus sur les tarifs actuels, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo.

- [Créer et gérer un modèle](how-to-create-manage-template.md)
- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)