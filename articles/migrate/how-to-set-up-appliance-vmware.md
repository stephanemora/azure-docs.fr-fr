---
title: Configurer une appliance pour Azure Migrate Server Assessment/Migration des machines virtuelles VMware | Microsoft Docs
description: Décrit comment configurer une appliance pour la découverte, l’évaluation et la migration sans agent des machines virtuelles VMware à l’aide de Azure Migrate Server Assessment/Migration.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/10/2019
ms.author: raynew
ms.openlocfilehash: 77bf9a0f73519aa979da49614475daf70f582a9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467127"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurer une appliance pour les machines virtuelles VMware

Cet article explique comment configurer l’appliance Azure Migrate si vous évaluez des machines virtuelles VMware avec l’outil Azure Migrate Server Assessment ou si vous migrez des machines virtuelles VMware vers Azure avec une migration sans agent à l’aide de l’outil de migration de serveur Azure Migrate.

L’appliance de machines virtuelles VMware est une appliance légère utilisée par Azure Migrate Server Assessment/Migration pour effectuer les opérations suivantes :

- Détecter les machines virtuelles VMware locales.
- Envoyer des métadonnées et des données de performances pour les machines virtuelles découvertes à Azure Migrate Server Assessment/Migration.

[En savoir plus](migrate-appliance.md) sur les appliances d’Azure Migrate.


## <a name="appliance-deployment-steps"></a>Étapes de déploiement d’appliance

Pour configurer l’appliance, vous devez :
- Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server.
- Créez l’appliance et vérifiez qu’elle peut se connecter à Azure Migrate Server Assessment.
- Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.

## <a name="download-the-ova-template"></a>Télécharger le modèle OVA

1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Assessment**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Oui, avec l’hyperviseur vSphere VMware**.
3. Cliquez sur **Télécharger** pour télécharger le fichier de modèle .OVA.



### <a name="verify-security"></a>Vérifier la sécurité

Vérifiez que le fichier .OVA est sécurisé avant de le déployer.

1. Sur l’ordinateur où vous avez téléchargé le fichier, ouvrez une fenêtre de commande d’administrateur.
2. Exécutez la commande suivante pour générer le code de hachage du fichier OVA :
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemple d’utilisation : ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Pour l’appliance version 1.0.0.5, le hachage généré doit correspondre à ces valeurs.

  **Algorithme** | **Valeur de hachage**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Créer la machine virtuelle de l’appliance

Importez le fichier téléchargé, puis créez une machine virtuelle.

1. Dans la console du client vSphere, cliquez sur **File** (Fichier) > **Deploy OVF Template** (Déployer le modèle OVF).
2. Dans l’Assistant de déploiement du modèle OVF > **Source**, spécifiez l’emplacement du fichier .OVA.
3. Dans **Name** (Nom) et **Location** (Emplacement), spécifiez un nom convivial pour la machine virtuelle. Sélectionnez l’objet d’inventaire dans lequel la machine virtuelle doit être hébergée.
5. Dans **Host/Cluster** (Hôte/Cluster), spécifiez l’hôte ou le cluster sur lequel s’exécute la machine virtuelle.
6. Dans **Storage** (Stockage), spécifiez la destination du stockage de la machine virtuelle.
7. Dans **Disk Format** (Format de disque), spécifiez le type de disque et la taille.
8. Dans **Network Mapping** (Mappage réseau), spécifiez le réseau auquel se connectera la machine virtuelle. Le réseau a besoin d’une connexion à Internet pour envoyer des métadonnées à Azure Migrate Server Assessment.
9. Passez en revue les paramètres, confirmez-les, puis cliquez sur **Finish** (Terminer).


### <a name="verify-appliance-access-to-azure"></a>Vérifier l’accès de l’appliance à Azure

