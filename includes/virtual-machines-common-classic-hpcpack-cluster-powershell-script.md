---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f0d2d59ed50c743adb19027c404bfa83a1886f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484863"
---
Selon votre environnement et vos choix, le script peut créer toute l’infrastructure de cluster, y compris le réseau virtuel Azure, les comptes de stockage, les services cloud, le contrôleur de domaine, les bases de données SQL locales ou distantes, le nœud principal et les nœuds supplémentaires du cluster. Le script peut également utiliser l’infrastructure Azure préexistante et créer uniquement les nœuds de cluster HPC.

Pour des informations générales sur la planification d’un cluster HPC Pack, consultez [Évaluation du produit et planification](https://technet.microsoft.com/library/jj899596.aspx) et [Mise en route](https://technet.microsoft.com/library/jj899590.aspx) dans la bibliothèque HPC Pack 2012 R2.

## <a name="prerequisites"></a>Conditions préalables
* **Abonnement Azure** : Vous pouvez utiliser un abonnement dans le service Azure Global ou Azure Chine. Vos limites d’abonnement affectent le nombre et le type de nœuds de cluster que vous pouvez déployer. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes du service](../articles/azure-subscription-service-limits.md).
* **Ordinateur de client Windows avec Azure PowerShell 0.8.10 ou ultérieur installé et configuré**: Consultez [prise en main Azure PowerShell](/powershell/azureps-cmdlets-docs) pour les instructions d’installation et les étapes pour vous connecter à votre abonnement Azure.
* **Script de déploiement HPC Pack IaaS**: Téléchargez et décompressez la dernière version du script à partir de la [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article se base sur la version 4.5.2 du script.
* **Fichier de configuration de script**: Créez un fichier XML que le script utilise pour configurer le cluster HPC. Pour plus d’informations et des exemples, consultez les sections suivantes de cet article et le fichier Manual.rtf qui accompagne le script de déploiement.

## <a name="syntax"></a>Syntaxe
```powershell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Exécutez le script en tant qu’administrateur.
> 
> 

### <a name="parameters"></a>parameters
* **ConfigFile**: Spécifie le chemin d’accès de fichier du fichier de configuration pour décrire le cluster HPC. Découvrez plus d’informations sur le fichier de configuration dans cette rubrique ou dans le fichier Manual.rtf, dans le dossier contenant le script.
* **AdminUserName**: Spécifie le nom d’utilisateur. Si la forêt de domaines est créée par le script, il s’agit du nom d’utilisateur d’administrateur local pour toutes les machines virtuelles et du nom d’administrateur de domaine. Si la forêt de domaines existe déjà, cela indique l’utilisateur du domaine comme nom d’utilisateur d’administrateur local pour installer HPC Pack.
* **AdminPassword**: Spécifie le mot de passe administrateur. S’il n’a pas été spécifié dans la ligne de commande, le script vous invite à entrer le mot de passe.
* **HPCImageName** (facultatif) : Spécifie le nom d’image de machine virtuelle HPC Pack permet de déployer le cluster HPC. Ce doit être une image HPC Pack fournie par Microsoft à partir d’Azure Marketplace. Si aucune image n’est spécifiée (recommandé en général), le script choisit la dernière [image HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) publiée. La dernière image est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 Update 3.
  
  > [!NOTE]
  > Le déploiement échoue si vous ne spécifiez pas d’image HPC Pack valide.
  > 
  > 
* **Fichier journal** (facultatif) : Spécifie le chemin de fichier de journal de déploiement. S’il n’a pas été spécifié, le script crée un fichier journal dans le répertoire temp de l’ordinateur qui exécute le script.
* **Force** (facultatif) : Supprime toutes les invites de confirmation.
* **NoCleanOnFailure** (facultatif) : Spécifie que les machines virtuelles Azure qui n’ont pas été correctement déployées ne sont pas supprimés. Supprimez manuellement ces machines virtuelles avant de réexécuter le script pour poursuivre le déploiement, faute de quoi le déploiement risque d’échouer.
* **PSSessionSkipCACheck** (facultatif) : Pour chaque service cloud avec des machines virtuelles déployées par ce script, un certificat auto-signé est généré automatiquement par Azure, et toutes les machines virtuelles dans le service cloud utilisent ce certificat en tant que le certificat de gestion à distance de Windows (WinRM) par défaut. Pour déployer des fonctionnalités HPC dans ces machines virtuelles Azure, le script par défaut installe temporairement ces certificats dans le magasin Ordinateur local\\Autorités de certification racines de confiance de l’ordinateur client pour supprimer l’erreur de sécurité d’autorité de certification non approuvée pendant l’exécution du script. Les certificats sont supprimés quand le script se termine. Si ce paramètre est spécifié, les certificats ne sont pas installés sur l’ordinateur client et l’avertissement de sécurité est supprimé.
  
  > [!IMPORTANT]
  > Ce paramètre n’est pas recommandé pour les déploiements de production.
  > 
  > 

### <a name="example"></a>Exemples
L’exemple suivant crée un cluster HPC Pack à l’aide du fichier de configuration *MyConfigFile.xml* et spécifie les informations d’identification de l’administrateur pour l’installation du cluster.

```powershell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Considérations supplémentaires
* Le script peut éventuellement activer la soumission de travaux via le portail web HPC Pack ou l’API REST du HPC Pack.
* Le script peut éventuellement exécuter des scripts de pré- et post-configuration personnalisés sur le nœud principal si vous souhaitez installer des logiciels supplémentaires ou configurer d’autres paramètres.

## <a name="configuration-file"></a>Fichier de configuration
Le fichier de configuration pour le script de déploiement est un fichier XML. Le fichier de schéma HPCIaaSClusterConfig.xsd se trouve dans le dossier de script de déploiement du HPC Pack IaaS. **IaaSClusterConfig** est l’élément racine du fichier de configuration qui contient les éléments enfants décrits en détail dans le fichier Manual.rtf dans le dossier de script de déploiement.

