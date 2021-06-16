---
title: Guide de migration d’un appareil physique Azure Stack Edge Pro FPGA vers GPU
description: Ce guide contient des instructions pour migrer les charges de travail d’un appareil Azure Stack Edge Pro FPGA vers un appareil Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: alkohli
ms.openlocfilehash: f0e4ee41feea34f99e160605e2fad06c61817a6e
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111986333"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrer les charges de travail d’un appareil Azure Stack Edge Pro FPGA vers un appareil Azure Stack Edge Pro GPU

Cet article explique comment migrer les charges de travail et les données d’un appareil Azure Stack Edge Pro FPGA vers un appareil Azure Stack Edge Pro GPU. Le processus de migration commence par une comparaison des deux appareils, un plan de migration et une révision des considérations sur la migration. La procédure de migration fournit des étapes détaillées qui se terminent par une vérification et un nettoyage des appareils.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>À propos de la migration

La migration désigne le processus de déplacement des charges de travail et des données des applications d’un emplacement de stockage à un autre. Cela implique de réaliser une copie exacte des données actuelles d’une organisation d’un dispositif de stockage sur un autre (de préférence sans interrompre ni désactiver les applications actives), puis de rediriger toutes les activités d’entrée/sortie (E/S) vers le nouvel appareil. 

Ce guide de migration fournit une description pas à pas des étapes à effectuer pour migrer les données d’un appareil Azure Stack Edge Pro FPGA vers un appareil Azure Stack Edge Pro GPU. Ce document s’adresse aux professionnels des technologies de l’information et aux travailleurs du savoir ayant pour responsabilités l’exploitation, le déploiement et la gestion des appareils Azure Stack Edge dans le centre de données.

Dans cet article, l’appareil Azure Stack Edge Pro FPGA est désigné comme l’appareil *source* et l’appareil Azure Stack Edge Pro GPU comme l’appareil *cible*. 

## <a name="comparison-summary"></a>Résumé de la comparaison