Vérifiez que la machine virtuelle de l’appliance peut se connecter aux [URL Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configurer l’appliance

Configurez l’appliance pour la première fois.

1. Dans la console du client vSphere, cliquez avec le bouton droit sur la machine virtuelle, puis choisissez **Open Console** (Ouvrir la console).
2. Spécifiez la langue, le fuseau horaire et le mot de passe pour l’appliance.
3. Ouvrez un navigateur sur une machine qui peut se connecter à la machine virtuelle, puis ouvrez l’URL de l’application web de l’appliance : **https://*nom ou adresse IP de l’appliance* : 44368**.

   Vous pouvez aussi ouvrir l’application à partir du Bureau de l’appliance en cliquant sur le raccourci de l’application.
4. Dans l’application web > **Configurer les prérequis**, procédez comme suit :
    - **Licence** : Acceptez les termes de licence et lisez les informations relatives aux tiers.
    - **Connectivité** : L’application vérifie que la machine virtuelle a accès à Internet. Si la machine virtuelle utilise un proxy :
        - Cliquez sur **Paramètres du proxy** et spécifiez l’adresse du proxy et le port d’écoute, sous la forme http://ProxyIPAddress ou http://ProxyFQDN.
        - Spécifiez les informations d’identification si le proxy nécessite une authentification.
        - Seuls les proxys HTTP sont pris en charge.
    - **Synchronisation de l’heure** : L’heure est vérifiée. L’heure de l’appliance doit être synchronisée avec l’heure Internet pour que la découverte fonctionne correctement.
    - **Installer les mises à jour** : Azure Migrate vérifie que les dernières mises à jour de l'appliance sont installées.
    - **Installer VDDK** : Azure Migrate vérifie que VDDK (VMWare vSphere Virtual Disk Development Kit) est installé.
        - Azure Migrate utilise VDDK pour répliquer les machines durant la migration vers Azure.
        - Téléchargez VDDK 6.7 à partir de VMware, puis extrayez le contenu du fichier zip téléchargé à l’emplacement spécifié sur l’appliance.

## <a name="register-the-appliance-with-azure-migrate"></a>Inscrire l’appliance auprès d’Azure Migrate

1. Cliquez sur **Se connecter**. S’il n’apparaît pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.
2. Sous le nouvel onglet, connectez-vous avec vos informations d’identification Azure.
    - Connectez-vous avec votre nom d’utilisateur et votre mot de passe.
    - La connexion avec un code PIN n’est pas prise en charge.
3. Une fois la connexion effectuée, revenez à l’application web.
2. Sélectionnez l’abonnement où le projet Azure Migrate a été créé. Sélectionnez ensuite le projet.
3. Spécifiez un nom pour l’appliance. Le nom doit être alphanumérique et comporter 14 caractères au maximum.
4. Cliquez sur **S'inscrire**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Démarrer la découverte continue en fournissant vCenter Server et des informations d’identification de machine virtuelle

L’appliance doit se connecter à vCenter Server pour détecter les données de configuration et de performances des machines virtuelles.

### <a name="specify-vcenter-server-details"></a>Spécifier les détails vCenter Server
1. Dans **Spécifier les détails vCenter Server**, spécifiez le nom (FQDN) ou l’adresse IP du serveur vCenter Server. Vous pouvez laisser le port par défaut, ou spécifier un port personnalisé sur lequel votre serveur vCenter Server est à l’écoute.
2. Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez les informations d’identification du compte en lecture seule que l’appliance utilisera pour découvrir les machines virtuelles sur le vCenter Server. Vérifiez que le compte dispose des [autorisations nécessaires pour la découverte](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Vous pouvez définir l’étendue de la découverte en limitant l’accès au compte vCenter. Pour en savoir plus sur la découverte délimitée, cliquez [ici](tutorial-assess-vmware.md#scoping-discovery).
3. Cliquez sur **Valider la connexion** pour vérifier que l’appliance peut se connecter à vCenter Server.

### <a name="specify-vm-credentials"></a>Spécifier les informations d’identification de machine virtuelle
Pour la découverte des applications, des rôles et des fonctionnalités et la visualisation des dépendances des machines virtuelles, vous pouvez fournir des informations d’identification de machine virtuelle qui ont accès aux machines virtuelles VMware. Vous pouvez ajouter une information d’identification pour les machines virtuelles Windows et une autre pour les machines virtuelles Linux. [En savoir plus](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sur les privilèges d’accès nécessaires.

> [!NOTE]
> Cette entrée est facultative et nécessaire pour activer la détection des applications et la visualisation des dépendances sans agent.

1. Dans **Découvrir les applications et les dépendances sur les machines virtuelles**, cliquez **Ajouter les informations d’identification**.
2. Sélectionnez le **Système d’exploitation**.
3. Fournissez un nom convivial pour les informations d’identification.
4. Dans **Nom d’utilisateur** et **Mot de passe**, spécifiez un compte disposant au moins d’un accès invité sur les machines virtuelles.
5. Cliquez sur **Add**.

Une fois que vous avez spécifié les informations d’identification de vCenter Server et de la machine virtuelle (facultatif), cliquez sur **Enregistrer et démarrer la découverte** pour démarrer la découverte de l’environnement local.

Environ 15 minutes sont nécessaires pour que les métadonnées des machines virtuelles découvertes apparaissent dans le portail. La découverte des applications, des rôles et des fonctionnalités installés prend un certain temps et la durée dépend du nombre de machines virtuelles découvertes. Pour 500 machines virtuelles, il faut environ 1 heure pour que l’inventaire des applications s’affiche dans le portail Azure Migrate.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les tutoriels pour l'[évaluation VMware](tutorial-assess-vmware.md) et la [migration sans agent](tutorial-migrate-vmware.md).
