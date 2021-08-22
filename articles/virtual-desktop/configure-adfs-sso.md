---
title: Configurer l’authentification unique AD FS Azure Virtual Desktop - Azure
description: Comment configurer l’authentification unique AD FS pour un environnement Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/30/2021
ms.author: helohr
ms.openlocfilehash: 3c6e61754d9332cbdfbea6b971363c1b0d6cb4fe
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289429"
---
# <a name="configure-ad-fs-single-sign-on-for-azure-virtual-desktop"></a>Configurer l’authentification unique AD FS pour Azure Virtual Desktop

Cet article vous guide tout au long du processus de configuration de l’authentification unique (SSO) Active Directory Federation Service (AD FS) pour Azure Virtual Desktop.

> [!NOTE]
> Azure Virtual Desktop (classique) ne prend pas en charge cette fonctionnalité.

## <a name="requirements"></a>Spécifications

Avant de configurer l’authentification unique AD FS, vous devez exécuter la configuration suivante dans votre environnement :

* Vous devez déployer le rôle **Services de certificats Active Directory**. Tous les serveurs qui ont ce rôle doivent être joints à un domaine, avoir les dernières mises à jour Windows et être configurés en tant qu’[Autorités de certification d’entreprise](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731183%28v%3dws.10%29).
* Vous devez déployer le rôle **Services ADFS (Active Directory Federation Services)** . Tous les serveurs qui ont ce rôle doivent être joints à un domaine, avoir les dernières mises à jour Windows et exécuter Windows Server 2016 ou ultérieur. Consultez notre [tutoriel sur la fédération](../active-directory/hybrid/tutorial-federation.md) pour commencer à configurer ce rôle.
* Nous vous recommandons de configurer le rôle **Proxy d’application web** pour sécuriser la connexion de votre environnement aux serveurs AD FS. Tous les serveurs qui ont ce rôle doivent avoir les dernières mises à jour Windows et exécuter Windows Server 2016 ou ultérieur. Consultez ce [Guide du proxy d’application web](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11)) pour commencer à configurer ce rôle.
* Vous devez déployer **Azure AD Connect** pour synchroniser les utilisateurs avec Azure AD. Azure AD Connect doit être configuré en [mode Fédération](../active-directory/hybrid/how-to-connect-install-custom.md).
* [Configurez votre environnement PowerShell](powershell-module.md) pour Azure Virtual Desktop sur le serveur AD FS.
* Lorsque vous utilisez Windows 10 20H1 ou 20H2 pour vous connecter à Azure Virtual Desktop, vous devez installer la **mise à jour cumulative 2021-04 pour Windows 10 (KB5001330)** ou ultérieur pour que l’authentification unique fonctionne correctement.

> [!NOTE]
> Cette solution n’est pas prise en charge avec Azure AD Domain Services. Vous devez utiliser un contrôleur de domaine Active Directory.

## <a name="supported-clients"></a>Clients pris en charge

Les clients Azure Virtual Desktop suivants prennent en charge cette fonctionnalité :

* [Client Windows Desktop](./user-documentation/connect-windows-7-10.md)
* [Client web](./user-documentation/connect-web.md)

## <a name="configure-the-certificate-authority-to-issue-certificates"></a>Configurer l’Autorité de certification pour délivrer des certificats

Vous devez créer correctement les modèles de certificats suivants pour permettre à AD FS d’utiliser l’authentification unique :

* Tout d’abord, vous devez créer le modèle de certificat **Agent d’inscription Exchange (demande hors connexion)** . AD FS utilise le modèle de certificat Agent d’inscription Exchange pour demander des certificats pour le compte de l’utilisateur.
* Vous devez également créer le modèle de certificat **Connexion par carte à puce**, qu’utilisera AD FS pour créer le certificat de connexion.

Une fois que vous avez créé ces modèles de certificat, vous devez activer les modèles sur l’Autorité de certification pour qu’AD FS puisse les demander.

