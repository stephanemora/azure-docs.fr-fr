---
title: Tutoriel - Créer une topologie de réseau hybride hub-and-spoke dans Azure avec Terraform
description: Tutoriel illustrant la création d’une architecture de référence d’un réseau hybride entier dans Azure, à l’aide de Terraform
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472177"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Tutoriel : Créer une topologie de réseau hybride hub-and-spoke dans Azure avec Terraform

Cette série de tutoriels explique comment utiliser Terraform pour implémenter une [topologie de réseau hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) dans Azure. 

Une topologie hub-and-spoke constitue un moyen d’isoler les charges de travail tout en partageant des services communs. Ces services incluent l’identité et la sécurité. Le hub est un réseau virtuel (VNet) qui agit comme un point de connexion central pour un réseau local. Les rayons sont des réseaux virtuels qui s’associent au hub. Les services partagés sont déployés dans le hub tandis que les charges de travail individuelles le sont à l’intérieur des réseaux spoke.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Utilisation de HCL (HashiCorp Language) pour disposer les ressources de l’architecture de référence du réseau hybride hub-and-spoke
> * Utiliser Terraform pour créer des ressources d’appliance réseau hub
> * Utilisation de Terraform pour créer un réseau hub dans Azure qui agisse comme point commun à toutes les ressources
> * Utilisation de Terraform pour créer des charges de travail individuelles en tant que réseaux virtuels spoke dans Azure
> * Utilisation de Terraform pour établir des passerelles et des connexions entre le réseau local et les réseaux Azure
> * Utilisation de Terraform pour créer des peerings de réseaux virtuels avec des réseaux spoke

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : si vous n’avez pas encore d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Installation et configuration de Terraform** : afin de provisionner les machines virtuelles et d’autres infrastructures dans Azure, [installez et configurez Terraform](terraform-install-configure.md).

## <a name="hub-and-spoke-topology-architecture"></a>Architecture de topologie hub-and-spoke

Dans la topologie hub-and-spoke, le hub est un réseau virtuel. Le réseau virtuel agit comme un axe central de connectivité pour votre réseau local. Les rayons (spokes) sont des réseaux virtuels qui s’homologuent avec le hub et qui peuvent être utilisés pour isoler les charges de travail. Le trafic circule entre le centre de données local et le hub via une connexion de passerelle ExpressRoute ou VPN. L’illustration suivante montre les différents composants d’une topologie hub-and-spoke :

![Architecture de topologie hub-and-spoke dans Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Avantages de la topologie hub-and-spoke

Une topologie de réseau hub-and-spoke permet d’isoler les charges de travail tout en partageant des services communs. Ces services incluent l’identité et la sécurité. Le hub est un réseau virtuel qui agit comme un point de connexion central pour un réseau local. Les rayons sont des réseaux virtuels qui s’associent au hub. Les services partagés sont déployés dans le hub tandis que les charges de travail individuelles le sont à l’intérieur des réseaux spoke. Voici quelques avantages de la topologie de réseau hub-and-spoke :

- **Réduction des coûts** par la centralisation des services dans un emplacement unique pouvant être partagé par plusieurs charges de travail. Ces charges de travail comportent des appliances virtuelles réseau et des serveurs DNS.
- **Surmonter les limites des abonnements** en effectuant le peering des réseaux virtuels de différents abonnements avec le hub central.
- **Séparation des préoccupations** entre le service informatique central (SecOps, InfraOps) et les charges de travail (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Utilisations courantes de l’architecture hub-and-spoke

Voici quelques utilisations courantes d’une architecture hub-and-spoke :

- De nombreux clients présentent des charges de travail qui sont déployées dans différents environnements. Ces environnements comprennent le développement, le test et la production. Souvent, ces charges de travail doivent partager des services, tels que DNS, IDS, NTP ou AD DS. Ces services partagés peuvent être placés dans le réseau virtuel hub. En procédant ainsi, chaque environnement est déployé sur un membre spoke pour garantir l’isolation.
- Des charges de travail n’exigeant pas de connectivité entre elles, mais nécessitant un accès aux services partagés.
- Des entreprises ayant besoin d’un contrôle centralisé en matière de sécurité.
- Des entreprises nécessitant une gestion distincte des charges de travail dans chaque membre spoke.

## <a name="preview-the-demo-components"></a>Aperçu des composants de la démonstration

À mesure que vous avancez dans cette série de tutoriels, vous découvrez différents composants qui sont définis dans des scripts Terraform distincts. L’architecture de démonstration créée et déployée est constituée des composants suivants :

- **Réseau local**. Un réseau local privé s’exécutant au sein d’une organisation. Pour l’architecture de référence hub-and-spoke, un réseau virtuel dans Azure est utilisé pour simuler un réseau local.

- **Périphérique VPN**. Un appareil ou service VPN assurant la connectivité externe au réseau local. L’appareil VPN peut être un équipement matériel ou une solution logicielle. 

- **Réseau virtuel hub**. Le hub constituant le point central de la connectivité pour votre réseau local, et un emplacement où héberger des services. Ces services peuvent être consommés par les différentes charges de travail hébergées dans les réseaux virtuels spoke.

- **Sous-réseau de passerelle**. Les passerelles de réseau virtuel étant conservées dans le même sous-réseau.

- **Réseaux virtuels spokes**. Les membres spokes peuvent servir à isoler les charges de travail dans leurs propres réseaux virtuels, qui sont alors gérées séparément des autres membres spokes. Chaque charge de travail peut inclure plusieurs niveaux, avec plusieurs sous-réseaux connectés à l’aide d’équilibreurs de charge Azure. 

- **Peering de réseaux virtuels**. Deux réseaux virtuels pouvant être connectés au moyen d’une connexion de peering. Les connexions de peering sont des connexions non transitives et à faible latence entre des réseaux virtuels. Une fois appairés, les réseaux virtuels échangent du trafic à l’aide de la dorsale principale d’Azure, sans avoir besoin de routeur. Dans une topologie de réseau hub-and-spoke, le peering de réseaux virtuels est utilisé pour connecter le hub à chaque membre spoke. Vous pouvez appairer des réseaux virtuels dans la même région ou dans différentes régions.

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire

Créez le répertoire qui contient vos fichiers de configuration Terraform pour la démonstration.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Déplacez-vous dans le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Créez un répertoire nommé `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Déplacez-vous dans le nouveau répertoire :

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Déclarer le fournisseur Azure

Créez le fichier de configuration Terraform qui déclare le fournisseur Azure.

1. Dans Cloud Shell, ouvrez un nouveau fichier nommé `main.tf`.

    ```bash
    code main.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Enregistrez le fichier et quittez l’éditeur.

## <a name="create-the-variables-file"></a>Créer le fichier de variables

Créez le fichier de configuration Terraform pour les variables courantes qui sont utilisées dans différents scripts.

1. Dans Cloud Shell, ouvrez un nouveau fichier nommé `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Enregistrez le fichier et quittez l’éditeur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Créer un réseau virtuel local avec Terraform dans Azure](./terraform-hub-spoke-on-prem.md)