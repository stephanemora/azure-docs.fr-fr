---
title: Comment utiliser DFS-N avec Azure Files
description: Cas d’usage courants de DFS-N avec Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f571fa6e04d19412db856a42232cb2d151ceb6ab
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108141484"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Utilisation de DFS Namespaces avec Azure Files
[Distributed File Systems Namespaces](/windows-server/storage/dfs-namespaces/dfs-overview), communément appelé DFS Namespaces ou DFS-N, est un rôle serveur Windows Server largement utilisé pour simplifier le déploiement et la maintenance des partages de fichiers SMB en production. DFS Namespaces est une technologie de virtualisation des espaces de noms de stockage, ce qui signifie qu’elle vous permet de fournir une couche d’indirection entre le chemin d’accès UNC de vos partages de fichiers et les partages de fichiers eux-mêmes. DFS Namespaces fonctionne avec des partages de fichiers SMB, indépendamment de l’hébergement de ces partages de fichiers : elle peut être utilisée avec des partages SMB hébergés sur un serveur de fichiers Windows local avec ou sans Azure File Sync, des partages de fichiers Azure directement, des partages de fichiers SMB hébergés dans Azure NetApp Files ou d’autres offres tierces, et même avec des partages de fichiers hébergés dans d’autres clouds. 

À la base, DFS Namespaces fournit un mappage entre un chemin d’accès UNC convivial, par exemple, `\\contoso\shares\ProjectX`, et le chemin d’accès UNC sous-jacent du partage SMB, comme `\\Server01-Prod\ProjectX` ou `\\storageaccount.file.core.windows.net\projectx`. Lorsque l’utilisateur final souhaite accéder à son partage de fichiers, il saisit le chemin d’accès UNC convivial, mais son client SMB accède au chemin d’accès SMB sous-jacent du mappage. Vous pouvez également étendre ce concept de base pour reprendre un nom de serveur de fichiers existant, tel que `\\MyServer\ProjectX`. Vous pouvez utiliser cette capacité pour réaliser les scénarios suivants :

- Fournir un nom résistant à la migration pour un ensemble logique de données. Dans cet exemple, vous avez un mappage comme `\\contoso\shares\Engineering` qui correspond à `\\OldServer\Engineering`. Lorsque vous effectuez la migration vers Azure Files, vous pouvez modifier votre mappage afin que votre chemin d’accès UNC convivial pointe vers `\\storageaccount.file.core.windows.net\engineering`. Lorsqu’un utilisateur final accède au chemin d’accès UNC convivial, il est redirigé sans heurt vers le chemin d’accès du partage de fichiers Azure.

- Établir un nom commun pour un ensemble logique de données qui est distribué à plusieurs serveurs sur différents sites physiques, par exemple par le biais d’Azure File Sync. Dans cet exemple, un nom tel que `\\contoso\shares\FileSyncExample` est mappé à plusieurs chemins d’accès UNC tels que `\\FileSyncServer1\ExampleShare`, `\\FileSyncServer2\DifferentShareName` ou `\\FileSyncServer3\ExampleShare`. Lorsque l’utilisateur accède à l’UNC conviviale, il reçoit une liste des chemins d’accès UNC possibles et choisit celui qui est le plus proche de lui en fonction des définitions de site Windows Server Active Directory (AD).

- Étendre un ensemble logique de données au-delà de la taille, de l’E/S ou d’autres seuils d’échelle. Cela est courant dans le cas de répertoires d’utilisateurs, où chaque utilisateur reçoit son propre dossier sur un partage, ou dans le cas de partages de travail, où les utilisateurs obtiennent un espace arbitraire pour gérer les besoins en données temporaires. Grâce à DFS Namespaces, vous créez un panorama de plusieurs dossiers dans un espace de noms cohérent. Par exemple, `\\contoso\shares\UserShares\user1` est mappé à `\\storageaccount.file.core.windows.net\user1`, `\\contoso\shares\UserShares\user2` est mappé à `\\storageaccount.file.core.windows.net\user2`, et ainsi de suite.  

Vous pouvez voir un exemple d’utilisation de DFS Namespaces avec votre déploiement Azure Files dans la présentation vidéo suivante.