> [!NOTE]
> Cette solution génère de nouveaux certificats à court terme pour chaque ouverture de session utilisateur, ce qui peut remplir la base de données de l’Autorité de certification au fur et à mesure si vous avez beaucoup d’utilisateurs. Vous pouvez éviter cela en [configurant une Autorité de certification pour le traitement des certificats non persistants](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ff934598(v=ws.10)).

### <a name="create-the-enrollment-agent-certificate-template"></a>Créer le modèle de certificat Agent d’inscription

Selon votre environnement, vous avez peut-être déjà configuré un modèle de certificat Agent d’inscription dans d’autres buts, comme Windows Hello Entreprise, des certificats Connexion ou des certificats VPN. Si c’est le cas, vous devez le modifier pour prendre en charge l’authentification unique. Si ce n’est pas le cas, vous pouvez créer un nouveau modèle.

Pour déterminer si vous utilisez déjà un modèle de certificat Agent d’inscription, exécutez la commande PowerShell suivante sur le serveur AD FS et voyez si une valeur est retournée. Si c’est vide, créez un modèle de certificat Agent d’inscription. Dans le cas contraire, mémorisez le nom et mettez à jour le modèle de certificat Agent d’inscription existant.

```powershell
Import-Module adfs
(Get-AdfsCertificateAuthority).EnrollmentAgentCertificateTemplateName
```

Pour créer un modèle de certificat Agent d’inscription :

1. Sur l’Autorité de certification, exécutez **mmc.exe** à partir du menu Démarrer pour lancer la **console MMC** (Microsoft Management Console).
2. Sélectionnez **Fichier...**  > **Ajouter/Composant logiciel enfichable distant...**  > **Modèles de certificat** > **Ajouter >**  > **OK** pour afficher la liste des modèles de certificat.
3. Développez les **Modèles de certificats**, cliquez avec le bouton droit sur **Agent d’inscription Exchange (demande hors connexion)** et sélectionnez **Dupliquer le modèle**.
4. Sélectionnez l’onglet **Général**, puis entrez « Agent d’inscription ADFS » dans le champ **Nom complet du modèle**. Le nom du modèle est alors automatiquement défini « ADFSEnrollmentAgent ».
5. Sélectionnez l’onglet **Sécurité**, puis **Ajouter...** .
6. Ensuite, sélectionnez **Types d’objets...** , **Comptes de service**, puis **OK**.
7. Entrez le nom du compte de service pour AD FS, puis sélectionnez **OK**.
   * Dans une configuration AD FS isolée, le compte de service s’appellera « adfssvc$ ».
   * Si vous configurez AD FS avec Azure AD Connect, le compte de service s’appellera « aadcsvc$ ».
8. Une fois que le compte de service est ajouté et visible sous l’onglet **Sécurité**, sélectionnez-le dans le volet **Noms de groupes ou d’utilisateurs**, sélectionnez **Autoriser** pour « Inscription » et « Inscription automatique » dans le volet **Autorisations pour le compte de service AD FS**, puis sélectionnez **OK** pour enregistrer.

   :::image type="content" source="media/adfs-enrollment-properties-security.png" alt-text="Capture d’écran montrant l’onglet Sécurité du modèle de certificat Agent d’inscription une fois qu’il est correctement configuré.":::

Pour mettre à jour un modèle de certificat Agent d’inscription existant :

1. Sur l’Autorité de certification, exécutez **mmc.exe** à partir du menu Démarrer pour lancer la **console MMC** (Microsoft Management Console).
2. Sélectionnez **Fichier...**  > **Ajouter/Composant logiciel enfichable distant...**  > **Modèles de certificat** > **Ajouter >**  > **OK** pour afficher la liste des modèles de certificat.
3. Développez les **Modèles de certificat**, puis double-cliquez sur le modèle qui correspond à celui configuré sur le serveur AD FS. Sous l’onglet **Général**, le nom du modèle doit correspondre au nom que vous avez trouvé ci-dessus.
4. Sélectionnez l’onglet **Sécurité**, puis **Ajouter...** .
5. Ensuite, sélectionnez **Types d’objets...** , **Comptes de service**, puis **OK**.
6. Entrez le nom du compte de service pour AD FS, puis sélectionnez **OK**.
   * Dans une configuration AD FS isolée, le compte de service s’appellera « adfssvc$ ».
   * Si vous configurez AD FS avec Azure AD Connect, le compte de service s’appellera « aadcsvc$ ».
