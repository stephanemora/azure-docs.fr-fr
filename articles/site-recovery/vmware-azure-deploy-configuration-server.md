---
title: Déployer le serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment déployer un serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/22/2018
ms.author: ramamill
ms.openlocfilehash: 712f8fb2cb951460ad2be36b2899f52d4966fc82
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468401"
---
# <a name="deploy-a-configuration-server"></a>Déployer un serveur de configuration

Vous devez déployer un serveur de configuration local quand vous utilisez [Azure Site Recovery](site-recovery-overview.md) pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure. Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure. Il gère également la réplication des données. Cet article vous guide tout au long des étapes nécessaires pour déployer le serveur de configuration lorsque vous répliquez des machines virtuelles WMware sur Azure. [Suivez cet article](physical-azure-set-up-source.md) si vous avez besoin de configurer un serveur de configuration pour la réplication du serveur physique.

>[!TIP]
Pour en savoir plus sur le rôle du serveur de configuration dans l’architecture d’Azure Site Recovery, voir [ici](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Déploiement du serveur de configuration avec le modèle OVA

Le serveur de configuration doit être configuré comme une machine virtuelle VMware hautement disponible avec certaines exigences matérielles et dimensionnelles minimales. Pour un déploiement simple et pratique, Site Recovery fournit un modèle OVA (Open Virtualization Application) téléchargeable pour configurer le serveur de configuration conformément à toutes les exigences listées ci-dessous.

## <a name="prerequisites"></a>Prérequis

Les conditions matérielles minimales pour un serveur de configuration sont synthétisées dans le tableau suivant.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>planification de la capacité

Les conditions minimales de taille pour le serveur de configuration varient selon la fréquence potentielle de modification des données. Utilisez ce tableau comme guide.

| **UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées** |
| --- | --- | --- | --- | --- |
| 8 processeurs virtuels (2 sockets * 4 cœurs \@ 2,5 GHz) |16 Go |300 Go |500 Go ou moins |Répliquez moins de 100 machines. |
| 12 processeurs virtuels (2 sockets * 6 cœurs \@ 2,5 GHz) |18 Go |600 Go |500 Go à 1 To |Répliquez entre 100 et 150 machines. |
| 16 processeurs virtuels (2 sockets * 8 cœurs \@ 2,5 GHz) |32 Go |1 To |1 To à 2 To |Répliquez entre 150 et 200 machines. |

Si vous répliquez plusieurs machines virtuelles VMware, lisez les [considérations relatives à la planification de la capacité](site-recovery-plan-capacity-vmware.md). Exécutez [l’outil du planificateur de déploiement](site-recovery-deployment-planner.md) pour la réplication de VMware.

## <a name="download-the-template"></a>Téléchargez le modèle

1. Dans le coffre, allez dans **Préparer l’infrastructure** > **Source**.
2. Dans **Préparer la source**, sélectionnez **+Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration pour VMware** s’affiche dans **Type de serveur**.
4. Téléchargez le modèle OVA (Open Virtualization Application) pour le serveur de configuration.

  > [!TIP]
>Vous pouvez également télécharger directement la dernière version du modèle de serveur de configuration dans le [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver).

>[!NOTE]
La licence fournie avec le modèle OVA est une licence d’évaluation valide pendant 180 jours. Passée cette période, le client devra activer les fenêtres avec une licence payante.

## <a name="import-the-template-in-vmware"></a>Importer le modèle dans VMware

1. Connectez-vous au serveur VMware vCenter ou à l’hôte vSphere ESXi, à l’aide du client VMWare vSphere.
2. Dans le menu **Fichier**, sélectionnez **Déployer le modèle OVF** pour démarrer l’Assistant Déploiement du modèle OVF.

     ![Modèle OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Dans **Sélectionner une source**, saisissez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, sélectionnez **Suivant**.
5. Dans **Sélectionner le nom et le dossier** et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage**, et pour des performances optimales, sélectionnez **Remise à zéro rapide d’un approvisionnement important** dans **Sélectionner le format de disque virtuel**. L'utilisation de l'option d'allocation dynamique peut avoir un impact sur les performances du serveur de configuration.
7. Dans le reste des pages de l’assistant, acceptez les paramètres par défaut.
8. Dans **Prêt à finaliser** :

    * Pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.

    * Pour ajouter une interface réseau supplémentaire, désactivez **Mettre sous tension après le déploiement**, puis sélectionnez **Terminer**. Par défaut, le modèle de serveur de configuration est déployé avec une seule carte d’interface réseau, mais vous pouvez en ajouter d’autres après le déploiement.

> [!IMPORTANT]
> Veillez à ne pas modifier les configurations de ressources (restriction mémoire/cœurs/UC), modifier/supprimer des services ou fichiers installés sur le serveur de configuration après le déploiement. Cela aurait un impact sur l'inscription du serveur de configuration auprès des services Azure, ainsi que sur les performances de ce serveur.

## <a name="add-an-additional-adapter"></a>Ajouter une carte supplémentaire

Si vous souhaitez ajouter une carte d’interface réseau supplémentaire au serveur de configuration, faites-le avant d’inscrire le serveur dans le coffre. L’ajout de cartes supplémentaires n’est pas pris en charge après l’inscription.

1. Dans l’inventaire du client vSphere, faites un clic droit sur la machine virtuelle, puis sélectionnez **Modifier les paramètres**.
2. Dans **Matériel**, sélectionnez **Ajouter** > **Adaptateur Ethernet**. Sélectionnez ensuite **Suivant**.
3. Sélectionnez un type d’adaptateur et un réseau.
4. Pour connecter la carte réseau virtuelle lorsque la machine virtuelle s’allume, sélectionnez **Connecter à la mise sous tension**. Ensuite, sélectionnez **Suivant** > **Terminer** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Inscrire le serveur de configuration auprès des services Azure Site Recovery

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et entrez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur.
4. Lors de la première connexion, l’outil de configuration d’Azure Site Recovery démarre en quelques secondes.
5. Saisissez un nom utilisé pour inscrire le serveur de configuration sur Site Recovery. Sélectionnez ensuite **Suivant**.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, sélectionnez **Connecter** pour vous connecter à votre abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.
7. L’outil effectue des tâches de configuration, puis redémarre.
8. Reconnectez-vous à la machine. L’Assistant Gestion du serveur de configuration démarre **automatiquement** au bout de quelques secondes.

### <a name="configure-settings"></a>Configurer les paramètres

1. Dans l’Assistant Gestion de serveur de configuration, sélectionnez **Configurer la connectivité**, puis la carte d’interface réseau utilisé par le serveur de processus pour recevoir le trafic de réplication des machines virtuelles. Ensuite, sélectionnez **Enregistrer**. Vous ne pourrez pas modifier ce paramètre une fois qu’il aura été configuré. Il est fortement conseillé de ne pas modifier l’adresse IP d’un serveur de configuration. Vérifiez que l’adresse IP affectée au serveur de configuration est une adresse IP statique et pas une adresse IP DHCP.
2. Dans **Sélectionner le coffre Recovery Services**, connectez-vous à Microsoft Azure et sélectionnez votre abonnement Azure ainsi que le groupe de ressources et le coffre souhaités.

    > [!NOTE]
    > Une fois inscrit, le coffre Recovery Services n’est plus modifiable.

3. Dans **Installer des logiciels tiers** :

    |Scénario   |Procédure à suivre  |
    |---------|---------|
    |Peut-on télécharger et installer manuellement MySQL ?     |  Oui. Téléchargez l’application MySQL et placez-la dans le dossier **C:\Temp\ASRSetup**, puis installez-la manuellement. Lorsque vous accepterez les conditions et cliquerez sur **Télécharger et installer**, le portail indiquera *Déjà installé*. Vous pourrez passer à l’étape suivante.       |
    |Est-il possible d’éviter de télécharger MySQL en ligne ?     |   Oui. Placez votre application d’installation MySQL dans le dossier **C:\Temp\ASRSetup**. Acceptez les conditions et cliquez sur **Télécharger et installer** ; le portail utilisera le programme d’installation que vous avez ajouté et installera l’application. Vous pourrez passer à l’étape suivante après l’installation.    |
    |Comment télécharger et installer MySQL avec Azure Site Recovery ?     |  Acceptez le contrat de licence et cliquez sur **Télécharger et installer**. Vous pourrez passer à l’étape suivante après l’installation.       |
4. Dans **Valider la configuration de l’appliance**, les conditions préalables sont vérifiées avant que vous ne poursuiviez.
5. Dans **Configurer le serveur vCenter Server/vSphere ESXi**, entrez le nom de domaine complet ou l’adresse IP du serveur vCenter ou de l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Entrez le numéro de port sur lequel le serveur écoute. Entrez un nom convivial à utiliser pour le serveur VMware dans le coffre.
6. Entrez les informations d’identification à utiliser par le serveur de configuration pour se connecter au serveur VMware. Site Recovery utilise ces informations d’identification pour détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. Sélectionnez **Ajouter**, puis **Continuer**. Les informations d’identification entrées ici sont enregistrées localement.
7. Dans **Configurer les informations d’identification des machines virtuelles**, entrez le nom d’utilisateur et le mot de passe des machines virtuelles pour installer automatiquement le service Mobilité au cours de la réplication. Dans le cas de machines **Windows**, le compte doit disposer de privilèges d’administrateur local sur les machines à répliquer. Sous **Linux**, fournissez les informations du compte racine.
8. Sélectionnez **Finaliser la configuration** pour terminer l’inscription.
9. Une fois l’inscription terminée, ouvrez le Portail Azure, vérifiez que le serveur de configuration et le serveur VMware sont listés dans **Coffre Recovery Services** > **Gérer** > **Infrastructure Site Recovery** > **Serveurs de configuration**.

## <a name="upgrade-the-configuration-server"></a>Mettre à niveau le serveur de configuration

Pour mettre à niveau le serveur de configuration vers la version la plus récente, suivez ces [étapes](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Pour obtenir des instructions détaillées sur la mise à niveau de tous les composants Site Recovery, cliquez [ici](https://docs.microsoft.com/en-us/azure/site-recovery/service%20updates-how-to).

## <a name="manage-the-configuration-server"></a>Gérer le serveur de configuration

Pour éviter toute interruption d’une réplication continue, assurez-vous que l’adresse IP du serveur de configuration ne change pas une fois celui-ci inscrit dans un coffre. Pour plus d’informations sur les tâches courantes d’administration du serveur de configuration, voir [ici](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>Forum Aux Questions

1. Puis-je utiliser la machine virtuelle sur laquelle le serveur de configuration est installé à des fins différentes ?

    **Non**. Nous vous recommandons d’utiliser la machine virtuelle pour le seul usage de serveur de configuration. Veillez à suivre toutes les spécifications mentionnées dans [Conditions préalables](#prerequisites) pour une gestion efficace de la récupération d’urgence.
2. Peut-on remplacer le coffre déjà inscrit dans le serveur de configuration par un nouveau ?

    **Non**. Une fois le coffre inscrit auprès du serveur de configuration, il n’est plus modifiable.
3. Peut-on utiliser le même serveur de configuration pour protéger à la fois des machines physiques et des machines virtuelles ?

    **Oui**. Le même serveur de configuration peut être utilisé pour répliquer des machines physiques et virtuelles. Toutefois, une machine physique ne peut être restaurée automatiquement que vers une machine virtuelle VMware.
4. Quel est le rôle d’un serveur de configuration et où est-il utilisé ?

    Pour plus d’informations sur le serveur de configuration et ses fonctionnalités, voir [Architecture de la réplication VMware vers Azure](vmware-azure-architecture.md).
5. Où peut-on trouver la dernière version du serveur de configuration ?

    Pour savoir comment mettre à niveau le serveur de configuration via le portail, voir [Mettre à niveau le serveur de configuration](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Pour obtenir des instructions détaillées sur la mise à niveau de tous les composants Site Recovery, voir [ici](https://aka.ms/asr_how_to_upgrade).
6. Où puis-je télécharger la phrase secrète du serveur de configuration ?

    Reportez-vous à [cet article](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) pour télécharger la phrase secrète.
7. Puis-je modifier la phrase secrète ?

    **Non**, il vous est **fortement déconseillé de modifier la phrase secrète** d'un serveur de configuration. Modifier la phrase secrète arrête la réplication des machines protégées et entraîne un état d’intégrité critique.
8. Où puis-je télécharger les clés d’inscription du coffre ?

    Dans le **coffre Recovery Services**, **Gérer** > **Infrastructure Site Recovery** > **Serveurs de configuration**. Dans Serveurs, sélectionnez **Télécharger une clé d’inscription** pour télécharger le fichier d’informations d’identification du coffre.
9. Puis-je cloner un serveur de configuration existant et l’utiliser pour l’orchestration de la réplication ?

    **Non**, l’utilisation d’un composant de serveur de configuration cloné n’est pas prise en charge.

10. Puis-je changer l’adresse IP du serveur de configuration ?

    **Non**, il est fortement recommandé de ne pas changer l’adresse IP d’un serveur de configuration. Vérifiez que toutes les adresses IP affectées au serveur de configuration sont des adresses IP statiques et pas des adresses IP DHCP.
11. Puis-je configurer le serveur de configuration sur Azure ?

    Il est conseillé de configurer le serveur de configuration sur l’environnement local moyennant une ligne de vue directe avec v-Center, et de réduire les latences de transfert de données. Vous pouvez procéder à des sauvegardes planifiées du serveur de configuration [à des fins de restauration automatique](vmware-azure-manage-configuration-server.md#failback-requirements).

Pour plus de questions-réponses sur le serveur de configuration, reportez-vous à notre [documentation relative aux questions courantes sur le serveur configuration](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Résoudre les problèmes de déploiement

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Étapes suivantes

Configurez la récupération d’urgence des [machines virtuelles VMware](vmware-azure-tutorial.md) vers Azure.