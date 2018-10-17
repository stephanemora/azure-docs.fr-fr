---
title: Didacticiel sur la préparation du Portail Azure pour déployer Data Box Edge | Microsoft Docs
description: Le premier didacticiel visant à déployer Azure Data Box Edge implique la préparation du Portail Azure.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 79061caac3d598df79f383b9b13458ab9537cd81
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832798"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge-preview"></a>Didacticiel : Préparer le déploiement d’Azure Data Box Edge (préversion)


Ce didacticiel est le premier d’une série de didacticiels permettant de déployer entièrement Azure Data Box Edge. Il explique comment préparer le Portail Azure pour déployer la ressource Data Box Edge. 

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


> [!IMPORTANT]
> Data Box Edge est en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 

### <a name="get-started"></a>Prise en main

Pour déployer Data Box Edge, consultez les didacticiels suivants dans l’ordre indiqué.

| **#** | **Dans cette étape** | **Utilisez ces documents** |
| --- | --- | --- | 
| 1. |**[Préparer le Portail Azure pour Data Box Edge](data-box-edge-deploy-prep.md)** |Créez et configurez votre ressource Data Box Edge avant d’installer un appareil physique Data Box Edge. |
| 2. |**[Installer Data Box Edge](data-box-edge-deploy-install.md)**|Décompressez, montez en rack et branchez l’appareil physique Data Box Edge.  |
| 3. |**[Se connecter à Data Box Edge, procéder à sa configuration et à son activation](data-box-edge-deploy-connect-setup-activate.md)** |Connectez-vous à l’interface utilisateur web locale, terminez l’installation de l’appareil et activez-le. L’appareil est prêt à configurer des partages SMB ou NFS.  |
| 4. |**[Transférer des données avec Data Box Edge](data-box-edge-deploy-add-shares.md)** |Ajoutez des partages et connectez-vous à ces derniers via SMB ou NFS. |
| 5. |**[Transformer des données avec Data Box Edge](data-box-edge-deploy-configure-compute.md)** |Configurez des modules Edge sur l’appareil pour transformer les données lors de leur déplacement vers Azure. |

Vous pouvez maintenant commencer à configurer le portail Azure.

## <a name="prerequisites"></a>Prérequis

Dans cette section, vous pouvez trouver la configuration requise pour votre ressource Data Box Edge, votre appareil Data Box Edge et le réseau du centre de données.

### <a name="for-the-data-box-edge-resource"></a>Pour la ressource Data Box Edge

Avant de commencer, assurez-vous que :

* Votre abonnement Microsoft Azure doit être activé pour la ressource Data Box Edge.
* Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

### <a name="for-the-data-box-edge-device"></a>Pour l’appareil Data Box Edge

Avant de déployer un appareil physique, assurez-vous que :

- Vous avez un 1 emplacement U disponible dans un rack de 19’’ standard dans votre centre de données pour le montage en rack de l’appareil. 
- Vérifiez que vous avez accès à une surface de travail plane, stable et de niveau sur laquelle poser l’appareil en sécurité.
- Vérifiez que le site où vous envisagez de configurer l’appareil dispose d’une alimentation secteur standard provenant d’une source indépendante ou d’une unité de distribution de l’alimentation (PDU) en rack avec un onduleur (UPS).
- Vous avez accès à un appareil physique.


### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

* Le réseau de votre centre de données est configuré conformément à la configuration réseau requise pour votre appareil Data Box Edge. Pour plus d’informations, consultez le document [Data Box Edge System Requirements](data-box-gateway-system-requirements.md) (Configuration système requise Data Box Edge).

* Votre instance Data Box Edge est dotée d’une bande passante Internet dédiée de 20 Mbits/s (ou plus) disponible à tout moment. La bande passante ne doit pas être partagée avec d’autres applications. Si vous utilisez la limitation de bande passante et si vous souhaitez qu’elle soit fonctionnelle, nous vous recommandons d’utiliser une bande passante Internet de 32 Mbits/s ou plus.

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

Pour créer une ressource Data Box Edge, procédez comme suit. 

Si vous disposez d’une ressource Data Box Edge existante pour gérer votre appareil physique, ignorez cette étape et passez à la section [Obtenir la clé d’activation](#get-the-activation-key).

Pour créer une ressource Data Box, procédez comme suit dans le Portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter à la préversion du portail Azure en suivant cette URL : [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Sélectionnez l’abonnement que vous souhaitez utiliser pour la préversion de Data Box Edge. Sélectionnez la région dans laquelle vous souhaitez déployer la ressource Data Box Edge. Dans l’option **Data Box Edge**, cliquez sur **Créer**.

    ![Rechercher le service Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Pour la nouvelle ressource, entrez ou sélectionnez les informations suivantes :
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom de la ressource   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom de la ressource doit être compris entre 2 et 50 caractères, et se composer uniquement de lettres, de chiffres et de traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
    |Abonnement    |L’abonnement est lié à votre compte de facturation. |
    |Groupe de ressources  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Lieu     |Pour cette version, les zones USA Est, USA Ouest 2, Asie Sud-Est et Europe Ouest sont disponibles. <br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|
    
    ![Créer une ressource Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Cliquez sur **OK**.
 
La création de la ressource prend quelques minutes. Un message vous informe que la ressource a bien été créée.


## <a name="get-the-activation-key"></a>Obtenir la clé d'activation

Une fois que la ressource Data Box Edge est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Data Box Edge et à le connecter à la ressource. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Cliquez sur la ressource que vous avez créée, puis sur **Vue d’ensemble**.

2. Cliquez sur **Générer une clé** pour créer une clé d’activation. Cliquez sur l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.

    ![Obtenir une clé d’activation](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La clé d’activation expire 3 jours après sa création. 
> - Si la clé expire, générez une nouvelle clé. L’ancienne clé n'est plus valide.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Edge et avez notamment appris à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

Passez au didacticiel suivant pour apprendre à installer Data Box Edge. 

> [!div class="nextstepaction"]
> [Installer Data Box Edge](./data-box-edge-deploy-install.md)



