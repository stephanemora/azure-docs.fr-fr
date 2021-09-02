---
title: Résoudre les problèmes de l’appliance Azure Migrate
description: Obtenez de l’aide pour résoudre les problèmes qui peuvent se produire avec l’appliance Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 405976fdf418c02172c3ad8129d206c3313380e2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533344"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Résoudre les problèmes de l’appliance Azure Migrate

Cet article vous aide à résoudre les problèmes liés au déploiement de l’appliance [Azure Migrate](migrate-services-overview.md) et à l’utilisation de l’appliance pour découvrir des serveurs locaux.

## <a name="whats-supported"></a>Prise en charge

[Passez en revue](migrate-appliance.md) la configuration requise pour la prise en charge de l’appliance.

## <a name="invalid-ovf-manifest-entry-during-appliance-set-up"></a>« Entrée de manifeste OVF non valide » lors de la configuration de l’appliance

**Error**

Vous obtenez l’erreur « Le fichier manifeste fourni n’est pas valide : entrée de manifeste OVF non valide » lors de la configuration d’un appareil à l’aide du modèle OVA.

**Correction**

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. [Plus d’informations](./tutorial-discover-vmware.md) Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si le déploiement échoue une nouvelle fois et vous utilisez le client VMware vSphere pour déployer le fichier OVF, essayez plutôt de le déployer à l’aide du client web vSphere. Si le déploiement échoue de nouveau, essayez un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) avec le client web (https://<*adresse IP de l’hôte*>/ui).
   - Dans **Accueil** > **Inventaire**, sélectionnez **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
4. Si le déploiement échoue toujours, contactez le support Azure Migrate.

## <a name="connectivity-check-failing-during-set-up-prerequisites"></a>La vérification de la connectivité a échoué lors de la configuration des composants requis

**Error**

Vous obtenez une erreur dans la vérification de la connectivité de l’appliance.

**Correction**

1. Vérifiez que vous pouvez vous connecter aux [URL](./migrate-appliance.md#url-access) nécessaires à partir de l’appliance.
1. Vérifiez si un proxy ou pare-feu bloque l’accès à ces URL. Si vous devez créer une liste verte, veillez à inclure toutes les URL.
1. Si un serveur proxy est configuré localement, assurez-vous de fournir les détails du proxy correctement en sélectionnant **Configurer le proxy** lors de la même étape. Veillez à fournir les informations d’identification d’autorisation si le proxy en a besoin.
1. Vérifiez que le serveur n’a pas été utilisé pour configurer l’[appliance de réplication](./migrate-replication-appliance.md) ou que l’agent du service Mobilité est installé sur le serveur.

## <a name="connectivity-check-failing-for-akams-url-during-set-up-prerequisites"></a>Échec de la vérification de la connectivité pour l’URL aka.ms lors de la configuration des composants requis

**Error**

Vous obtenez une erreur dans la vérification de la connectivité de l’appliance pour l’URL aka.ms.

**Correction**

1. Vérifiez que vous disposez d’une connectivité à Internet et que vous avez mis en liste d’autorisation l’URL -aka.ms/* pour télécharger les dernières versions des services.
2. Vérifiez si un proxy/pare-feu bloque l’accès à cette URL. Vérifiez que vous avez fourni les détails du proxy correctement à l’étape Prérequis du gestionnaire de configuration.
3. Vous pouvez revenir au gestionnaire de configuration de l’appliance et exécuter à nouveau les composants requis pour lancer la mise à jour automatique.
3. Si la nouvelle tentative ne donne rien, vous pouvez télécharger le fichier *latestcomponents.json* [ici](https://aka.ms/latestapplianceservices) pour vérifier les dernières versions des services qui échouent et les mettre à jour manuellement à partir des liens de téléchargement dans le fichier.

 Si vous avez activé l’appliance pour la **connectivité au point de terminaison privé** et que vous ne souhaitez pas autoriser l’accès à cette URL via Internet, vous pouvez [désactiver la mise à jour automatique](./migrate-appliance.md#turn-off-auto-update), car le lien aka.ms est requis pour ce service.

 >[!Note]
 >Si vous désactivez le service de mise à jour automatique, les services s’exécutant sur l’appliance n’obtiendront pas automatiquement les dernières mises à jour. Pour contourner ce problème, [mettez à jour les services de l’appliance manuellement](./migrate-appliance.md#manually-update-an-older-version).

## <a name="auto-update-check-failing-during-set-up-prerequisites"></a>La vérification de la mise à jour automatique a échoué lors de la configuration des composants requis

**Error**

Vous recevez une erreur lors de la vérification des mises à jour automatiques sur l’appliance.

**Correction**

1. Assurez-vous d’avoir créé une liste verte pour les [URL requises](./migrate-appliance.md#url-access) et qu’aucun paramètre de proxy ni de pare-feu ne les bloque.
1. En cas d’échec de la mise à jour d’un composant d’appliance, vous pouvez soit réexécuter les prérequis, soit [mettre à jour manuellement les services de l’appliance](./migrate-appliance.md#manually-update-an-older-version).

## <a name="time-sync-check-failing-during-set-up-prerequisites"></a>La vérification de la synchronisation de l’heure a échoué lors de la configuration des composants requis

**Error**

Une erreur de synchronisation de l’heure indique que l’horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l’heure actuelle.

**Correction**

- Assurez-vous que l’heure du serveur de l’appareil est synchronisée avec l’heure Internet en vérifiant les paramètres de date et heure dans le panneau de configuration.
- Vous pouvez également modifier l’heure de l’horloge sur le serveur de l’appliance pour qu’elle corresponde à l’heure actuelle en procédant comme suit :
     1. Ouvrez une invite de commandes d’administration sur le serveur.
     2. Pour vérifier le fuseau horaire, exécutez **w32tm /tz**.
     3. Pour synchroniser l’heure, exécutez **w32tm /resync**.

## <a name="vddk-check-failing-during-set-up-prerequisites-on-vmware-appliance"></a>Échec de la vérification de VDDK lors de la « Configuration des composants requis » sur l’appliance VMware

**Error**

Échec de vérification de VDDK, car l’appliance n’a pas pu trouver le kit VDDK requis installé sur l’appareil. Cela peut entraîner des échecs de réplication en cours.

**Correction**

1. Assurez-vous d’avoir téléchargé le kit VDDK 6.7 et copié ses fichiers dans **C:\Program Files\VMware\VMware Virtual Disk Development Kit** sur le serveur de l’appliance.
2. Assurez-vous qu’aucun autre logiciel ou application n’utilise une autre version du kit VDDK sur l’appareil.

## <a name="getting-project-key-related-error-during-appliance-registration"></a>Obtention d’une erreur liée à la clé du projet lors de l’inscription de l’appareil

**Error** 

Vous avez des problèmes lorsque vous essayez d’inscrire l’appliance à l’aide de la clé de projet Azure Migrate copiée à partir du projet.

**Correction**

1. Vérifiez que vous avez copié la clé appropriée à partir du projet : sur la carte **Azure Migrate : Découverte et évaluation** de votre projet, sélectionnez **Découvrir**, puis **Gérer l’appliance existante** à l’étape 1. Sélectionnez le nom de l’appliance (pour laquelle vous avez généré une clé précédemment) dans le menu déroulant et copiez la clé correspondante.
2. Veillez à coller la clé sur l’appliance ayant le **type de cloud** (Public/US Gov) et le **type d’appliance** (VMware/Hyper-V/physique ou autre) appropriés. Vérifiez le type de cloud et de scénario en haut du gestionnaire de configuration de l’appliance.

## <a name="failed-to-connect-to-the-azure-migrate-project-during-appliance-registration"></a>« Échec de la connexion au projet Azure Migrate » lors de l’inscription de l’appareil

**Error**

Une fois la connexion établie avec un compte d’utilisateur Azure, l’étape d’inscription de l’appliance échoue avec le message **La connexion au projet Azure Migrate a échoué. Consultez les détails de l’erreur et suivez les étapes de correction en cliquant sur Réessayer**.

Ce problème se produit quand le compte d’utilisateur Azure qui a été utilisé pour se connecter à partir du gestionnaire de configuration de l’appliance est différent du compte d’utilisateur qui a été utilisé pour générer la clé de projet Azure Migrate dans le portail.

**Correction**
1. Pour effectuer l’inscription de l’appliance, utilisez le compte d’utilisateur Azure qui a permis de générer la clé de projet Azure Migrate dans le portail OU
1. attribuez les rôles et les [autorisations](./tutorial-discover-vmware.md#prepare-an-azure-user-account) nécessaires à l’autre compte d’utilisateur Azure utilisé pour l’inscription de l’appliance

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-during-appliance-registration"></a>« Échec de l’opération Azure Active Directory (AAD) avec l’état interdit » lors de l’inscription de l’appliance

**Error**

Vous ne parvenez pas à effectuer l’inscription en raison de privilèges AAD insuffisants et j’obtiens l’erreur « L’opération Azure Active Directory (AAD) a échoué avec l’état Interdit ».

**Correction**

Veillez à disposer des [autorisations nécessaires](./tutorial-discover-vmware.md#prepare-an-azure-user-account) pour créer et gérer des applications AAD dans Azure. Vous devez avoir le rôle **Développeur d’applications** ou le rôle d’utilisateur avec l’autorisation **L’utilisateur peut inscrire des applications** accordée au niveau du locataire.

## <a name="forbidden-to-access-key-vault-during-appliance-registration"></a>« Accès interdit à Key Vault » lors de l’inscription de l’appareil

**Error**

L’opération de création ou de mise à jour de coffre de clés Azure pour « {KeyVaultName} » a échoué en raison de l’erreur : « {KeyVaultErrorMessage} ».

Cela se produit généralement quand le compte d’utilisateur Azure utilisé pour inscrire l’appliance est différent du compte utilisé pour générer la clé de projet Azure Migrate dans le portail (c’est-à-dire lorsque le coffre de clés a été créé).

**Correction**

1. Assurez-vous que le compte d’utilisateur actuellement connecté sur l’appliance dispose des autorisations requises sur Key Vault (comme mentionné dans le message d’erreur). Le compte utilisateur a besoin des autorisations mentionnées [ici](./tutorial-discover-vmware.md#prepare-an-azure-user-account).
2. Allez dans Key Vault et assurez-vous que votre compte d’utilisateur a une stratégie d’accès avec toutes les autorisations _Clé, Secret et Certificat_ attribuées sous Stratégie d’accès à Key Vault. [En savoir plus](../key-vault/general/assign-access-policy-portal.md)
3. Si vous avez activé l’appliance pour la **connectivité privée des points de terminaison**, assurez-vous que l’appliance est hébergée dans le même réseau virtuel que celui où Key Vault a été créé ou qu’elle est connectée au réseau virtuel Azure (où Key Vault a été créé) par une liaison privée. Assurez-vous que la liaison privée Key Vault peut être résolue à partir de l’appliance. Allez sur **Azure Migrate** : **Découverte** et **Évaluation**> **Propriétés** pour trouver les détails des points de terminaison privés pour des ressources comme l’instance Key Vault créée lors de la création de la clé Azure Migrate. [En savoir plus](./troubleshoot-network-connectivity.md)
4. Si vous disposez des autorisations et de la connectivité requises, réessayez l’inscription sur l’appliance après un certain temps.

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>Impossible de se connecter à vCenter Server lors de la validation

**Error**

Si cette erreur de connexion s’affiche, il est possible que vous ne puissiez pas vous connecter à vCenter Server *Nom_Serveur*.com:9443. Les détails de l’erreur indiquent qu’aucun des points de terminaison qui écoutent `https://\*servername*.com:9443/sdk` ne peut accepter le message.

**Correction**

- Vérifiez si vous exécutez la dernière version de l’appliance. Si ce n’est pas le cas, mettez-la à niveau vers la [dernière version](./migrate-appliance.md).
- Si le problème se produit encore avec la dernière version, il est possible que l’appliance ne parvienne pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué soit incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tente de se connecter au port numéro 443.

    1. Effectuez un test ping sur *Nom_Serveur*.com à partir de l’appliance.
    2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter à l’aide de l’adresse IP.
    3. Identifiez le numéro de port correct pour se connecter à vCenter Server.
    4. Vérifiez que vCenter Server est opérationnel.

## <a name="server-credentials-domain-failing-validation-on-vmware-appliance"></a>Échec de validation des informations d’identification du serveur (domaine) sur l’appareil VMware

**Error**

Vous obtenez « Échec de la validation » pour les informations d’identification de domaine ajoutées sur l’appareil VMware pour effectuer l’inventaire logiciel et l’analyse des dépendances sans agent.

**Correction**

1. Vérifiez que vous avez fourni le nom de domaine et les informations d’identification appropriés
1. Vérifiez que le domaine est accessible à partir de l’appliance pour valider les informations d’identification. Il est possible que l’appliance soit confrontée à des problèmes de visibilité directe ou que le nom de domaine ne puisse pas être résolu à partir du serveur de l’appliance.
1. Sélectionnez **Modifier** pour mettre à jour le nom de domaine ou les informations d’identification, puis **Revalider les informations d’identification** pour revalider les informations d’identification après un certain temps

## <a name="access-is-denied-when-connecting-to-hyper-v-hosts-or-clusters-during-validation"></a>« Accès refusé » lors de la connexion à des hôtes ou des clusters Hyper-V pendant la validation

**Error**

Vous ne parvenez pas à valider l’hôte/le cluster Hyper-V ajouté en raison d’une erreur : « Accès refusé ».

**Correction**

1. Assurez-vous que vous remplissez toutes les [conditions préalables pour les hôtes Hyper-V](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements). 
1. Vérifiez les étapes [**ici**](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts) sur la façon de préparer les hôtes Hyper-V manuellement ou avec un script PowerShell de provisionnement.

## <a name="the-server-does-not-support-ws-management-identify-operations-during-validation"></a>« Le serveur ne prend pas en charge les opérations WS-Management Identify » lors de la validation

**Error**

Vous n’êtes pas en mesure de valider les clusters Hyper-V sur l’appliance en raison de l’erreur suivante : « Le serveur ne prend pas en charge les opérations WS-Management Identify. Ignorez la partie TestConnection de la demande, puis réessayez. »

**Correction**

Cela se produit généralement lorsque vous fournissez une configuration de proxy sur l’appliance. L’appliance se connecte aux clusters à l’aide du nom court des nœuds du cluster, même si vous avez fourni le nom de domaine complet du nœud. Ajoutez le nom abrégé des nœuds de cluster à la liste du proxy de contournement sur l’appliance, le problème est résolu et la validation du cluster Hyper-V réussit.

## <a name="cant-connect-to-host-or-cluster-during-validation-on-hyper-v-appliance"></a>« Impossible de se connecter à l’hôte ou au cluster » lors de la validation sur l’appliance Hyper-V

**Error**

« Impossible de se connecter à un hôte ou à un cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 » peut se produire si le service Azure DNS pour l’appliance ne peut pas résoudre le nom de cluster ou d’hôte que vous avez fourni.

Cela se produit généralement lorsque vous avez ajouté l’adresse IP d’un hôte qui ne peut pas être résolue par DNS. Vous pouvez également voir cette erreur pour les hôtes d’un cluster. Cela indique que l’appliance peut se connecter au cluster, mais que le cluster retourne des noms d’hôte qui ne sont pas des noms de domaine complets.

**Correction**

Pour résoudre cette erreur, mettez à jour le fichier hôtes sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes :
1. Ouvrez le Bloc-notes en tant qu’admin.
1. Ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts.
1. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
1. Enregistrez et fermez le fichier hôtes.
1. Vérifiez si l’appliance peut se connecter aux hôtes en utilisant l’application de gestion de l’appliance. Au bout de 30 minutes, vous devriez voir les informations à jour sur ces hôtes dans le Portail Azure.

## <a name="unable-to-connect-to-server-during-validation-of-physical-servers"></a>« Impossible de se connecter au serveur » lors de la validation des serveurs physiques

**Correction**

- Assurez-vous qu’il existe de la connectivité entre l’appliance et le serveur cible.
- S’il s’agit d’un serveur Linux, assurez-vous que l’authentification par mot de passe est activée en procédant comme suit :
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier config ssh à l’aide de la commande « vi /etc/ssh/sshd_config ».
    2. Définissez l’option « PasswordAuthentication » sur Oui. Enregistrez le fichier .
    3. Redémarrez le service ssh en exécutant « service sshd restart ».
- S’il s’agit d’un serveur Windows, assurez-vous que le port 5985 est ouvert pour permettre les appels WMI distants.
- Si vous découvrez un serveur GCP Linux et que vous utilisez un utilisateur root, utilisez les commandes suivantes pour modifier le paramètre par défaut de la connexion racine
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier config ssh à l’aide de la commande « vi /etc/ssh/sshd_config ».
    2. Définissez l’option « PermitRootLogin » sur Oui.
    3. Redémarrez le service ssh en exécutant « service sshd restart ».

## <a name="failed-to-fetch-bios-guid-for-server-during-validation"></a>« Échec de la récupération du GUID du BIOS » pour le serveur lors de la validation

**Error**

La validation d’un serveur physique échoue sur l’appliance avec le message d’erreur « Échec de la récupération du GUID du BIOS ».

**Correction**

**Serveurs Linux :** Connectez-vous au serveur cible qui échoue à la validation et exécutez les commandes suivantes pour voir s’il renvoie le GUID du BIOS du serveur :
````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
Vous pouvez également exécuter les commandes à partir de l’invite de commandes sur le serveur de l’appliance en établissant une connexion SSH avec le serveur Linux cible à l’aide de la commande suivante :
````
ssh <username>@<servername>
````

**Serveurs Windows :** Exécutez le code suivant dans PowerShell à partir du serveur de l’appliance pour le serveur cible dont la validation échoue pour voir s’il renvoie le GUID du BIOS du serveur :
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

Lors de l’exécution du code ci-dessus, vous devez fournir le nom d’hôte du serveur cible qui peut être l’adresse IP/le nom de domaine complet/le nom d’hôte. Après cela, vous serez invité à fournir les informations d’identification pour la connexion au serveur.

## <a name="no-suitable-authentication-method-found-for-server-during-validation"></a>« Aucune méthode d’authentification appropriée n’a été trouvée pour le serveur lors de la validation

**Error**

Vous obtenez cette erreur lorsque vous essayez de valider un serveur Linux via l’appliance physique - « Aucune méthode d’authentification appropriée n’a été trouvée ».

**Correction**

Assurez-vous que l’authentification par mot de passe est activée sur le serveur Linux en procédant comme suit :

1. Connectez-vous au serveur Linux, puis ouvrez le fichier config ssh à l’aide de la commande « vi /etc/ssh/sshd_config ».
2. Définissez l’option « PasswordAuthentication » sur Oui. Enregistrez le fichier .
3. Redémarrez le service ssh en exécutant « service sshd restart ».

## <a name="access-is-denied-when-connecting-to-physical-servers-during-validation"></a>« Accès refusé » lors de la connexion aux serveurs physiques lors de la validation

**Error**

Vous obtenez cette erreur lorsque vous essayez de valider un serveur Windows par le biais de l’appliance physique : « Le service Gestion des services Web ne peut pas traiter la demande. Le service WMI a renvoyé une erreur d’accès refusé. »

**Correction**

- Si vous obtenez cette erreur, assurez-vous que le compte d’utilisateur fourni (domaine/local) sur le gestionnaire de configuration de l’appliance a été ajouté à ces groupes : Utilisateurs de gestion à distance, Utilisateurs de l’Analyseur de performances et Utilisateurs du Journal des performances.
- Si le groupe d’utilisateurs Gestion à distance n’est pas présent, ajoutez un compte d’utilisateur au groupe WinRMRemoteWMIUsers_.
- Vous pouvez également vérifier si le protocole WS-Management est activé sur le serveur en exécutant la commande suivante à l’invite de commandes du serveur cible.
    
    ```` winrm qc ````
- Si vous rencontrez toujours le problème, assurez-vous que le compte d’utilisateur dispose des autorisations d’accès à l’espace de noms CIMV2 et aux sous-espaces de noms dans le panneau de configuration WMI. Vous pouvez définir l’accès en suivant ces étapes :
    1.  Accéder au serveur dont la validation échoue sur l’appliance
    2.  Recherchez et sélectionnez « Exécuter » dans le menu Démarrer. Dans la boîte de dialogue « Exécuter », tapez wmimgmt.msc dans le champ de texte « Ouvrir : », puis appuyez sur Entrée.
    3.  La console wmimgmt s’ouvre, où vous pouvez trouver « Contrôle WMI (local) » dans le volet gauche. Cliquez dessus avec le bouton droit et sélectionnez « Propriétés » dans le menu.
    4.  Dans la boîte de dialogue « Propriétés du contrôle WMI (local) », cliquez sur l’onglet « Sécurité ».
    5.  Dans l’onglet Sécurité, développez le dossier racine dans l’arborescence de l’espace de noms et sélectionnez l’espace de noms « CIMV2 ».
    6.  Cliquez sur le bouton « Sécurité » pour ouvrir la boîte de dialogue « Sécurité pour ROOT\cimv2 ».
    7.  Sous la section « Groupe ou noms d’utilisateurs », cliquez sur le bouton « Ajouter » pour ouvrir la boîte de dialogue « Sélectionner les utilisateurs, les ordinateurs, les comptes de service ou les groupes ».
    8.  Recherchez le compte d’utilisateur, sélectionnez-le et cliquez sur le bouton « OK » pour revenir à la boîte de dialogue « Sécurité pour ROOT\cimv2 ».
    9.  Dans la section Groupe ou noms d’utilisateurs, sélectionnez le compte d’utilisateur que vous venez d’ajouter et vérifiez si les autorisations suivantes sont autorisées :<br/>
    Activer le compte <br/>
    Appel à distance autorisé
    10. Cliquez sur Appliquer pour activer les autorisations définies sur le compte d’utilisateur.

- Les mêmes étapes sont également applicables sur un compte d’utilisateur local pour des serveurs hors domaine/de groupe de travail, mais dans certains cas, le filtrage [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) peut bloquer certaines propriétés WMI en fonction des commandes exécutées en tant qu’utilisateur standard, de sorte que vous pouvez utiliser soit utiliser un compte administrateur local soit désactiver UAC afin que le compte d’utilisateur local ne soit pas filtré et devienne un administrateur complet.
- La désactivation du contrôle de compte d’utilisateur distant en modifiant l’entrée de Registre qui contrôle le contrôle de compte d’utilisateur distant n’est pas recommandée, mais peut être nécessaire dans un groupe de travail. L’entrée de registre est HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy. Lorsque la valeur de cette entrée est égale à zéro (0), le filtrage des jetons d’accès de contrôle de compte d’utilisateur à distance est activé. Lorsque la valeur est 1, le contrôle de compte d’utilisateur distant est désactivé.

## <a name="appliance-is-disconnected"></a>L’appliance est déconnectée

**Error**

Vous voyez le message d’erreur « L’appliance est déconnectée » lorsque vous essayez d’activer la réplication sur quelques serveurs VMware à partir du portail.

Cela peut se produire si l’appliance est en état d’arrêt ou si le service DRA de l’appliance ne peut pas communiquer avec Azure.

**Correction**

 1. Accédez au gestionnaire de configuration de l’appliance et réexécutez les prérequis pour afficher l’état du service DRA sous **Voir les services d’appliance**. 
 1. Si le service n’est pas en cours d’exécution, arrêtez et redémarrez le service à partir de l’invite de commandes avec les commandes suivantes :

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>Étapes suivantes

Configurez une appliance pour [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) ou des [serveurs physiques](how-to-set-up-appliance-physical.md).