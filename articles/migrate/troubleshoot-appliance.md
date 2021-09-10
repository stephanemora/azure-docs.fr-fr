---
title: Résoudre les problèmes de l’appliance Azure Migrate
description: Obtenez de l’aide pour résoudre les problèmes qui peuvent se produire avec l’appliance Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: c67ad02737fc8a40895f99cd3acf05ba98e7643e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305892"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Résoudre les problèmes de l’appliance Azure Migrate

Cet article vous aide à résoudre les problèmes rencontrés lorsque vous déployez l’appliance [Azure Migrate](migrate-services-overview.md) et utilisez l’appliance pour découvrir des serveurs locaux.

## <a name="whats-supported"></a>Prise en charge

[Passez en revue](migrate-appliance.md) la configuration requise pour la prise en charge de l’appliance.

## <a name="invalid-ovf-manifest-entry-error-occurs-during-appliance-setup"></a>Une erreur « entrée de manifeste OVF non valide » se produit lors de la configuration de l’appliance

Vous obtenez l’erreur « Le fichier manifeste fourni n’est pas valide : entrée de manifeste OVF non valide » lorsque vous configurez une appliance à l’aide du modèle OVA.

### <a name="remediation"></a>Correction

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. [Plus d’informations](./tutorial-discover-vmware.md) Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
1. Si le déploiement échoue une nouvelle fois et que vous utilisez le client VMware vSphere pour déployer le fichier OVF, essayez plutôt de le déployer à l’aide du client web vSphere. Si le déploiement échoue de nouveau, essayez un autre navigateur web.
1. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) avec le client web (https://<*adresse IP de l’hôte*>/ui).
   - Dans **Accueil** > **Inventaire**, sélectionnez **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
1. Si le déploiement échoue toujours, contactez le support Azure Migrate.

## <a name="connectivity-check-fails-during-the-prerequisites-setup"></a>La vérification de la connectivité échoue pendant la configuration des composants requis

Vous obtenez une erreur dans la vérification de la connectivité de l’appliance.

### <a name="remediation"></a>Correction

1. Vérifiez que vous pouvez vous connecter aux [URL](./migrate-appliance.md#url-access) nécessaires à partir de l’appliance.
1. Vérifiez si un proxy ou un pare-feu bloque l’accès à ces URL. Si vous devez créer une liste d’autorisation, veillez à inclure toutes les URL.
1. Si un serveur proxy est configuré localement, fournissez les détails du proxy correctement en sélectionnant **Configurer le proxy** lors de la même étape. Fournissez les informations d’identification d’autorisation si le proxy en a besoin.
1. Vérifiez que le serveur n’a pas été utilisé pour configurer l’[appliance de réplication](./migrate-replication-appliance.md) ou que l’agent du service Mobilité est installé sur le serveur.

## <a name="connectivity-check-fails-for-the-akams-url-during-the-prerequisites-setup"></a>La vérification de la connectivité échoue pour l’URL aka.ms pendant la configuration des composants requis

Vous obtenez une erreur dans la vérification de la connectivité de l’appliance pour l’URL aka.ms.

### <a name="remediation"></a>Correction

1. Vérifiez que vous disposez d’une connectivité à Internet et que vous avez ajouté l’URL -aka.ms/* à la liste d’autorisation pour télécharger les dernières versions des services.
1. Vérifiez si un proxy ou un pare-feu bloque l’accès à cette URL. Vérifiez que vous avez fourni les détails du proxy correctement à l’étape des prérequis du gestionnaire de configuration.
1. Revenez au gestionnaire de configuration de l’appliance et réexécutez les composants requis pour lancer la mise à jour automatique.
1. Si la nouvelle tentative ne donne rien, téléchargez le fichier *latestcomponents.json* à partir de ce [site web](https://aka.ms/latestapplianceservices) pour vérifier les dernières versions des services qui échouent. Mettez-les à jour manuellement à partir des liens de téléchargement dans le fichier.

 Si vous avez activé l’appliance pour la **connectivité au point de terminaison privé** et que vous ne souhaitez pas autoriser l’accès à cette URL via Internet, [désactivez la mise à jour automatique](./migrate-appliance.md#turn-off-auto-update), car le lien aka.ms est requis pour ce service.

 >[!Note]
 >Si vous désactivez le service de mise à jour automatique, les services s’exécutant sur l’appliance n’obtiendront pas automatiquement les dernières mises à jour. Pour contourner cette situation, [mettez à jour les services de l’appliance manuellement](./migrate-appliance.md#manually-update-an-older-version).

## <a name="auto-update-check-fails-during-the-prerequisites-setup"></a>La vérification des mises à jour automatiques échoue pendant la configuration des composants requis

Vous obtenez une erreur lors de la vérification des mises à jour automatiques sur l’appliance.

### <a name="remediation"></a>Correction

1. Assurez-vous d’avoir créé une liste verte pour les [URL requises](./migrate-appliance.md#url-access) et qu’aucun paramètre de proxy ni de pare-feu ne les bloque.
1. En cas d’échec de la mise à jour d’un composant d’appliance, vous pouvez soit réexécuter les prérequis, soit [mettre à jour manuellement les services de l’appliance](./migrate-appliance.md#manually-update-an-older-version).

## <a name="time-sync-check-fails-during-the-prerequisites-setup"></a>La vérification de la synchronisation de l’heure échoue pendant la configuration des composants requis

Une erreur de synchronisation de l’heure indique que l’horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l’heure actuelle.

### <a name="remediation"></a>Correction

- Assurez-vous que l’heure du serveur de l’appliance est synchronisée avec l’heure Internet en vérifiant les paramètres de date et d’heure dans le Panneau de configuration.
- Vous pouvez également modifier l’heure de l’horloge sur le serveur de l’appliance pour qu’elle corresponde à l’heure actuelle en procédant comme suit :
     1. Ouvrez une invite de commandes d’administration sur le serveur.
     1. Pour vérifier le fuseau horaire, exécutez **w32tm /tz**.
     1. Pour synchroniser l’heure, exécutez **w32tm /resync**.

## <a name="vddk-check-fails-during-the-prerequisites-setup-on-the-vmware-appliance"></a>Échec de la vérification de VDDK pendant la configuration des composants requis sur l’appliance VMware

La vérification du kit de développement de disque virtuel (VDDK) a échoué, car l’appliance n’a pas pu trouver le VDDK requis installé sur l’appareil. Ce problème peut entraîner des échecs de réplication en cours.

### <a name="remediation"></a>Correction

1. Assurez-vous d’avoir téléchargé le VDDK 6.7 et copié ses fichiers dans **C:\Program Files\VMware\VMware Virtual Disk Development Kit** sur le serveur de l’appliance.
1. Assurez-vous qu’aucun autre logiciel ou application n’utilise une autre version du VDDK sur l’appliance.

## <a name="project-key-related-error-occurs-during-appliance-registration"></a>Une erreur liée à la clé de projet se produit lors de l’inscription de l’appliance

 Vous rencontrez des problèmes lorsque vous essayez d’inscrire l’appliance à l’aide de la clé de projet Azure Migrate copiée à partir du projet.

### <a name="remediation"></a>Correction

1. Vérifiez que vous avez copié la clé appropriée à partir du projet. Sur la carte **Azure Migrate : découverte et évaluation** de votre projet, sélectionnez **Découvrir**. Sélectionnez ensuite **Gérer l’appliance existante** à l’étape 1. Sélectionnez le nom de l’appliance pour laquelle vous avez généré une clé précédemment dans le menu déroulant. Copiez la clé correspondante.
1. Veillez à coller la clé sur l’appliance ayant le **type de cloud** (Public/US Gov) et le **type d’appliance** (VMware/Hyper-V/physique ou autre) appropriés. Vérifiez le type de cloud et de scénario en haut du gestionnaire de configuration de l’appliance.

## <a name="failed-to-connect-to-the-azure-migrate-project-error-occurs-during-appliance-registration"></a>Une erreur « La connexion au projet Azure Migrate a échoué » se produit lors de l’inscription de l’appliance

Une fois la connexion établie avec un compte d’utilisateur Azure, l’étape d’inscription de l’appliance échoue avec le message « La connexion au projet Azure Migrate a échoué. Consultez les détails de l’erreur et suivez les étapes de correction en cliquant sur Réessayer. »

Ce problème se produit lorsque le compte d’utilisateur Azure qui a été utilisé pour se connecter à partir du gestionnaire de configuration de l’appliance est différent du compte d’utilisateur qui a été utilisé pour générer la clé de projet Azure Migrate dans le portail.

### <a name="remediation"></a>Correction

Deux options s'offrent à vous :

- Pour effectuer l’inscription de l’appliance, utilisez le compte d’utilisateur Azure qui a permis de générer la clé de projet Azure Migrate dans le portail.
- Vous pouvez également attribuer les rôles et les [autorisations](./tutorial-discover-vmware.md#prepare-an-azure-user-account) nécessaires à l’autre compte d’utilisateur Azure utilisé pour l’inscription de l’appliance.

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-error-occurs-during-appliance-registration"></a>Une erreur « L’opération Azure Active Directory (AAD) a échoué avec l’état interdit » se produit lors de l’inscription de l’appliance

Vous ne parvenez pas à effectuer l’inscription en raison de privilèges Azure Active Directory insuffisants et obtenez l’erreur « L’opération Azure Active Directory (AAD) a échoué avec l’état Interdit. ».

### <a name="remediation"></a>Correction

Veillez à disposer des [autorisations nécessaires](./tutorial-discover-vmware.md#prepare-an-azure-user-account) pour créer et gérer des applications Azure Active Directory dans Azure. Vous devez avoir le rôle **Développeur d’applications** *ou* le rôle d’utilisateur avec l’autorisation **L’utilisateur peut inscrire des applications** accordée au niveau du locataire.

## <a name="forbidden-to-access-key-vault-error-occurs-during-appliance-registration"></a>Une erreur « Accès interdit à Key Vault » se produit lors de l’inscription de l’appliance

L’opération de création ou de mise à jour d’Azure Key Vault pour « {KeyVaultName} » a échoué en raison de l’erreur « {KeyVaultErrorMessage} ».

Ce problème se produit généralement lorsque le compte d’utilisateur Azure utilisé pour inscrire l’appliance est différent du compte utilisé pour générer la clé de projet Azure Migrate dans le portail (c’est-à-dire lorsque le coffre de clés a été créé).

### <a name="remediation"></a>Correction

1. Assurez-vous que le compte d’utilisateur actuellement connecté sur l’appliance dispose des autorisations nécessaires sur le coffre de clés mentionné dans le message d’erreur. Le compte d’utilisateur a besoin des autorisations mentionnées sur [ce site web](./tutorial-discover-vmware.md#prepare-an-azure-user-account).
1. Accédez au coffre de clés et assurez-vous que votre compte d’utilisateur a une stratégie d’accès avec toutes les autorisations **Clé**, **Secret** et **Certificat** attribuées sous **Stratégie d’accès à Key Vault**. [Plus d’informations](../key-vault/general/assign-access-policy-portal.md)
1. Si vous avez activé l’appliance pour la **connectivité de point de terminaison privé**, assurez-vous que l’appliance est hébergée dans le même réseau virtuel que celui où le coffre de clés a été créé ou qu’elle est connectée au réseau virtuel Azure où le coffre de clés a été créé par une liaison privée. Assurez-vous que la liaison privée du coffre de clés peut être résolue à partir de l’appliance. Accédez à **Azure Migrate : découverte et évaluation** > **Propriétés** pour trouver les détails des points de terminaison privés pour des ressources telles que l’instance de coffre de clés créée lors de la création de la clé Azure Migrate. [Plus d’informations](./troubleshoot-network-connectivity.md)
1. Si vous disposez des autorisations et de la connectivité nécessaires, réessayez l’inscription sur l’appliance après un certain temps.

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>Impossible de se connecter à vCenter Server lors de la validation

Si cette erreur de connexion s’affiche, il est possible que vous ne puissiez pas vous connecter à vCenter Server *Nom_Serveur*.com:9443. Les détails de l’erreur indiquent qu’aucun point de terminaison qui écoute `https://\*servername*.com:9443/sdk` ne peut accepter le message.

### <a name="remediation"></a>Correction

- Vérifiez si vous exécutez la dernière version de l’appliance. Si ce n’est pas le cas, mettez-la à niveau vers la [dernière version](./migrate-appliance.md).
- Si le problème se produit encore avec la dernière version, il est possible que l’appliance ne parvienne pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué soit incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tente de se connecter au numéro de port 443.

    1. Effectuez un test ping sur *Nom_Serveur*.com à partir de l’appliance.
    1. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter à l’aide de l’adresse IP.
    1. Identifiez le numéro de port correct pour se connecter à vCenter Server.
    1. Vérifiez que le serveur vCenter est opérationnel.

## <a name="server-credentials-domain-fails-validation-on-the-vmware-appliance"></a>Échec de la validation des informations d’identification du serveur (domaine) sur l’appliance VMware

Vous obtenez « Échec de la validation » pour les informations d’identification de domaine ajoutées sur l’appliance VMware pour effectuer l’inventaire logiciel et l’analyse des dépendances sans agent.

### <a name="remediation"></a>Correction

1. Vérifiez que vous avez fourni le nom de domaine et les informations d’identification appropriés.
1. Vérifiez que le domaine est accessible à partir de l’appliance pour valider les informations d’identification. Il est possible que l’appliance soit confrontée à des problèmes de visibilité directe ou que le nom de domaine ne puisse pas être résolu à partir du serveur de l’appliance.
1. Sélectionnez **Modifier** pour mettre à jour le nom de domaine ou les informations d’identification. Sélectionnez **Revalider les informations d’identification** pour valider à nouveau les informations d’identification après un certain temps.

## <a name="access-is-denied-error-occurs-when-you-connect-to-hyper-v-hosts-or-clusters-during-validation"></a>Une erreur « Accès refusé » se produit lorsque vous vous connectez à des hôtes ou des clusters Hyper-V pendant la validation

Vous ne pouvez pas valider l’hôte ou le cluster Hyper-V ajouté en raison de l’erreur « Accès refusé ».

### <a name="remediation"></a>Correction

1. Assurez-vous que vous remplissez toutes les [conditions préalables pour les hôtes Hyper-V](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements). 
1. Vérifiez les étapes sur [ce site web](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts) sur la façon de préparer les hôtes Hyper-V manuellement ou avec un script PowerShell d’approvisionnement.

## <a name="the-server-does-not-support-ws-management-identify-operations-error-occurs-during-validation"></a>Une erreur « Le serveur ne prend pas en charge les opérations WS-Management Identify » lors de la validation

Vous ne pouvez pas valider les clusters Hyper-V sur l’appliance en raison de l’erreur « Le serveur ne prend pas en charge les opérations WS-Management Identify. Ignorez la partie TestConnection de la demande, puis réessayez. »

### <a name="remediation"></a>Correction

Cette erreur se produit généralement lorsque vous fournissez une configuration de proxy sur l’appliance. L’appliance se connecte aux clusters à l’aide du nom abrégé des nœuds de cluster, même si vous avez fourni le FQDN du nœud. Ajoutez le nom abrégé des nœuds de cluster à la liste du proxy de contournement sur l’appliance. Le problème est résolu et la validation du cluster Hyper-V réussit.

## <a name="cant-connect-to-host-or-cluster-error-occurs-during-validation-on-a-hyper-v-appliance"></a>Une erreur « Impossible de se connecter à l’hôte ou au cluster » se produit lors de la validation sur une appliance Hyper-V

L’erreur « Impossible de se connecter à un hôte ou à un cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 » peut se produire si le service Azure DNS pour l’appliance ne peut pas résoudre le nom de cluster ou d’hôte que vous avez fourni.

Ce problème se produit généralement lorsque vous avez ajouté l’adresse IP d’un hôte qui ne peut pas être résolue par DNS. Vous pouvez également voir cette erreur pour les hôtes d’un cluster. Elle indique que l’appliance peut se connecter au cluster, mais que le cluster renvoie les noms d’hôtes qui ne sont pas des FQDN.

### <a name="remediation"></a>Correction

Pour résoudre cette erreur, mettez à jour le fichier hosts sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes.
1. Ouvrez le Bloc-notes en tant qu’admin.
1. Ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts.
1. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
1. Enregistrez et fermez le fichier hôtes.
1. Vérifiez si l’appliance peut se connecter aux hôtes à l’aide de l’application de gestion de l’appliance. Au bout de 30 minutes, vous devriez voir les informations à jour sur ces hôtes dans le Portail Azure.

## <a name="unable-to-connect-to-server-error-occurs-during-validation-of-physical-servers"></a>Une erreur « Impossible de se connecter au serveur » se produit lors de la validation des serveurs physiques

### <a name="remediation"></a>Correction

- Assurez-vous qu’il existe de la connectivité entre l’appliance et le serveur cible.
- S’il s’agit d’un serveur Linux, assurez-vous que l’authentification par mot de passe est activée en procédant comme suit :
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier de configuration ssh à l’aide de la commande **vi /etc/ssh/sshd_config**.
    1. Définissez l’option **PasswordAuthentication** sur Oui. Enregistrez le fichier .
    1. Redémarrez le service ssh en exécutant **service sshd restart**.
- S’il s’agit d’un serveur Windows, assurez-vous que le port 5985 est ouvert pour permettre les appels WMI distants.
- Si vous découvrez un serveur GCP Linux et que vous utilisez un utilisateur root, utilisez les commandes suivantes pour modifier le paramètre par défaut de la connexion racine :
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier de configuration ssh à l’aide de la commande **vi /etc/ssh/sshd_config**.
    1. Définissez l’option **PermitRootLogin** sur Oui.
    1. Redémarrez le service ssh en exécutant **service sshd restart**.

## <a name="failed-to-fetch-bios-guid-error-occurs-for-the-server-during-validation"></a>Une erreur « Échec de la récupération du GUID du BIOS » se produit pour le serveur lors de la validation

La validation d’un serveur physique échoue sur l’appliance avec le message d’erreur « Échec de la récupération du GUID du BIOS ».

### <a name="remediation"></a>Correction

**Serveurs Linux :**

Connectez-vous au serveur cible dont la validation échoue. Exécutez les commandes suivantes pour voir s’il retourne le GUID du BIOS du serveur :

````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
Vous pouvez également exécuter les commandes à partir de l’invite de commandes sur le serveur de l’appliance en établissant une connexion SSH avec le serveur Linux cible à l’aide de la commande suivante :
````
ssh <username>@<servername>
````

**Serveurs Windows :**

Exécutez le code suivant dans PowerShell à partir du serveur de l’appliance pour le serveur cible dont la validation échoue pour voir s’il retourne le GUID du BIOS du serveur :

````
[CmdletBinding()]
Param(
  [Parameter(Mandatory=$True,Position=1)]
   [string]$Hostname
)
$HostNS = "root\cimv2"
$error.Clear()

$Cred = Get-Credential
$Session = New-CimSession -Credential $Cred -ComputerName $Hostname

if ($Session -eq $null -or $Session.TestConnection() -eq $false)
{
    Write-Host "Connection failed with $Hostname due to $error"
    exit -1
}

Write-Host "Connection established with $Hostname"
#Get-WmiObject -Query "select uuid from Win32_ComputerSystemProduct" 

$HostIntance = $Session.QueryInstances($HostNS, "WQL", "Select UUID from Win32_ComputerSystemProduct")
$HostIntance | fl *
````

Lorsque vous exécutez le code précédent, vous devez fournir le nom d’hôte du serveur cible. Il peut s’agir d’une adresse IP/d’un FQDN/d’un nom d’hôte. Après cela, vous êtes invité à fournir les informations d’identification pour la connexion au serveur.

## <a name="no-suitable-authentication-method-found-error-occurs-for-the-server-during-validation"></a>Une erreur « Aucune méthode d’authentification appropriée n’a été trouvée » se produit pour le serveur lors de la validation

Vous obtenez l’erreur « Aucune méthode d’authentification appropriée n’a été trouvée » lorsque vous essayez de valider un serveur Linux par le biais de l’appliance physique.

### <a name="remediation"></a>Correction

Assurez-vous que l’authentification par mot de passe est activée sur le serveur Linux en procédant comme suit :

1. Connectez-vous au serveur Linux. Ouvrez le fichier de configuration ssh à l’aide de la commande **vi /etc/ssh/sshd_config**.
1. Définissez l’option **PasswordAuthentication** sur **Oui**. Enregistrez le fichier .
1. Redémarrez le service ssh en exécutant **service sshd restart**.

## <a name="access-is-denied-error-occurs-when-you-connect-to-physical-servers-during-validation"></a>L’erreur « Accès refusé » se produit lorsque vous vous connectez à des serveurs physiques lors de la validation

Vous obtenez l’erreur « Le service WS-Management ne peut pas traiter la requête. Le service WMI a renvoyé une erreur d’accès refusé » lorsque vous essayez de valider un serveur Windows par le biais de l’appliance physique.

### <a name="remediation"></a>Correction

- Si vous obtenez cette erreur, assurez-vous que le compte d’utilisateur fourni (domaine/local) dans le gestionnaire de configuration de l’appliance a été ajouté aux groupes suivants : Utilisateurs de gestion à distance, Utilisateurs de l’Analyseur de performances et Utilisateurs du Journal des performances.
- Si le groupe Utilisateurs de gestion à distance n’est pas présent, ajoutez le compte d’utilisateur au groupe WinRMRemoteWMIUsers_.
- Vous pouvez également vérifier si le protocole WS-Management est activé sur le serveur en exécutant la commande suivante à l’invite de commandes du serveur cible :
    
    ```` winrm qc ````

- Si vous rencontrez toujours le problème, assurez-vous que le compte d’utilisateur dispose des autorisations d’accès à l’espace de noms CIMV2 et aux sous-espaces de noms dans le Panneau de configuration WMI. Vous pouvez définir l’accès en suivant ces étapes :

    1. Accédez au serveur dont la validation échoue sur l’appliance.
    1. Recherchez et sélectionnez **Exécuter** dans le menu **Démarrer**. Dans la boîte de dialogue **Exécuter**, entrez **wmimgmt.msc** dans la zone de texte **Ouvrir**, puis sélectionnez **Entrée**.
    1. La console wmimgmt s’ouvre. Vous pouvez y trouver **Contrôle WMI (local)** dans le volet gauche. Faites un clic droit dessus et sélectionnez **Propriétés** dans le menu.
    1. Dans la boîte de dialogue **Propriétés du contrôle WMI (local)** , sélectionnez l’onglet **Sécurité**.
    1. Dans l’onglet **Sécurité**, développez le dossier **Racine** dans l’arborescence de l’espace de noms et sélectionnez l’espace de noms **cimv2**.
    1. Sélectionnez **Sécurité** pour ouvrir la boîte de dialogue **Sécurité pour ROOT\cimv2**.
    1. Sous la section **Groupe ou noms d’utilisateurs**, sélectionnez **Ajouter** pour ouvrir la boîte de dialogue **Sélectionner les utilisateurs, les ordinateurs, les comptes de service ou les groupes**.
    1. Recherchez le compte d’utilisateur, sélectionnez-le, puis sélectionnez **OK** pour revenir à la boîte de dialogue **Sécurité pour ROOT\cimv2**.
    1. Dans la section **Groupes ou noms d’utilisateurs**, sélectionnez le compte d’utilisateur que vous venez d’ajouter. Vérifiez que les autorisations suivantes sont accordées :<br/>
       - Activer le compte <br/>
       - Appel à distance autorisé
    1. Sélectionnez **Appliquer** pour activer les autorisations définies sur le compte d’utilisateur.

- Les mêmes étapes sont également applicables sur un compte d’utilisateur local pour des serveurs hors domaine/de groupe de travail. Dans certains cas, le filtrage [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) peut bloquer certaines propriétés WMI en fonction des commandes exécutées en tant qu’utilisateur standard, de sorte que vous puissiez soit utiliser un compte administrateur local, soit désactiver UAC afin que le compte d’utilisateur local ne soit pas filtré et devienne un administrateur complet.
- La désactivation du Contrôle de compte d’utilisateur distant en modifiant l’entrée de Registre qui contrôle le Contrôle de compte d’utilisateur distant n’est pas recommandée, mais peut être nécessaire dans un groupe de travail. L’entrée de registre est HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy. Lorsque la valeur de cette entrée est égale à zéro (0), le filtrage des jetons d’accès de contrôle de compte d’utilisateur à distance est activé. Lorsque la valeur est 1, le contrôle de compte d’utilisateur distant est désactivé.

## <a name="appliance-is-disconnected"></a>L’appliance est déconnectée

Vous obtenez un message d’erreur « L’appliance est déconnectée » lorsque vous essayez d’activer la réplication sur quelques serveurs VMware à partir du portail.

Cette erreur peut se produire si l’appliance est en état d’arrêt ou si le service DRA de l’appliance ne peut pas communiquer avec Azure.

### <a name="remediation"></a>Correction

 1. Accédez au gestionnaire de configuration de l’appliance et réexécutez les prérequis pour afficher l’état du service DRA sous **Voir les services d’appliance**. 
 1. Si le service n’est pas en cours d’exécution, arrêtez et redémarrez le service à partir de l’invite de commandes avec les commandes suivantes :

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>Étapes suivantes

Configurez une appliance pour [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) ou des [serveurs physiques](how-to-set-up-appliance-physical.md).