Cette section récapitule et compare les fonctionnalités des appareils Azure Stack Edge Pro GPU et Azure Stack Edge Pro FPGA. Le matériel des appareils source et cible est en grande partie identique, seules la carte d’accélération matérielle et la capacité de stockage peuvent différer.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Fonctionnalité  | Azure Stack Edge Pro GPU (appareil cible)  | Azure Stack Edge Pro FPGA (appareil source)|
|----------------|-----------------------|------------------------|
| Matériel       | Accélération matérielle : 1 ou 2 GPU Nvidia T4 <br> Les spécifications relatives à la capacité de calcul, à la mémoire, à l’interface réseau, au bloc d’alimentation et au cordon d’alimentation sont identiques à celles de l’appareil avec FPGA.  | Accélération matérielle : FPGA Intel Arria 10 <br> Les spécifications relatives à la capacité de calcul, à la mémoire, à l’interface réseau, au bloc d’alimentation et au cordon d’alimentation sont identiques à celles de l’appareil avec GPU.          |
| Stockage utilisable | 4,19 To <br> Après réservation d’espace pour résilience de parité et usage interne | 12,5 To <br> Après réservation d’espace pour usage interne |
| Sécurité       | Certificats |                                                     |
| Charges de travail      | Charges de travail IoT Edge <br> Charges de travail de machine virtuelle <br> Charges de travail Kubernetes| Charges de travail IoT Edge |
| Tarifs        | [Tarification](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Tarification](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Plan de migration

Pour créer votre plan de migration, prenez en compte les informations suivantes :

- Développez une planification pour la migration. 
- Quand vous migrez des données, vous risquez de subir un temps d’arrêt. Nous vous recommandons de planifier la migration pendant une fenêtre de maintenance avec temps d’arrêt, car le processus entraîne des perturbations. C’est au cours de ce temps d’arrêt que vous configurerez et restaurerez des configurations, comme décrit plus loin dans ce document.
- Déterminez la durée totale du temps d’arrêt et communiquez-la à toutes les parties prenantes.
- Identifiez les données locales qui doivent être migrées à partir de l’appareil source. Par précaution, vérifiez que toutes les données sur le stockage existant ont une sauvegarde récente. 


## <a name="migration-considerations"></a>Considérations relatives à la migration 

Avant de poursuivre la migration, prenez en compte les informations suivantes : 

- Un appareil Azure Stack Edge Pro GPU ne peut pas être activé sur une ressource Azure Stack Edge Pro FPGA. Vous devez créer une ressource pour l’appareil Azure Stack Edge Pro GPU, comme décrit dans [Créer une commande d’appareil Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- Les modèles de Machine Learning déployés sur l’appareil source qui utilisent le FPGA doivent être modifiés pour l’appareil cible avec GPU. Pour obtenir de l’aide sur les modèles, vous pouvez contacter le support Microsoft. Les modèles personnalisés déployés sur l’appareil source qui n’utilisent pas le FPGA (ils utilisent uniquement le processeur) doivent fonctionner tels quels sur l’appareil cible (avec le processeur).
- Les modules IoT Edge déployés sur l’appareil source peuvent nécessiter des modifications pour qu’ils puissent être déployés sur l’appareil cible. 
- L’appareil source prend en charge les protocoles NFS 3.0 et 4.1. L’appareil cible prend uniquement en charge le protocole NFS 3.0.
- L’appareil source prend en charge les protocoles SMB et NFS. L’appareil cible prend en charge le stockage par le biais du protocole REST à l’aide de comptes de stockage, en plus des protocoles SMB et NFS pour les partages.
- L’accès au partage sur l’appareil source se fait par le biais du l’adresse IP, alors que l’accès au partage sur l’appareil cible se fait par le biais du nom de l’appareil.

## <a name="migration-steps-at-a-glance"></a>Aperçu des étapes de migration

Ce tableau récapitule le flux global de la migration et décrit les étapes nécessaires pour mener à bien la migration ainsi que l’emplacement où effectuer ces étapes.

| Dans cette phase | Effectuer cette étape| Sur cet appareil |
|---------------|-------------|----------------|
| Préparer l’appareil source*       | 1. Enregistrer les données de configuration <br>2. Sauvegarder les données de partage <br>3. Préparer les charges de travail IoT Edge| Appareil source  |
| Préparer l’appareil cible*       |1. Créer une commande <br>2. Configurer et activer| Périphérique cible  |
| Migration des données       | 1. Migrer les données des partages <br>2. Redéployer les charges de travail IoT Edge| Périphérique cible  |
| Vérifier les données            |Vérifier les données migrées |Périphérique cible  |
| Nettoyer, renvoyer              |Effacer les données et renvoyer| Appareil source  |

**Les appareils source et cible peuvent être préparés en parallèle.*

## <a name="prepare-source-device"></a>Préparer l’appareil source

La préparation comprend l’identification des partages cloud Edge, des partages locaux Edge et des modules IoT Edge déployés sur l’appareil. 

### <a name="1-record-configuration-data"></a>1. Enregistrer les données de configuration

Effectuez ces étapes sur votre appareil source par le biais de l’interface utilisateur locale.

Enregistrez les données de configuration sur l’appareil *source*. Utilisez la [liste de vérification du déploiement](azure-stack-edge-gpu-deploy-checklist.md) pour vous aider à enregistrer la configuration de l’appareil. Vous aurez besoin de ces informations sur la configuration pour configurer le nouvel appareil cible durant la migration. 

### <a name="2-back-up-share-data"></a>2. Sauvegarder les données de partage

Les données de l’appareil peuvent être de l’un des types suivants :

- Données dans des partages cloud Edge
- Données dans des partages locaux

#### <a name="data-in-edge-cloud-shares"></a>Données dans des partages cloud Edge

Le cloud Edge partage les données hiérarchisées de votre appareil vers Azure. Effectuez ces étapes sur votre appareil *source* par le biais du portail Azure. 

- Dressez la liste de tous les partages et utilisateurs du cloud Edge sur l’appareil source.
- Dressez la liste de toutes les planifications de bande passante dont vous disposez. Vous recréerez ces planifications de bande passante sur votre appareil cible.
- En fonction de la bande passante réseau disponible, configurez les planifications de bande passante sur votre appareil afin de maximiser les données hiérarchisées dans le cloud. Cela réduit les données locales sur l’appareil.
- Vérifiez que les partages sont entièrement hiérarchisés dans le cloud. Pour cela, examinez l’état du partage dans le portail Azure.  

#### <a name="data-in-edge-local-shares"></a>Données dans les partages locaux Edge

Les données dans les partages locaux Edge restent sur l’appareil. Effectuez ces étapes sur votre appareil *source* par le biais du portail Azure. 

- Faites la liste des partages locaux Edge présents sur l’appareil.
- Étant donné que vous effectuerez une migration ponctuelle des données, créez une copie des données du partage local Edge sur un autre serveur local. Vous pouvez utiliser des outils de copie tels que `robocopy` (SMB) ou `rsync` (NFS) pour copier les données. Vous avez peut-être déjà déployé une solution tierce de protection des données pour sauvegarder les données de vos partages locaux. Les solutions tierces suivantes sont prises en charge pour une utilisation avec des appareils Azure Stack Edge Pro FPGA :

    | Logiciels tiers           | Référence à la solution                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Pour plus d’informations, contactez Cohesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Pour plus d’informations, contactez Commvault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Pour plus d’informations, contactez Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Pour plus d’informations, contactez Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Préparer les charges de travail IoT Edge

- Si vous avez déployé des modules IoT Edge et que vous utilisez l’accélération FPGA, vous devrez peut-être modifier les modules avant que ceux-ci ne s’exécutent sur l’appareil GPU. Suivez les instructions pour [modifier les modules IoT Edge](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Préparer l’appareil cible

### <a name="1-create-new-order"></a>1. Créer une commande

Vous devez créer une commande (et une ressource) pour votre appareil *cible*. L’appareil cible doit être activé sur la ressource GPU, et non sur la ressource FPGA.

Pour passer une commande, [créez une nouvelle ressource Edge Azure Stack](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) dans le Portail Azure.


### <a name="2-set-up-activate"></a>2. Configurer, activer

Vous devez configurer et activer l’appareil *cible* sur la nouvelle ressource que vous avez créée précédemment. 

Effectuez ces étapes pour configurer l’appareil *cible* par le biais du portail Azure :

1. Rassemblez les informations requises dans la [liste de contrôle du déploiement](azure-stack-edge-gpu-deploy-checklist.md). Vous pouvez utiliser les informations précédemment enregistrées sur la configuration de l’appareil source. 
1. [Déballez l’appareil](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [montez-le en rack](azure-stack-edge-gpu-deploy-install.md#rack-the-device), puis [câblez-le](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Connectez-vous à l’interface utilisateur locale de l’appareil](azure-stack-edge-gpu-deploy-connect.md).
1. Configurez le réseau à l’aide d’un ensemble d’adresses IP (si vous utilisez des adresses IP statiques) différent de celui que vous utilisiez pour votre ancien appareil. [Consultez cet article pour savoir comment configurer les paramètres réseau](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Attribuez le nom de votre ancien appareil au nouveau et indiquez un domaine DNS. [Consultez cet article pour savoir comment configurer les paramètres de l’appareil](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configurez les certificats sur le nouvel appareil. [Consultez cet article pour savoir comment configurer des certificats](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Récupérez la clé d’activation à partir du Portail Azure, puis activez le nouvel appareil. [Consultez cet article pour savoir comment activer l’appareil](azure-stack-edge-gpu-deploy-activate.md).

Vous êtes maintenant prêt à restaurer les données de partage et à déployer les charges de travail que vous exécutiez sur l’ancien appareil.

## <a name="migrate-data"></a>Migration des données

Vous allez maintenant copier les données de l’appareil source dans les partages cloud Edge et les partages locaux Edge sur votre appareil *cible*.

### <a name="1-from-edge-cloud-shares"></a>1. À partir des partages cloud Edge

Pour synchroniser les données sur les partages cloud Edge sur votre appareil cible, effectuez les étapes suivantes :

1. [Ajoutez des partages](./azure-stack-edge-gpu-manage-shares.md#add-a-share) correspondant aux noms de partage créés sur l’appareil source. Quand vous créez les partages, assurez-vous que le paramètre **Sélectionner un conteneur d’objets blob** est défini sur **Utiliser l’existant**, puis sélectionnez le conteneur utilisé avec l’appareil précédent.
1. [Ajoutez des utilisateurs](./azure-stack-edge-gpu-manage-users.md#add-a-user) qui avaient accès à l’appareil précédent.
1. [Actualisez le partage](./azure-stack-edge-gpu-manage-shares.md#refresh-shares) de données à partir d’Azure. L’actualisation du partage extrait toutes les données cloud du conteneur existant vers les partages.
1. Recréez les planifications de bande passante à associer à vos partages. Pour obtenir des instructions détaillées, consultez [Ajouter une planification de bande passante](./azure-stack-edge-gpu-manage-bandwidth-schedules.md#add-a-schedule) .


### <a name="2-from-edge-local-shares"></a>2. À partir des partages locaux Edge

Vous avez peut-être déployé une solution de sauvegarde tierce pour protéger les données des partages locaux pour vos charges de travail IoT. Vous devez maintenant restaurer ces données.

Une fois que l’appareil de remplacement est entièrement configuré, activez l’appareil pour le stockage local. 

Pour récupérer les données à partir de partages locaux, procédez comme suit :

1. [Configurez le calcul sur l’appareil](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Ajoutez tous les partages locaux sur l’appareil cible. Consultez les étapes détaillées dans [Ajouter un partage local](azure-stack-edge-gpu-manage-shares.md#add-a-local-share).
1. L’accès aux partages SMB sur l’appareil source utilise les adresses IP, tandis que vous utilisez le nom de l’appareil sur l’appareil cible. Consultez [Se connecter à un partage SMB sur Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-smb-share). Pour vous connecter à des partages NFS sur l’appareil cible, vous devez utiliser les nouvelles adresses IP associées à l’appareil. Consultez [Se connecter à un partage NFS sur Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-nfs-share). 

    Si vous avez copié les données des partages sur un serveur intermédiaire avec SMB ou NFS, vous pouvez copier les données depuis ce serveur vers les partages de l’appareil cible. Si les appareils source et cible sont *en ligne*, vous pouvez également copier les données directement de l’appareil source.

    Si vous avez utilisé un logiciel tiers pour sauvegarder les données dans les partages locaux, vous devez exécuter la procédure de récupération qui est fournie par la solution de protection des données de votre choix. Consultez les références dans le tableau suivant.

    | Logiciels tiers           | Référence à la solution                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Pour plus d’informations, contactez Cohesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Pour plus d’informations, contactez Commvault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Pour plus d’informations, contactez Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Pour plus d’informations, contactez Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. Redéployer les charges de travail IoT Edge

Une fois les modules IoT Edge préparés, vous devez déployer les charges de travail IoT Edge sur votre appareil cible. Si vous rencontrez des erreurs durant le déploiement de modules IoT Edge, consultez :

- [Problèmes courants et résolutions pour Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md). 
- [Erreurs d’exécution IoT Edge](azure-stack-edge-gpu-troubleshoot-iot-edge.md).

## <a name="verify-data"></a>Vérifier les données

Après la migration, vérifiez que toutes les données ont été migrées et que les charges de travail ont été déployées sur l’appareil cible.

## <a name="erase-data-return"></a>Effacer les données, renvoyer

Une fois la migration des données terminée, effacez les données locales et renvoyez l’appareil source. Suivez les étapes décrites dans [Renvoyer votre appareil Azure Stack Edge Pro](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment déployer des charges de travail IoT Edge sur un appareil Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-compute-module-simple.md)