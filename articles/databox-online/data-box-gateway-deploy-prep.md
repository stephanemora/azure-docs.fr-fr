---
title: Didacticiel sur la préparation du portail Azure pour déployer Data Box Gateway | Microsoft Docs
description: Le premier didacticiel visant à déployer Azure Data Box Gateway implique la préparation du Portail Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6db713984b62ce3db48b2e72a4b117696bdd6add
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452970"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Didacticiel : Préparer le déploiement d’Azure Data Box Gateway (préversion)


Ce didacticiel est le premier d’une série de didacticiels permettant de déployer entièrement Azure Data Box Gateway. Il explique comment préparer le Portail Azure pour déployer la ressource Data Box Gateway. 

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Télécharger l'image de l’appareil virtuel
> * Obtenir la clé d'activation

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


> [!IMPORTANT]
> - Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 

### <a name="get-started"></a>Prise en main

Pour déployer Data Box Gateway, consultez les didacticiels suivants dans l’ordre indiqué.

| **#** | **Dans cette étape** | **Utilisez ces documents** |
| --- | --- | --- | 
| 1. |**[Préparer le Portail Azure pour Data Box Gateway](data-box-gateway-deploy-prep.md)** |Créez et configurez votre ressource Data Box Gateway avant l’approvisionnement d’un appareil virtuel Data Box Gateway. |
| 2. |**[Approvisionner Azure Data Box Gateway dans Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Approvisionner Azure Data Box Gateway dans VMware](data-box-gateway-deploy-provision-vmware.md)**|Pour Hyper-V, approvisionnez un appareil virtuel Data Box Gateway et connectez-vous à celui-ci sur un système hôte exécutant Hyper-V sur Windows Server 2016, Windows Server 2012 ou R2. <br><br><br> Pour VMware, approvisionnez un appareil virtuel Data Box Gateway et connectez-vous à celui-ci sur un système hôte exécutant VMware ESXi 6.0 ou 6.5.<br></br> |
| 3. |**[Se connecter à Data Box Gateway, le configurer et l’activer](data-box-gateway-deploy-connect-setup-activate.md)** |Connectez-vous à l’interface utilisateur web locale, terminez l’installation de l’appareil et activez-le. Vous pouvez ensuite configurer les partages SMB.  |
| 4. |**[Transférer des données avec Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Ajoutez des partages et connectez-vous à des partages via SMB ou NFS. |

Vous pouvez maintenant commencer à configurer le portail Azure.

## <a name="prerequisites"></a>Prérequis

Dans les informations suivantes, vous trouverez la configuration requise pour votre ressource Data Box Gateway, votre appareil Data Box Gateway et le réseau du centre de données.

### <a name="for-the-data-box-gateway-resource"></a>Pour la ressource Data Box Gateway

Avant de commencer, assurez-vous que :

* Votre abonnement Microsoft Azure doit être activé pour la ressource Data Box Gateway.
* Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

### <a name="for-the-data-box-gateway-device"></a>Pour l’appareil Data Box Gateway

Avant de déployer un appareil virtuel, assurez-vous que :

* Vous avez accès à un système hôte exécutant Hyper-V sur Windows Server 2012 R2 ou version ultérieure ou VMware (ESXi 6.0 ou 6.5) et permettant de configurer un appareil.
* Le système hôte est en mesure de dédier les ressources suivantes pour approvisionner votre appareil virtuel Data Box :
  
  * Un minimum de 4 cœurs.
  * Au moins 8 Go de RAM. 
  * Une interface réseau.
  * Un disque de système d’exploitation de 250 Go.
  * Un disque virtuel de 2 To pour les données système.

### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

* Le réseau de votre centre de données est configuré conformément à la configuration réseau requise pour votre appareil Data Box Gateway. Pour plus d’informations, consultez le document [Configuration requise pour Data Box Gateway](data-box-gateway-system-requirements.md).

* Votre instance Data Box Gateway est dotée d’une bande passante Internet dédiée de 20 Mbits/s (ou plus) disponible à tout moment. La bande passante ne doit pas être partagée avec d’autres applications. Si vous utilisez la limitation de bande passante et que vous souhaitez qu’elle soit fonctionnelle, nous vous recommandons d’utiliser une bande passante Internet de 32 Mbits/s ou plus.

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

Pour créer une ressource Data Box Gateway, procédez comme suit. 

Si vous disposez d’une ressource Data Box Gateway existante pour gérer vos appareils virtuels, ignorez cette étape et accédez à [Obtenir la clé d’activation](#get-the-activation-key).

Pour créer une ressource Data Box, procédez comme suit dans le Portail Azure.
1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter à la préversion du portail Azure en suivant cette URL : [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Sélectionnez l’abonnement que vous souhaitez utiliser pour la préversion de Data Box Edge. Sélectionnez la région dans laquelle vous souhaitez déployer la ressource Data Box Edge. Pour l’option **Data Box Gateway**, cliquez sur **Créer**.

    ![Rechercher le service Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Pour la nouvelle ressource, entrez ou sélectionnez les informations suivantes :
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom de la ressource   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom doit être compris entre 2 et 50 caractères, et se composer uniquement de lettres, de chiffres et de traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
    |Abonnement    |L’abonnement est lié à votre compte de facturation. |
    |Groupe de ressources  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Lieu     |Pour cette version, les zones USA Est, USA Ouest 2, Asie Sud-Est et Europe Ouest sont disponibles. <br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|
    
    ![Créer la ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Cliquez sur **OK**.
 
La création de la ressource prend quelques minutes. Un message vous informe que la ressource a bien été créée.


## <a name="download-the-virtual-device-image"></a>Télécharger l'image de l’appareil virtuel

Une fois que la ressource Data Box Gateway est créée, téléchargez l'image de l’appareil virtuel appropriée pour approvisionner un appareil virtuel sur le système hôte. Les images d’appareil virtuel sont spécifiques au système d'exploitation et peuvent être téléchargées depuis la page **Démarrage rapide** de votre ressource dans le Portail Azure.

> [!IMPORTANT]
> Le logiciel en cours d’exécution sur Data Box Gateway peut seulement être utilisé avec la ressource Data Box Gateway.


Procédez comme suit dans le [portail Azure](https://portal.azure.com/).

1. Cliquez sur la ressource que vous avez créée, puis sur **Vue d’ensemble**. Si vous disposez d’une ressource Azure Data Box Gateway existante, cliquez sur la ressource, puis accédez à **Vue d’ensemble**.

    ![Nouvelle ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. Dans le démarrage rapide du volet de droite, cliquez sur le lien correspondant à l’image que vous souhaitez télécharger. Les fichiers image ont une taille d’environ 4,8 Go.
   
   * [VHD pour Hyper-V sur Windows Server 2012 R2 et versions ultérieures](https://aka.ms/dbe-vhdx-2012)
   * [VMDK pour VMWare ESXi 6.0 ou 6.5](https://aka.ms/dbe-vmdk)

5. Téléchargez et décompressez le fichier sur un lecteur local et notez l’emplacement du fichier décompressé.


## <a name="get-the-activation-key"></a>Obtenir la clé d'activation

Une fois que la ressource Data Box Gateway est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Data Box Gateway et à le connecter à la ressource.

La clé d’activation sert à inscrire tous les appareils Data Box Gateway devant être activés auprès de votre ressource Data Box Gateway. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Cliquez sur la ressource que vous avez créée, puis sur **Vue d’ensemble**.

    ![Nouvelle ressource Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Cliquez sur **Générer une clé** pour créer une clé d’activation. Cliquez sur l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.

    ![Obtenir une clé d’activation](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La clé d’activation expire 3 jours après sa création. 
> - Si la clé expire, générez une nouvelle clé. L’ancienne clé n'est plus valide.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Gateway et avez notamment appris à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Télécharger l'image de l’appareil virtuel
> * Obtenir la clé d'activation

Passez au didacticiel suivant pour découvrir comment approvisionner une machine virtuelle pour Data Box Gateway. Selon votre système d'exploitation hôte, consultez les instructions détaillées dans :

> [!div class="nextstepaction"]
> [Approvisionner une instance Data Box Gateway dans Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

Ou

> [!div class="nextstepaction"]
> [Approvisionner une instance Data Box Gateway dans VMware](./data-box-gateway-deploy-provision-vmware.md)