7. Une fois que le compte de service est ajouté et visible sous l’onglet **Sécurité**, sélectionnez-le dans le volet **Noms de groupes ou d’utilisateurs**, sélectionnez **Autoriser** pour « Inscription » et « Inscription automatique » dans le volet **Autorisations pour le compte de service AD FS**, puis sélectionnez **OK** pour enregistrer.

### <a name="create-the-smartcard-logon-certificate-template"></a>Créer le modèle de certificat Connexion par carte à puce

Pour créer le modèle de certificat Connexion par carte à puce :

1. Sur l’Autorité de certification, exécutez **mmc.exe** à partir du menu Démarrer pour lancer la **console MMC** (Microsoft Management Console).
2. Sélectionnez **Fichier...**  > **Ajouter/Composant logiciel enfichable distant...**  > **Modèles de certificat** > **Ajouter** > **OK** pour afficher la liste des modèles de certificat.
3. Développez les **Modèles de certificats**, cliquez avec le bouton droit sur **Connexion par carte à puce** et sélectionnez **Dupliquer le modèle**.
4. Sélectionnez l’onglet **Général**, puis entrez « ADFS SSO » dans le champ **Nom complet du modèle**. Le nom du modèle est alors automatiquement défini « ADFSSSO ».
   > [!NOTE]
   > Étant donné que ce certificat est à la demande, nous vous recommandons d’écourter la période de validité à 8 heures et la période de renouvellement à 1 heure.

5. Sélectionnez l’onglet **Nom du sujet**, puis **Fournir dans la demande**. Quand vous voyez un message d’avertissement, sélectionnez **OK**.

   :::image type="content" source="media/adfs-sso-properties-subject-inline.png" alt-text="Capture d’écran montrant l’onglet Nom du sujet du modèle de certificat SSO et à quoi celui-ci doit ressembler quand il est correctement configuré." lightbox="media/adfs-sso-properties-subject-expanded.png":::

6. Sélectionnez l’onglet **Conditions d’émission**.
7. Sélectionnez **Ce nombre de signatures autorisées** et entrez la valeur **1**.

   :::image type="content" source="media/adfs-sso-properties-issuance-inline.png" alt-text="Capture d’écran montrant l’onglet Conditions d’émission du modèle de certificat SSO et à quoi celui-ci doit ressembler quand il est correctement configuré." lightbox="media/adfs-sso-properties-issuance-expanded.png":::

