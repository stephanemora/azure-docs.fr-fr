---
title: 'Tutoriel : Préparer le portail Azure et l’environnement du centre de données pour déployer Azure Stack Edge avec GPU | Microsoft Docs'
description: Le premier tutoriel sur le déploiement d’Azure Stack Edge avec GPU implique la préparation du portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c02af8983489651d8b5b83d5d3107a534c51f067
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254676"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-with-gpu"></a>Tutoriel : Préparer le déploiement d’Azure Stack Edge avec GPU 

Ce tutoriel est le premier d’une série de tutoriels permettant de déployer entièrement Azure Stack Edge avec GPU. Il explique comment préparer le portail Azure pour déployer une ressource Azure Stack Edge.

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

### <a name="get-started"></a>Bien démarrer

Pour le déploiement d’Azure Stack Edge, vous devez d’abord préparer votre environnement. Une fois que l’environnement est prêt, suivez les étapes requises et, le cas échéant, les étapes et les procédures facultatives pour déployer complètement l’appareil. Les instructions de déploiement étape par étape indiquent le moment où vous devez effectuer chacune de ces étapes obligatoires et facultatives.

| Étape | Description |
| --- | --- |
| **Préparation** |Ces étapes doivent être effectuées en préparation du déploiement à venir. |
| **[Liste de contrôle de la configuration du déploiement](#deployment-configuration-checklist)** |Utilisez cette liste de contrôle pour rassembler et enregistrer des informations avant et pendant le déploiement. |
| **[Conditions préalables au déploiement](#prerequisites)** |Celles-ci valident que l'environnement est prêt pour le déploiement. |
|  | |
|**Didacticiels de déploiement** |Ces didacticiels sont nécessaires pour déployer votre appareil Azure Stack Edge en production. |
|**[1. Préparer le portail Azure pour Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)** |Créez et configurez votre ressource Azure Stack Edge avant d’installer un appareil physique Azure Stack Edge. |
|**[2. Installer Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md)**|Déballez, montez en rack et branchez l’appareil physique Azure Stack Edge.  |
|**[3. Se connecter à Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)** |Une fois l’appareil installé, connectez-vous à l’interface utilisateur web locale de l’appareil.  |
|**[4. Configurer les paramètres réseau pour Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configurez le réseau, y compris les paramètres de réseau de calcul et de proxy web pour votre appareil.   |
|**[5. Configurer les paramètres de l’appareil pour Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Attribuez un nom d’appareil et un domaine DNS, configurez le serveur de mise à jour et l’heure de l’appareil. |
|**[6. Configurer les paramètres de sécurité pour Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configurez des certificats pour votre appareil. Utilisez des certificats générés par l’appareil ou apportez vos propres certificats.   |
|**[7. Activer Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md)** |Utilisez la clé d’activation du service pour activer l’appareil. L’appareil est prêt à configurer des partages SMB ou NFS ou à se connecter via REST. |
|**[8. Configurer le calcul](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configurez le rôle de calcul sur votre appareil. Cela créera également un cluster Kubernetes. |
|**[9A. Transférer des données avec des partages Edge](azure-stack-edge-j-series-deploy-add-shares.md)** |Ajoutez des partages et connectez-vous à ces derniers via SMB ou NFS. |
|**[9B. Transférer des données avec des comptes de stockage Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Ajoutez des comptes de stockage et connectez-vous au stockage d’objets Blob via les API REST. |


Vous pouvez maintenant commencer à collecter des informations sur la configuration logicielle de votre appareil Azure Stack Edge.

## <a name="deployment-configuration-checklist"></a>Liste de contrôle de la configuration du déploiement

Avant de déployer votre appareil, vous devez collecter des informations pour configurer le logiciel sur votre appareil Azure Stack Edge. L’étude préalable d’une partie de ces informations simplifie le déploiement de l’appareil Azure Stack Edge dans votre environnement. Utilisez la [liste de contrôle de configuration de déploiement Azure Stack Edge](azure-stack-edge-gpu-deploy-checklist.md) pour noter les détails de configuration lors du déploiement de votre appareil.


## <a name="prerequisites"></a>Prérequis

Voici les prérequis relatifs à la configuration de votre ressource Azure Stack Edge, de votre appareil Azure Stack Edge et du réseau du centre de données.

### <a name="for-the-azure-stack-edge-resource"></a>Pour la ressource Azure Stack Edge

Avant de commencer, assurez-vous que :

- Votre abonnement Microsoft Azure est activé pour une ressource Azure Stack Edge. Vérifiez que vous avez utilisé un abonnement pris en charge tel que [Contrat Entreprise (EA) Microsoft](https://azure.microsoft.com/overview/sales-number/), [Fournisseur de solutions cloud (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Les abonnements de type Paiement à l’utilisation ne sont pas pris en charge.
- Vous disposez d’un accès Propriétaire ou Contributeur au niveau du groupe de ressources pour les ressources Azure Stack Edge/Data Box Gateway, IoT Hub et Stockage Azure.

    - Pour créer une ressource Azure Stack Edge / Data Box Gateway, vous devez disposer d’autorisations Contributeur (ou supérieures) au niveau du groupe de ressources. Vous devez également être sûr que le fournisseur `Microsoft.DataBoxEdge` est inscrit. Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Pour créer une ressource IoT Hub, vérifiez que le fournisseur Microsoft.Devices est bien inscrit. Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Pour créer une ressource de compte de stockage, là encore, vous avez besoin d’autorisations Contributeur (ou supérieures) au niveau du groupe de ressources. Par défaut, le stockage Azure est un fournisseur de ressources inscrit.
- Vous avez un accès Administrateur ou Utilisateur à l’API Graph Azure Active Directory. Pour plus d’informations, consultez [API Graph Azure Active Directory](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.

### <a name="for-the-azure-stack-edge-device"></a>Pour votre appareil Azure Stack Edge

Avant de déployer un appareil physique, assurez-vous que :

- Vous avez consulté les informations relatives à la sécurité qui étaient incluses dans le colis d’expédition.
- Dans votre centre de données, un emplacement 1U est disponible dans un rack standard 19 pouces pour le montage en rack de l’appareil.
- Vous avez accès à une surface de travail plane, stable et de niveau sur laquelle poser l’appareil en sécurité.
- Le site où vous envisagez de configurer l’appareil dispose d’une alimentation secteur standard provenant d’une source indépendante ou d’une unité de distribution de l’alimentation (PDU) en rack avec un onduleur (UPS).
- Vous avez accès à un appareil physique.


### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

- Le réseau de votre centre de données est configuré conformément à la configuration réseau nécessaire pour votre appareil Azure Stack Edge. Pour plus d’informations, consultez la [Configuration système nécessaire pour Azure Stack Edge](azure-stack-edge-system-requirements.md).

- Pour utiliser votre appareil Azure Stack Edge dans des conditions normales, vous avez :

    - Une bande passante de chargement de minimum 10 Mbits/s pour s’assurer que l’appareil reste à jour.
    - Une bande passante de chargement et de téléchargement de minimum 20 Mbits/s dédiée pour transférer des fichiers.

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

Si vous disposez d’une ressource Azure Stack Edge existante pour gérer votre appareil physique, ignorez cette étape et passez à la section [Obtenir la clé d’activation](#get-the-activation-key).

Pour créer une ressource Azure Stack Edge, suivez ces étapes dans le portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter au portail Azure en suivant cette URL : [https://portal.azure.com](https://portal.azure.com).

2. Dans le volet de gauche, sélectionnez **+ Créer une ressource**. Recherchez et sélectionnez **Azure Stack Edge / Data Box Gateway**. Sélectionnez **Create** (Créer). Si vous rencontrez des problèmes, accédez à [Résoudre les problèmes de commande](azure-stack-edge-troubleshoot-ordering.md).

3. Sélectionnez l’abonnement que vous souhaitez utiliser pour l’appareil Azure Stack Edge. Sélectionnez le pays où vous souhaitez expédier cet appareil physique. Sélectionnez **Afficher les appareils**.

    ![Créer une ressource 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Sélectionnez le type d'appareil. Sous **Azure Stack Edge Commercial**, choisissez **Azure Stack Edge avec GPU**, puis sélectionnez **S’inscrire**. 

    ![Créer une ressource 2](media/azure-stack-edge-gpu-deploy-prep/create-resource-2.png)

5. Un formulaire abrégé s’affiche. Remplissez le formulaire et sélectionnez **Envoyer**. Microsoft activera votre abonnement.

    ![Créer une ressource 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Une fois l’abonnement activé, vous devriez être en mesure de procéder à la création de la ressource. Dans le panneau **Sélectionner le type d'appareil**, choisissez **Sélectionner**.

    ![Créer une ressource 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)


6. Sous l’onglet **Bases**, entrez ou sélectionnez les **détails du projet** suivants.
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement    |Ce champ est automatiquement renseigné en fonction de la sélection antérieure. L’abonnement est lié à votre compte de facturation. |
    |Resource group  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Entrez ou sélectionnez les **détails de l’instance** suivants.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom doit être compris entre 2 et 50 caractères, et se composer uniquement de lettres, de chiffres et de traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
    |Région     |Pour obtenir la liste complète des régions où la ressource Azure Stack Edge est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si vous utilisez Azure Government, toutes les régions administratives sont disponibles, comme indiqué dans [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|

    ![Créer une ressource 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


5. Sélectionnez **Suivant : Adresse de livraison**.

    - Si vous disposez déjà d’un appareil, cochez la case **J’ai un appareil Azure Stack Edge**.

        ![Créer une ressource 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Si vous commandez un nouvel appareil, entrez le nom du contact, la société, l’adresse de livraison de l’appareil et les informations de contact.

        ![Créer une ressource 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

6. Sélectionnez **Suivant : Vérifier + créer**.

7. Sous l’onglet **Vérifier + créer**, passez en revue les **Détails de la tarification**, les **Conditions d’utilisation** et les détails de votre ressource. Cochez la case **J’ai pris connaissance des conditions de confidentialité**.

    ![Créer une ressource 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

8. Sélectionnez **Create** (Créer).

La création de la ressource prend quelques minutes. Un message vous informe que la ressource a été créée et déployée. Sélectionnez **Accéder à la ressource**.

![Accéder à la ressource Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Une fois la commande passée, Microsoft l’examine et vous communique (par e-mail) les détails de l’expédition.

![Notification relative à l’examen de la commande Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

Si vous rencontrez des problèmes pendant le processus de commande, consultez [Résoudre les problèmes de commande](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Obtenir la clé d'activation

Une fois que la ressource Azure Stack Edge est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Azure Stack Edge et à le connecter à la ressource. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Sélectionnez la ressource que vous avez créée. Sélectionnez **Vue d’ensemble**, puis **Configuration de l’appareil**.

    ![Sélectionner Configuration de l’appareil](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Sur la vignette **Activer**, sélectionnez **Générer une clé** pour créer une clé d’activation. Sélectionnez l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.

    ![Obtenir une clé d’activation](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - La clé d’activation expire 3 jours après sa création.
> - Si la clé expire, générez une nouvelle clé. L’ancienne clé n'est plus valide.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez approfondi vos connaissances sur Azure Stack Edge et avez notamment appris à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

Passez au tutoriel suivant pour apprendre à installer Azure Stack Edge.

> [!div class="nextstepaction"]
> [Installer Azure Stack Edge](./azure-stack-edge-gpu-deploy-install.md)



