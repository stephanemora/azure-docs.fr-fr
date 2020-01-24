---
title: Configurer la reprise d’activité après sinistre pour Hyper-V (avec VMM) sur un site secondaire avec Azure Site Recovery/PowerShell
description: Décrit comment configurer la reprise d’activité de machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire avec Azure Site Recovery et PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: d2f25774f89182004e23605bf4c37d1e1d739df7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867032"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Configurer la reprise d’activité de machines virtuelles Hyper-V sur un site secondaire à l’aide de PowerShell (Resource Manager)

Cet article montre comment automatiser les étapes de la réplication des machines virtuelles Hyper-V dans les clouds System Center Virtual Machine Manager sur le cloud Virtual Machine Manager d’un site local secondaire en utilisant [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises

- Examinez [l’architecture et les composants du scénario](hyper-v-vmm-architecture.md).
- Vérifiez les [exigences de prise en charge](site-recovery-support-matrix-to-sec-site.md) pour tous les composants.
- Assurez-vous que les serveurs Virtual Machine Manager et les hôtes Hyper-V sont conformes aux [exigences de prise en charge](site-recovery-support-matrix-to-sec-site.md).
- Vérifiez que les machines virtuelles à répliquer sont conformes à la [prise en charge de la machine répliquée](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Préparer le mappage réseau

Le [mappage réseau](hyper-v-vmm-network-mapping.md) effectue un mappage entre les réseaux de machines virtuelles Virtual Machine Manager locaux dans les clouds source et cible. Il effectue les opérations suivantes :

- Connecte les machines virtuelles aux réseaux de machines virtuelles cibles appropriés après le basculement. 
- Place de manière optimale les machines virtuelles de réplication sur les serveurs hôtes Hyper-V cibles. 
- Si vous ne configurez pas le mappage réseau, les machines virtuelles réplica ne seront connectées à aucun réseau de machines virtuelles après le basculement.

Préparez Virtual Machine Manager comme suit :

* Assurez-vous que vous avez des [réseaux logiques Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) sur les serveurs source et cible Virtual Machine Manager :

    - Le réseau logique sur le serveur source doit être associé au cloud source dans lequel se trouvent les hôtes Hyper-V.
    - Le réseau logique sur le serveur cible doit être associé au cloud cible.
* Assurez-vous que vous avez des [réseaux de machines virtuelles](https://docs.microsoft.com/system-center/vmm/network-virtual) sur les serveurs Virtual Machine Manager source et cible. Les réseaux de machines virtuelles doivent être associés au réseau logique dans chaque emplacement.
* Connectez les machines virtuelles sur les hôtes Hyper-V sources au réseau de machines virtuelles source. 

## <a name="prepare-for-powershell"></a>Préparer PowerShell

Assurez-vous qu’Azure PowerShell est prêt à l’emploi :

- Si vous utilisez déjà PowerShell, mettez à niveau vers la version 0.8.10 ou version ultérieure. [En savoir plus](/powershell/azureps-cmdlets-docs) sur la configuration de PowerShell.
- Après avoir installé et configuré PowerShell, examinez les [cmdlets de service](/powershell/azure/overview).
- Pour en savoir plus sur l’utilisation des valeurs de paramètre, des entrées dans PowerShell, consultez le guide [Prise en main](/powershell/azure/get-started-azureps).

## <a name="set-up-a-subscription"></a>Configurer un abonnement
1. Dans PowerShell, connectez-vous à votre compte Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Récupérez la liste de vos abonnements, avec les ID d’abonnement. Notez l’ID de l’abonnement dans lequel vous voulez créer le coffre Recovery Services. 

        Get-AzSubscription
3. Définissez l’abonnement du coffre.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
1. Si vous ne disposez d’aucun groupe de ressources Azure Resource Manager, créez-en un.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Créez un nouveau coffre Recovery Services. Enregistrez l’objet de coffre dans une variable pour une utilisation ultérieure. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Vous pouvez récupérer l’objet de coffre après sa création à l’aide de la cmdlet Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Définir le contexte du coffre
1. Récupérez un coffre existant.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Définir le contexte d’archivage.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Installer le fournisseur Site Recovery
1. Sur la machine Virtual Machine Manager, créez un répertoire en exécutant la commande suivante :

       New-Item c:\ASR -type directory
2. Extrayez les fichiers à l’aide du fichier de configuration du fournisseur téléchargé.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Installez le fournisseur et attendez que l’installation se termine.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Inscrivez le serveur dans le coffre.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Créer et associer une stratégie de réplication
1. Créez une stratégie de réplication, dans ce cas pour Hyper-V 2012 R2, de la manière suivante :

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Le cloud Virtual Machine Manager peut contenir des hôtes Hyper-V s’exécutant sur différentes versions de Windows Server, alors que la stratégie de réplication est destinée à une version spécifique d’un système d’exploitation. Si vos hôtes s’exécutent sur des systèmes d’exploitation différents, créez des stratégies de réplication distinctes pour chaque système. Par exemple, si vous avez cinq hôtes exécutés sur Windows Servers 2012 et trois hôtes exécutés sur Windows Server 2012 R2, créez deux stratégies de réplication. Vous en créez une pour chaque type de système d’exploitation.

2. Récupérez le conteneur de protection principal (cloud Virtual Machine Manager principal) et le conteneur de protection de récupération (cloud Virtual Machine Manager de récupération).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Récupérez la stratégie de réplication que vous avez créée en utilisant le nom convivial.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Lancez l’association du conteneur de protection (cloud Virtual Machine Manager) avec la stratégie de réplication.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Attendez que la tâche d’association de la stratégie se termine. Pour vérifier que la tâche est terminée, utilisez l’extrait de code PowerShell suivant :

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Une fois la tâche terminée, exécutez la commande suivante :

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pour vérifier que l’opération est terminée, suivez les étapes décrites à la section [Analyser l’activité](#monitor-activity).

##  <a name="configure-network-mapping"></a>Configurer le mappage réseau
1. Utilisez cette commande pour récupérer des serveurs pour le coffre actif. La commande stocke les serveurs Site Recovery dans la variable de tableau $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Exécutez cette commande pour récupérer les réseaux pour le serveur Virtual Machine Manager source et le serveur Virtual Machine Manager cible.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Le serveur Virtual Machine Manager source peut être le premier ou le deuxième serveur dans le tableau de serveurs. Vérifiez les noms de serveur Virtual Machine Manager et récupérez les réseaux appropriés.


3. L’applet de commande crée un mappage entre le réseau principal et le réseau de récupération. Il spécifie le réseau principal comme premier élément de $PrimaryNetworks. Il spécifie le réseau de récupération comme premier élément de $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Activer la protection des machines virtuelles
Après avoir correctement configuré les serveurs, les clouds et les réseaux, activez la protection pour les machines virtuelles dans le cloud.

1. Afin d’activer la protection, exécutez la commande suivante pour récupérer le conteneur de protection :

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Obtenez l’entité de protection (machine virtuelle) de la façon suivante :

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Activez la réplication de la machine virtuelle.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

> [!NOTE]
> Si vous voulez répliquer sur des disques managés activés par une CMK dans Azure, effectuez les étapes suivantes avec la commande Az de PowerShell 3.3.0 et versions ultérieures :
>
> 1. Activez le basculement vers des disques managés en mettant à jour les propriétés des machines virtuelles.
> 2. Utilisez l’applet de commande Get-AsrReplicationProtectedItem pour extraire l’ID de chaque disque de l’élément protégé.
> 3. Créez un objet de dictionnaire avec l’applet de commande New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]" pour contenir le mappage de l’ID de disque dans un jeu de chiffrement de disque. Vous devez créer ces jeux de chiffrement de disque au préalable dans la région cible.
> 4. Mettez à jour les propriétés des machines virtuelles à l’aide de l’applet de commande Set-AsrReplicationProtectedItem en passant l’objet du dictionnaire dans le paramètre -DiskIdToDiskEncryptionSetMap.

## <a name="run-a-test-failover"></a>Exécuter un test de basculement

Pour tester votre déploiement, exécutez un test de basculement pour une seule machine virtuelle. Vous pouvez également créer un plan de récupération qui contient plusieurs machines virtuelles et exécuter un test de basculement pour le plan. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé.

1. Récupérez la machine virtuelle dans laquelle les machines virtuelles basculent.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Exécution d’un basculement de test.

   Pour une machine virtuelle :

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Pour un plan de récupération :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Pour vérifier que l’opération est terminée, suivez les étapes décrites à la section [Analyser l’activité](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Exécuter des basculements planifiés et non planifiés

1. Exécutez un basculement planifié.

   Pour une machine virtuelle :

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pour un plan de récupération :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Exécutez un basculement non planifié.

   Pour une machine virtuelle :
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pour un plan de récupération :

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Analyser l’activité
Utilisez les commandes suivantes pour analyser l’activité de basculement. Attendez que le traitement se termine entre les travaux.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](/powershell/module/az.recoveryservices) sur Site Recovery avec les cmdlets PowerShell Resource Manager.