[![Démonstration de la configuration de DFS-N avec Azure Files. Cliquez dessus pour la lire !](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Avancez la vidéo à 10:10 dans la barre de lecture pour voir comment configurer DFS Namespaces.

Si vous disposez déjà d’un espace de noms DFS, aucune étape particulière n’est requise pour l’utiliser avec Azure Files et File Sync. Si vous accédez à votre partage de fichiers Azure à partir d’un environnement local, des considérations normales de mise en réseau s’appliquent. Pour plus d’informations, consultez [Considérations relatives à la mise en réseau Azure Files](./storage-files-networking-overview.md).

## <a name="namespace-types"></a>Types d’espaces de noms
DFS Namespaces propose deux types d’espaces de noms principaux :

- **Espace de noms basé sur un domaine** : espace de noms hébergé dans le cadre de votre domaine Windows Server AD. Les espaces de noms hébergés dans le cadre d’AD auront un chemin d’accès UNC contenant le nom de votre domaine, par exemple, `\\contoso.com\shares\myshare`, si votre domaine est `contoso.com`. Les espaces de noms basés sur un domaine prennent en charge des limites d’échelle plus importantes et une redondance intégrée via AD. Les espaces de noms basés sur un domaine ne peuvent pas être des ressources cluster sur un cluster de basculement. 
- **Espace de noms autonome** : espace de noms hébergé sur un serveur individuel, non hébergé dans le cadre de Windows Server AD. Les espaces de noms autonomes auront un nom basé sur le nom du serveur autonome, par exemple `\\MyStandaloneServer\shares\myshare`, où votre serveur autonome s’appelle `MyStandaloneServer`. Les espaces de noms autonomes prennent en charge des cibles d’échelle de moindre envergure que les espaces de noms basés sur un domaine, mais ils peuvent être hébergés en tant que ressource cluster sur un cluster de basculement.

## <a name="requirements"></a>Configuration requise
Pour utiliser DFS Namespaces avec Azure Files et File Sync, vous devez disposer des ressources suivantes :

- Domaine Active Directory. Celui-ci peut être hébergé où vous le souhaitez, par exemple localement, sur une machine virtuelle Azure, ou même dans un autre cloud.
- Un serveur Windows qui peut héberger l’espace de noms. Un modèle de déploiement courant pour DFS Namespaces consiste à utiliser le contrôleur de domaine Active Directory pour héberger les espaces de noms. Toutefois, les espaces de noms peuvent être configurés à partir de n’importe quel serveur sur lequel est installé le rôle serveur DFS Namespaces. DFS Namespaces est disponible sur toutes les versions de Windows Server prises en charge.
- Un partage de fichiers SMB hébergé dans un environnement joint à un domaine, tel qu’un partage de fichiers Azure hébergé dans un compte de stockage joint à un domaine, ou un partage de fichiers hébergé sur un serveur de fichiers Windows joint à un domaine à l’aide d’Azure File Sync. Pour plus d’informations sur la jonction de votre compte de stockage à un domaine, consultez [Authentification basée sur l’identité](storage-files-active-directory-overview.md). Les serveurs de fichiers Windows sont joints à un domaine de la même façon, que vous utilisiez ou non Azure File Sync.
- Les partages de fichiers SMB que vous souhaitez utiliser avec DFS Namespaces doivent être accessibles à partir de vos réseaux locaux. Ceci concerne principalement les partages de fichiers Azure, mais s’applique techniquement à tout partage de fichiers hébergé dans Azure ou tout autre cloud. Pour plus d’informations sur la mise en réseau, consultez [Considérations relatives à la mise en réseau pour un accès direct](storage-files-networking-overview.md).

## <a name="install-the-dfs-namespaces-server-role"></a>Installer le rôle serveur DFS Namespaces
Si vous utilisez déjà DFS Namespaces ou si vous souhaitez configurer DFS Namespaces sur votre contrôleur de domaine, vous pouvez ignorer ces étapes en toute sécurité.

# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour installer le rôle serveur DFS Namespaces, ouvrez Gestionnaire de serveur sur votre serveur. Sélectionnez **Gérer**, puis **Ajouter des rôles et fonctionnalités**. L’Assistant qui en résulte vous guide tout au long de l’installation des composants Windows nécessaires à l’exécution et à la gestion de DFS Namespaces. 

Dans la section **Type d’installation** de l’Assistant d’installation, sélectionnez la case d’option **Installation basée sur un rôle ou une fonctionnalité**, puis sélectionnez **Suivant**. Dans la section **Sélection du serveur**, sélectionnez les serveurs sur lesquels vous souhaitez installer le rôle serveur DFS Namespaces, puis sélectionnez **Suivant**. 

Dans la section **Rôles serveur**, sélectionnez et cochez le rôle **DFS Namespaces** dans la liste des rôles. Vous le trouverez sous **Services de fichiers et de stockage** > **Services de fichiers et ISCSI**. Lorsque vous sélectionnez le rôle serveur DFS Namespaces, cela peut également ajouter tous les rôles serveur de prise en charge requis ou les fonctionnalités que vous n’avez pas déjà installées.

![Capture d’écran de l’Assistant **Ajouter des rôles et des fonctionnalités** avec le rôle **DFS Namespaces** sélectionné.](./media/files-manage-namespaces/dfs-namespaces-install.png)

Après avoir coché le rôle **DFS Namespaces**, vous pouvez sélectionner **Suivant** sur tous les écrans suivants, puis sélectionner **Installer** dès que l’Assistant active le bouton. Une fois l’installation terminée, vous pouvez configurer votre espace de noms.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
À partir d’une session PowerShell avec élévation de privilèges (ou à l’aide de la communication à distance PowerShell), exécutez les commandes suivantes.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Reprendre des noms de serveurs existants avec la consolidation de la racine
Une utilisation importante de DFS Namespaces consiste à reprendre un nom de serveur existant dans le but de refactoriser la disposition physique des partages de fichiers. Par exemple, vous pouvez souhaiter consolider les partages de fichiers de plusieurs anciens serveurs de fichiers sur un seul serveur de fichiers lors d’une migration de modernisation. Traditionnellement, la connaissance de l’utilisateur final et l’établissement de liens entre documents limitent votre capacité à consolider les partages de fichiers de serveurs de fichiers disparates sur un seul hôte, mais la fonctionnalité de consolidation de la racine de DFS Namespaces vous permet de mettre en place un serveur unique sur plusieurs noms de serveurs et de l’acheminer vers le nom de partage approprié.

Bien qu’utile pour différents scénarios de migration de centre de données, la consolidation de la racine est particulièrement utile pour l’adoption de partages de fichiers Azure natifs Cloud, car :

- Les partages de fichiers Azure ne vous permettent pas de conserver les noms de serveurs locaux existants.
- Les partages de fichiers Azure doivent être accessibles par le biais du nom de domaine complet (FQDN) du compte de stockage. Par exemple, un partage de fichiers Azure appelé `share` dans le compte de stockage `storageaccount` est toujours accessible via le chemin d’accès UNC `\\storageaccount.file.core.windows.net\share`. Cela peut prêter à confusion pour les utilisateurs finaux qui s’attendent à un nom court (par exemple, `\\MyServer\share`) ou à un nom qui est un sous-domaine du nom de domaine de l’organisation (par exemple, `\\MyServer.contoso.com\share`).

La consolidation de la racine ne peut être utilisée qu’avec des espaces de noms autonomes. Si vous disposez déjà d’un espace de noms basé sur un domaine pour vos partages de fichiers, vous n’avez pas besoin de créer un espace de noms consolidé à la racine.

### <a name="enabling-root-consolidation"></a>Activation de la consolidation de la racine
La consolidation de la racine peut être activée en définissant les clés de Registre suivantes à partir d’une session PowerShell avec élévation de privilèges (ou à l’aide de la communication à distance PowerShell).

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Création d’entrées DNS pour les noms de serveurs de fichiers existants
Pour que DFS Namespaces réponde aux noms de serveurs de fichiers existants, créez des enregistrements d’alias (CNAME) pour vos serveurs de fichiers existants qui pointent vers le nom du serveur DFS Namespaces. La procédure exacte de mise à jour de vos enregistrements DNS peut dépendre des serveurs utilisés par votre organisation et de l’utilisation éventuelle d’outils personnalisés pour automatiser la gestion de DNS. Les étapes suivantes sont présentées pour le serveur DNS inclus dans Windows Server et utilisé automatiquement par Windows AD.

# <a name="portal"></a>[Portail](#tab/azure-portal)
Sur un serveur DNS Windows, ouvrez la console de gestion DNS. Vous pouvez la trouver en sélectionnant le bouton **Démarrer** et en saisissant **DNS**. Accédez à la zone de recherche directe pour votre domaine. Par exemple, si votre domaine est `contoso.com`, la zone de recherche directe se trouve sous **Zones de recherche directe** >  **`contoso.com`** dans la console de gestion. La hiérarchie exacte indiquée dans cette boîte de dialogue dépend de la configuration DNS de votre réseau.

Cliquez avec le bouton droit sur votre zone de recherche directe et sélectionnez **Nouvel alias (CNAME)** . Dans la boîte de dialogue qui s’affiche, entrez le nom court du serveur de fichiers que vous remplacez (le nom de domaine complet sera renseigné automatiquement dans la zone de texte intitulée **Nom de domaine complet**). Dans la zone de texte intitulée **Nom de domaine complet (FQDN) de l’hôte cible**, entrez le nom du serveur DFS-N que vous avez configuré. Vous pouvez utiliser le bouton **Parcourir** pour sélectionner le serveur si vous le souhaitez. Sélectionnez **OK** pour créer l’enregistrement CNAME pour votre serveur.

![Capture d’écran illustrant le **nouvel enregistrement de ressource** pour une entrée DNS CNAME.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Sur un serveur DNS Windows, ouvrez une session PowerShell (ou utilisez la communication à distance PowerShell) pour exécuter les commandes suivantes, en renseignant `$oldServer` et `$dfsnServer` avec les valeurs appropriées pour votre environnement (`$domain` sera renseigné automatiquement avec le nom de domaine, mais vous pouvez également le saisir manuellement).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Créer un espace de noms
L’unité de base de la gestion de DFS Namespaces est l’espace de noms. La racine de l’espace de noms, ou nom, est le point de départ de l’espace de noms, de sorte que dans le chemin d’accès UNC `\\contoso.com\Public\`, la racine de l’espace de noms est `Public`. 

Si vous utilisez DFS Namespaces pour reprendre un nom de serveur existant avec consolidation de la racine, le nom de l’espace de noms doit être le nom du serveur que vous souhaitez reprendre, précédé du caractère `#`. Par exemple, si vous souhaitez reprendre un serveur existant nommé `MyServer`, vous devez créer un espace de noms DFS-N appelé `#MyServer`. La section PowerShell ci-dessous s’occupe d’ajouter le préfixe `#`, mais si vous créez un espace de noms par le biais de la console Gestion DFS, vous devez ajouter le préfixe, le cas échéant. 

# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour créer un espace de noms, ouvrez la console **Gestion DFS**. Vous pouvez la trouver en sélectionnant le bouton **Démarrer** et en saisissant **Gestion DFS**. La console de gestion qui en résulte comprend deux sections, **Espaces de noms** et **Réplications**, qui font référence à DFS Namespaces et à DFS Replication (DFS-R), respectivement. Azure File Sync fournit un mécanisme moderne de réplication et de synchronisation qui peut être utilisé à la place de DFS-R si la réplication est également souhaitée.

Sélectionnez la section **Espaces de noms**, puis sélectionnez le bouton **Nouvel espace de noms** (vous pouvez également cliquer avec le bouton droit sur la section **Espaces de noms**). L’**Assistant Nouvel espace de noms** qui en résulte vous guide dans la création d’un espace de noms. 

La première section de l’Assistant vous demande de choisir le serveur DFS Namespaces qui hébergera l’espace de noms. Plusieurs serveurs peuvent héberger un espace de noms, mais vous devrez configurer DFS Namespaces avec un seul serveur à la fois. Entrez le nom du serveur DFS Namespaces souhaité, puis sélectionnez **Suivant**. Dans la section **Nom et paramètres de l’espace de noms**, vous pouvez entrer le nom de votre choix pour l’espace de noms et sélectionner **Suivant**. 

La section **Type d’espace de noms** vous permet de choisir entre un **espace de noms basé sur un domaine** et un **espace de noms autonome**. Si vous avez l’intention d’utiliser DFS Namespaces pour conserver un nom de serveur de fichiers ou de périphérique NAS existant, vous devez sélectionner l’option Espace de noms autonome. Pour tout autre scénario, vous devez sélectionner un espace de noms basé sur un domaine. Pour plus d’informations sur le choix entre les types d’espaces de noms, consultez les [types d’espaces de noms](#namespace-types) ci-dessus.

![Capture d’écran de la sélection entre un espace de nom basé sur un domaine et un espace de nom autonome dans l’**Assistant Nouvel espace de noms**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Sélectionnez le type d’espace de noms souhaité pour votre environnement, puis sélectionnez **Suivant**. L’Assistant résume ensuite l’espace de noms à créer. Sélectionnez **Créer** pour créer l’espace de noms et **Fermer** à la fin de la boîte de dialogue.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
À partir d’une session PowerShell sur le serveur DFS Namespaces, exécutez les commandes PowerShell suivantes, en renseignant `$namespace`, `$type` et `$takeOverName` avec les valeurs appropriées pour votre environnement.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Configurer des dossiers et des cibles de dossier
Pour qu’un espace de noms soit utile, il doit avoir des dossiers et des cibles de dossier. Chaque dossier peut avoir une ou plusieurs cibles de dossier, qui sont des pointeurs vers les partages de fichiers SMB qui hébergent ce contenu. Lorsque les utilisateurs parcourent un dossier avec des cibles de dossier, l’ordinateur client reçoit une référence qui le redirige sans heurt vers l’une des cibles de dossier. Vous pouvez également avoir des dossiers sans cibles de dossier pour ajouter une structure et une hiérarchie à l’espace de noms.

Vous pouvez considérer les dossiers DFS Namespaces comme analogues aux partages de fichiers. 

# <a name="portal"></a>[Portail](#tab/azure-portal)
Dans la console Gestion DFS, sélectionnez l’espace de noms que vous venez de créer, puis sélectionnez **Nouveau dossier**. La boîte de dialogue **Nouveau dossier** qui s’affiche vous permet de créer le dossier et ses cibles.

![Capture d’écran de la boîte de dialogue **Nouveau dossier**.](./media/files-manage-namespaces/dfs-folder-targets.png)

Dans la zone de texte intitulée **Nom**, indiquez le nom du dossier. Sélectionnez **Ajouter…** pour ajouter des cibles de dossier pour ce dossier. La boîte de dialogue **Ajouter une cible de dossier** qui en résulte contient une zone de texte intitulée **Chemin d’accès à la cible de dossier** dans laquelle vous pouvez fournir le chemin d’accès UNC au dossier souhaité. Sélectionnez **OK** dans la boîte de dialogue **Ajouter une cible de dossier**. Si vous ajoutez un chemin d’accès UNC à un partage de fichiers Azure, vous pouvez recevoir un message indiquant que le serveur `storageaccount.file.core.windows.net` ne peut pas être contacté. C’est normal ; sélectionnez **Oui** pour continuer. Enfin, sélectionnez **OK** dans la boîte de dialogue **Nouveau dossier** pour créer le dossier et les cibles du dossier.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Maintenant que vous avez créé un espace de noms, un dossier et une cible de dossier, vous devez être en mesure de monter votre partage de fichiers par l’intermédiaire de DFS Namespaces. Si vous utilisez un espace de noms basé sur un domaine, le chemin d’accès complet de votre partage doit être `\\<domain-name>\<namespace>\<share>`. Si vous utilisez un espace de noms autonome, le chemin d’accès complet de votre partage doit être `\\<DFS-server>\<namespace>\<share>`. Si vous utilisez un espace de noms autonome avec consolidation de la racine, vous pouvez y accéder directement à l’aide de l’ancien nom du serveur, par exemple `\\<old-server>\<share>`.

## <a name="see-also"></a>Voir aussi
- Déploiement d’un partage de fichiers Azure : [Planification d’un déploiement Azure Files](storage-files-planning.md) et [Création d’un partage de fichiers](storage-how-to-create-file-share.md).
- Configuration de l’accès au partage de fichiers : [Authentification basée sur l’identité](storage-files-active-directory-overview.md) et [Considérations relatives à la mise en réseau pour l’accès direct](storage-files-networking-overview.md).
- [Distributed File System Namespaces de Windows Server](/windows-server/storage/dfs-namespaces/dfs-overview).