8. Pour **Stratégie d’application**, sélectionnez **Agent de demande de certificat**.
9.  Sélectionnez l’onglet **Sécurité**, puis **Ajouter...** .
10. Sélectionnez **Types d’objets...** , **Comptes de service**, puis **OK**.
11. Entrez le nom du compte de service pour AD FS comme vous l’avez fait dans la section [Créer le modèle de certificat Agent d’inscription](#create-the-enrollment-agent-certificate-template).
    * Dans une configuration AD FS isolée, le compte de service s’appellera « adfssvc$ ».
    * Si vous configurez AD FS avec Azure AD Connect, le compte de service s’appellera « aadcsvc$ ».
12. Une fois que le compte de service est ajouté et visible sous l’onglet **Sécurité**, sélectionnez-le dans le volet **Noms de groupes ou d’utilisateurs**, sélectionnez **Autoriser** pour « Inscription » et « Inscription automatique », puis sélectionnez **OK** pour enregistrer.

   :::image type="content" source="media/adfs-sso-properties-security.png" alt-text="Capture d’écran montrant l’onglet Sécurité du modèle de certificat SSO une fois qu’il est correctement configuré.":::

### <a name="enable-the-new-certificate-templates"></a>Activez les nouveaux modèles de certificat :

Pour activer les nouveaux modèles de certificat :

1. Sur l’Autorité de certification, exécutez **mmc.exe** à partir du menu Démarrer pour lancer la **console MMC** (Microsoft Management Console).
2. Sélectionnez **Fichier...**  > **Ajouter/Supprimer un composant logiciel enfichable...**  > **Autorité de certification** > **Ajouter >**  > **Terminer** > et **OK** pour afficher l’Autorité de certification.
3. Développez l’Autorité de certification dans le volet gauche et ouvrez **Modèles de certificats**.
4. Cliquez avec le bouton droit dans le volet central qui affiche la liste des modèles de certificats, sélectionnez **Nouveau**, puis **Modèle de certificat à délivrer**.
5. Sélectionnez à la fois **Agent d’inscription ADFS** et **ADFS SSO**, puis sélectionnez **OK**. Vous devriez voir les deux modèles dans le volet central.

   :::image type="content" source="media/adfs-certificate-templates.png" alt-text="Capture d’écran montrant la liste des modèles de certificats qui peuvent être délivrés, notamment le nouvel Agent d’inscription ADFS et ADFS SSO.":::

   > [!NOTE]
   > Si vous avez déjà configuré un modèle de certificat Agent d’inscription, il vous suffit d’ajouter le modèle ADFS SSO.

## <a name="configure-the-ad-fs-servers"></a>Configurer les serveurs AD FS

Vous devez configurer les serveurs AD FS (Active Directory Federation Services) pour utiliser les nouveaux modèles de certificats et définir l’approbation de partie de confiance pour prendre en charge SSO.

L’approbation de partie de confiance entre votre serveur AD FS et le service Azure Virtual Desktop permet de transférer correctement les demandes de certificat d’authentification unique à votre environnement de domaine.

Quand vous configurez l’authentification unique AD FS, vous devez choisir une clé partagée ou un certificat :

* Si vous avez un seul serveur AD FS, vous pouvez choisir une clé partagée ou un certificat.
* Si vous avez plusieurs serveurs AD FS, vous devez choisir un certificat.

La clé partagée ou le certificat utilisé pour générer le jeton permettant de se connecter à Windows doit être stocké en lieu sûr dans [Azure Key Vault](../key-vault/general/overview.md). Vous pouvez stocker le secret dans un coffre de clés existant ou en déployer un nouveau. Dans les deux cas, vous devez veiller à définir la bonne stratégie d’accès afin que le service Azure Virtual Desktop puisse y accéder.

Lorsque vous utilisez un certificat, vous pouvez vous servir de n’importe quel certificat à usage général. Aucun nom d’objet ni aucun autre nom de l’objet (SAN) ne sont requis. Même si ce n’est pas obligatoire, il est recommandé de créer un certificat délivré par une Autorité de certification valide. Ce certificat peut être créé directement dans Azure Key Vault et doit avoir une clé privée exportable. La clé publique peut être exportée et utilisée pour configurer le serveur AD FS à l’aide du script ci-dessous. Notez que ce certificat est différent du certificat SSL AD FS qui doit avoir un nom d’objet propre et une Autorité de certification valide.

Le script PowerShell **ConfigureWVDSSO.ps1** disponible dans [PowerShell Gallery](https://www.powershellgallery.com/packages/ConfigureWVDSSO) configure votre serveur AD FS pour l’approbation de partie de confiance et installe le certificat si nécessaire.

Ce script n’a qu’un seul paramètre obligatoire, *ADFSAuthority*, qui est l’URL qui correspond à votre serveur AD FS et utilise « /adfs » comme suffixe. Par exemple : `https://adfs.contoso.com/adfs`.

1. Sur les machines virtuelles AD FS, exécutez l’applet de commande PowerShell suivante pour configurer AD FS afin d’utiliser les modèles de certificats de la section précédente :
  
   ```powershell
   Set-AdfsCertificateAuthority -EnrollmentAgentCertificateTemplate "ADFSEnrollmentAgent" -LogonCertificateTemplate "ADFSSSO" -EnrollmentAgent
   ```
 
   > [!NOTE]
   > Si vous avez déjà configuré un EnrollmentAgentCertificateTemplate, assurez-vous d’utiliser le nom de modèle existant au lieu d’ADFSEnrollmentAgent.

2. Exécutez le script ConfigureWVDSSO.ps1.
   > [!NOTE]
   > Vous avez besoin des valeurs de variable `$config` pour suivre la partie suivante des instructions, donc ne fermez pas la fenêtre PowerShell que vous avez utilisée pour suivre les instructions précédentes. Vous pouvez continuer à utiliser la même fenêtre PowerShell ou la laisser ouverte lors du lancement d’une nouvelle session PowerShell.
   
   * Si vous utilisez une clé partagée dans le coffre de clés, exécutez l’applet de commande PowerShell suivante sur le serveur AD FS avec ADFSServiceUrl remplacé par l’URL complète pour atteindre votre service AD FS :

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > Vous avez besoin des propriétés WvdWebAppAppIDUri et RdWebURL pour configurer un environnement dans un cloud souverain comme Azure Government. Dans le cloud commercial Azure, ces propriétés sont automatiquement définies sur `https://www.wvd.microsoft.com` et `https://rdweb.wvd.microsoft.com` respectivement.

   * Si vous utilisez un certificat dans le coffre de clés, exécutez l’applet de commande PowerShell suivante sur le serveur AD FS avec ADFSServiceUrl remplacé par l’URL complète pour atteindre votre service AD FS :

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" -UseCert -CertPath "<Path to the pfx file>" -CertPassword <Password to the pfx file> [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > Vous avez besoin des propriétés WvdWebAppAppIDUri et RdWebURL pour configurer un environnement dans un cloud souverain comme Azure Government. Dans le cloud commercial Azure, ces propriétés sont automatiquement définies sur `https://www.wvd.microsoft.com` et `https://rdweb.wvd.microsoft.com` respectivement.

3. Définissez la stratégie d’accès dans Azure Key Vault en exécutant l’applet de commande PowerShell suivante :

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName "<Key Vault Name>" -ServicePrincipalName 9cdead84-a844-4324-93f2-b2e6bb768d07 -PermissionsToSecrets get -PermissionsToKeys sign
   ```

4. Stockez la clé partagée ou le certificat dans Azure Key Vault avec une étiquette contenant une liste séparée par des virgules d’ID d’abonnement autorisés à utiliser le secret.

   * Si vous utilisez une clé partagée dans le coffre de clés, exécutez l’applet de commande PowerShell suivante pour stocker la clé partagée et définir l’étiquette :

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Set-AzKeyVaultSecret -VaultName "<Key Vault Name>" -Name "adfsssosecret" -SecretValue (ConvertTo-SecureString -String $config.SSOClientSecret  -AsPlainText -Force) -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]}
     ```

   * Si votre certificat se trouve déjà dans le coffre de clés, exécutez l’applet de commande PowerShell suivante pour définir l’étiquette :

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>"
     $secret = Update-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "<Certificate Name>" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -PassThru
     ```

   * Si vous disposez d’un certificat local, exécutez l’applet de commande PowerShell suivante pour importer le certificat dans le coffre de clés et définir l’étiquette :

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Import-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "adfsssosecret" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -FilePath "<Path to pfx>" -Password (ConvertTo-SecureString -String "<pfx password>"  -AsPlainText -Force)
     ```

> [!NOTE]
> Vous pouvez éventuellement configurer la fréquence à laquelle les utilisateurs sont invités à entrer leurs informations d’identification en modifiant les [paramètres d’authentification unique AD FS](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#keep-me-signed-in-for-unauthenticated-devices). Par défaut, les utilisateurs sont invités toutes les 8 heures sur les appareils non inscrits.

## <a name="configure-your-azure-virtual-desktop-host-pool"></a>Configurer votre pool d’hôtes Azure Virtual Desktop

Il est temps de configurer les paramètres d’authentification unique AD FS sur votre pool d’hôtes Azure Virtual Desktop. Pour ce faire, [configurez votre environnement PowerShell](powershell-module.md) pour Azure Virtual Desktop si vous ne vous êtes pas encore connecté à votre compte.

Ensuite, mettez à jour les informations d’authentification unique pour votre pool d’hôtes en exécutant l’une des deux applets de commande suivantes dans la même fenêtre PowerShell sur la machine virtuelle AD FS :

* Si vous utilisez une clé partagée dans Key Vault, exécutez l’applet de commande PowerShell suivante :

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > Vous devez définir la propriété SsoClientId pour qu’elle corresponde au cloud Azure dans lequel vous déployez l’authentification unique. Dans le cloud commercial Azure, cette propriété doit être définie sur `https://www.wvd.microsoft.com`. Toutefois, le paramètre obligatoire pour cette propriété sera différent pour les autres clouds comme le cloud Azure Government.

* Si vous utilisez un certificat dans Key Vault, exécutez l’applet de commande PowerShell suivante :

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType CertificateInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > Vous devez définir la propriété SsoClientId pour qu’elle corresponde au cloud Azure dans lequel vous déployez l’authentification unique. Dans le cloud commercial Azure, cette propriété doit être définie sur `https://www.wvd.microsoft.com`. Toutefois, le paramètre obligatoire pour cette propriété sera différent pour les autres clouds comme le cloud Azure Government.

### <a name="configure-additional-host-pools"></a>Configurer des pools d’hôtes supplémentaires

Lorsque vous devez configurer des pools d’hôtes supplémentaires, vous pouvez récupérer les paramètres que vous avez utilisés pour configurer un pool d’hôtes existant afin d’en configurer un nouveau.

Pour récupérer les paramètres de votre pool d’hôtes existant, ouvrez une fenêtre PowerShell et exécutez cette applet de commande :

```powershell
Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" | fl *
```

Vous pouvez suivre les étapes pour [Configurer votre pool d’hôtes Azure Virtual Desktop](#configure-your-azure-virtual-desktop-host-pool) à l’aide des mêmes valeurs *SsoClientId*, *SsoClientSecretKeyVaultPath*, *SsoSecretType* et *SsoadfsAuthority*.

## <a name="removing-sso"></a>Suppression de l’authentification unique

Pour désactiver l’authentification unique sur le pool d’hôtes, exécutez l’applet de commande suivante :

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority ''
```

Si vous voulez aussi désactiver l’authentification unique sur votre serveur AD FS, exécutez cette applet de commande :

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureWVDSSO.ps1 -WvdWebAppAppIDUri "<WVD Web App URI>" -WvdClientAppApplicationID "a85cf173-4192-42f8-81fa-777a763e6e2c"
```

> [!NOTE]
> La propriété WvdWebAppAppIDUri doit correspondre au cloud Azure dans lequel vous déployez. Dans le cloud commercial Azure, cette propriété est `https://www.wvd.microsoft.com`. Il sera différent pour les autres clouds comme le cloud Azure Government.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré l’authentification unique, vous pouvez vous connecter à un client Azure Virtual Desktop pris en charge pour le tester dans une session utilisateur. Si vous voulez apprendre à vous connecter à une session avec vos nouvelles informations d’identification, consultez les articles suivants :

* [Se connecter avec le client Windows Desktop](./user-documentation/connect-windows-7-10.md)
* [Se connecter avec le client web](./user-documentation/connect-web.md)