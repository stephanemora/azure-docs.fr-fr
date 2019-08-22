---
title: Dépannage d’Azure Site Recovery en cas de problème ou d’erreur de réplication Azure vers Azure | Microsoft Docs
description: Dépannage des erreurs et des problèmes lors de la réplication de machines virtuelles Azure pour la récupération d’urgence
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034789"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Résoudre les problèmes de réplication de machine virtuelle Azure vers Azure

Cet article décrit les problèmes courants dans Azure Site Recovery lors de la réplication et de la récupération de machines virtuelles Azure d’une région vers une autre, et explique comment les résoudre. Pour plus d’informations sur les configurations prises en charge, consultez la [matrice de prise en charge pour la réplication des machines virtuelles Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Liste d’erreurs
- **[Problèmes liés aux quotas de ressources Azure (code d’erreur 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Certificats racines approuvés (code d’erreur 151066)](#trusted-root-certificates-error-code-151066)**
- **[Connectivité sortante pour Site Recovery (code d'erreur 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problèmes liés aux quotas de ressources Azure (code d’erreur 150097)
Votre abonnement doit être activé pour créer des machines virtuelles Azure dans la région cible que vous prévoyez d’utiliser comme région de récupération d’urgence. De plus, votre abonnement doit avoir un quota suffisant activé pour créer des machines virtuelles de taille spécifique. Par défaut, Site Recovery sélectionne pour la machine virtuelle cible la même taille que la machine virtuelle source. Si la taille correspondante n’est pas disponible, la taille la plus proche possible est choisie automatiquement. S’il n’existe pas de taille correspondante qui prend en charge la configuration de la machine virtuelle source, ce message d’erreur s’affiche :

**Code d’erreur** | **Causes possibles** | **Recommandation**
--- | --- | ---
150097<br></br>**Message** : Impossible d’activer la réplication pour la machine virtuelle nom_machine_virtuelle. | - Votre ID d’abonnement n’est peut-être pas activé pour créer des machines virtuelles dans la région cible.</br></br>- Votre ID d’abonnement n’est peut-être pas activé ou ne dispose pas d’un quota suffisant pour créer des tailles de machine virtuelle spécifiques à l’emplacement dans la région cible.</br></br>- Une taille de machine virtuelle cible appropriée qui correspond au nombre de cartes réseau de la machine virtuelle source (2) est introuvable pour l’ID d’abonnement dans la région cible.| Contactez le [support de facturation Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour activer la création de machines virtuelles pour les tailles de machine virtuelle requises à l’emplacement cible pour votre abonnement. Ensuite, réessayez l’opération.

### <a name="fix-the-problem"></a>Résoudre le problème
Vous pouvez contacter le [support de facturation Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pour activer votre abonnement et créer des machines virtuelles de tailles requises à l’emplacement cible.

Si l’emplacement cible a une contrainte de capacité, désactivez la réplication et activez-la à un autre emplacement où votre abonnement a un quota suffisant pour créer des machines virtuelles des tailles requises.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificats racines approuvés (code d’erreur 151066)

Si tous les certificats racines approuvés les plus récents ne sont pas présents sur la machine virtuelle, le travail d’activation de la réplication risque d’échouer. Sans les certificats, l’authentification et l’autorisation des appels du service Site Recovery à partir de la machine virtuelle échouent. Le message d’erreur signalant l’échec du travail d’activation de la réplication Site Recovery s’affiche :

**Code d’erreur** | **Cause possible** | **Recommandations**
--- | --- | ---
151066<br></br>**Message** : Échec de la configuration de Site Recovery. | Les certificats racines approuvés nécessaires pour l’autorisation et l’authentification ne sont pas présents sur la machine. | - Pour une machine virtuelle exécutant le système d’exploitation Windows, vérifiez que les certificats racines approuvés sont présents sur la machine. Pour plus d’informations, consultez [Configurer des racines de confiance et des certificats non autorisés](https://technet.microsoft.com/library/dn265983.aspx).<br></br>- Pour une machine virtuelle exécutant le système d’exploitation Linux, suivez les instructions relatives aux certificats racines approuvés publiées par le distributeur de la version du système d’exploitation Linux.

### <a name="fix-the-problem"></a>Résoudre le problème
**Windows**

Installez toutes les mises à jour de Windows les plus récentes sur la machine virtuelle pour que tous les certificats racines approuvés soient présents sur la machine. Si vous êtes dans un environnement déconnecté, suivez le processus de mise à jour de Windows standard en vigueur dans votre organisation pour obtenir les certificats. Si les certificats nécessaires ne sont pas présents sur la machine virtuelle, les appels au service Site Recovery échouent pour des raisons de sécurité.

Suivez le processus classique de gestion des mises à jour de Windows ou de gestion des mises à jour de certificats en vigueur dans votre organisation pour obtenir tous les certificats racines les plus récents et la liste de révocation de certificats mise à jour sur les machines virtuelles.

Pour vérifier que le problème est résolu, accédez à login.microsoftonline.com à partir d’un navigateur sur votre machine virtuelle.

**Linux**

Suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.

SuSE Linux utilisant des liens symboliques pour tenir à jour une liste de certificats, vous devez effectuer les étapes suivantes :

1.  Connectez-vous en tant qu’utilisateur racine.

2.  Exécutez cette commande pour modifier le répertoire.

      ``# cd /etc/ssl/certs``

1. Vérifiez si le certificat d’autorité de certification racine Symantec est présent.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Si le certificat d’autorité de certification racine Symantec est introuvable, exécutez la commande suivante pour télécharger le fichier. Vérifiez les erreurs et suivez l’action recommandée pour les défaillances du réseau.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Vérifiez si le certificat d’autorité de certification racine Baltimore est présent.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Si le certificat d’autorité de certification racine Baltimore est introuvable, téléchargez le certificat.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Vérifiez si le certificat DigiCert_Global_Root_CA est présent.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Si le certificat DigiCert_Global_Root_CA est introuvable, exécutez les commandes suivantes pour télécharger le certificat.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Exécutez le script rehash pour mettre à jour les hachages de sujet des certificats qui viennent d’être téléchargés.

    ``# c_rehash``

8.  Vérifiez si les hachages de sujet en tant que liens symboliques sont créés pour les certificats.

    - Commande

      ``# ls -l | grep Baltimore``

    - Output

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Commande

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Commande

      ``# ls -l | grep DigiCert_Global_Root``

    - Output

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Créez une copie du fichier VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem et donnez-lui le nom b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Créez une copie du fichier Baltimore_CyberTrust_Root.pem et donnez-lui le nom 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Créez une copie du fichier DigiCert_Global_Root_CA.pem et donnez-lui le nom 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Vérifiez si les fichiers sont présents.  

    - Commande

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Output

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connectivité sortante pour les plages d’adresses IP ou les URL Site Recovery (code d’erreur 151037 ou 151072)

Pour que la réplication Site Recovery fonctionne, une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques est nécessaire à partir de la machine virtuelle. Si votre machine virtuelle se trouve derrière un pare-feu ou utilise des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, vous pouvez rencontrer l’un des problèmes ci-après.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problème 1 : Échec de l’inscription de la machine virtuelle Azure auprès de Site Recovery (151195) </br>
- **Cause possible** </br>
  - Il est impossible d’établir la connexion aux points de terminaison de Site Recovery en raison de l’échec de la résolution DNS.
  - Cela est plus fréquent pendant la reprotection lorsque vous avez procédé au basculement de la machine virtuelle, mais que le serveur DNS n’est pas accessible à partir de la région de récupération d’urgence.

- **Résolution :**
   - Si vous utilisez un système DNS personnalisé, assurez-vous que le serveur DNS est accessible à partir de la région de récupération d’urgence. Pour vérifier si vous avez un système DNS personnalisé accédez à Machine virtuelle > Réseau de récupération d’urgence > Serveurs DNS. Essayez d’accéder au serveur DNS à partir de la machine virtuelle. S’il n’est pas accessible, rendez-le accessible en basculant sur le serveur DNS ou en créant la ligne du site entre le réseau de récupération d’urgence et le système DNS.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problème2 : Échec de la configuration de Site Recovery (151196)
- **Cause possible** </br>
  - Impossible d’établir la connexion aux points de terminaison IP4 d’identité et d’authentification Office 365.

- **Résolution :**
  - Azure Site Recovery exigeait l’accès aux plages d’adresses IP d’Office 365 pour l’authentification.
    Si vous utilisez un proxy de règles/pare-feu de groupe de sécurité réseau Azure pour contrôler la connectivité réseau sortante sur la machine virtuelle, assurez-vous d’autoriser la communication avec les plages IP Office 365. Créer une règle de groupe de sécurité réseau basée sur une [balise de service Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) pour autoriser l’accès à toutes les adresses IP correspondant à AAD
      - Si de nouvelles adresses sont ajoutées ultérieurement à Azure Active Directory (AAD), vous devez créer de nouvelles règles de groupe de sécurité réseau.

> [!NOTE]
> Si les machines virtuelles se trouvent derrière un équilibreur de charge interne **Standard**, elles ne peuvent pas accéder aux IP O365, par exemple. login.microsoftonline.com par défaut. Changez-le en un type d’équilibreur de charge **De base** ou créez un accès hors limite comme mentionné dans cet [article](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problème 3 : Échec de la configuration de Site Recovery (151197)
- **Cause possible** </br>
  - Il est impossible d’établir la connexion aux points de terminaison de service Azure Site Recovery.

- **Résolution :**
  - Azure Site Recovery exigeait l’accès aux [plages d’adresses IP Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) selon la région. Assurez-vous que les plages d’adresses IP requises sont accessibles à partir de la machine virtuelle.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problème 4 : Échec de la réplication interapplication lorsque le trafic réseau transite par un serveur proxy local (151072)
- **Cause possible** </br>
  - Les paramètres de proxy personnalisés sont incorrects, et l’agent du Mobility Service Azure Site Recovery n’a pas détecté automatiquement les paramètres de proxy à partir d’Internet Explorer


- **Résolution :**
  1. L’agent du service Mobilité détecte les paramètres de proxy à partir d’Internet Explorer sur Windows et à l’emplacement /etc/environment sur Linux.
  2. Si vous préférez définir un proxy uniquement pour le Mobility Service Azure Site Recovery, vous pouvez fournir les détails du proxy dans le fichier ProxyInfo.conf situé aux emplacements suivants :</br>
     - ``/usr/local/InMage/config/`` sur ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` sur ***Windows***
  3. Le fichier ProxyInfo.conf doit inclure les paramètres de proxy au format INI suivant.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. L’agent du Mobility Service Azure Site Recovery prend uniquement en charge les ***proxies non authentifiés***.


### <a name="fix-the-problem"></a>Résoudre le problème
Pour autoriser les [URL requises](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou les [plages d’adresses IP requises](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), suivez les étapes fournies dans ce [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disque introuvable sur la machine (code d’erreur 150039)

Un nouveau disque attaché à la machine virtuelle doit être initialisé.

**Code d’erreur** | **Causes possibles** | **Recommandations**
--- | --- | ---
150039<br></br>**Message** : Le disque de données Azure (nom_disque) (URI_disque) portant le numéro d’unité logique (LUN) (valeur_LUN) n’a pas été mappé au disque correspondant signalé à partir de la machine virtuelle ayant la même valeur LUN. | - Un nouveau disque de données a été attaché à la machine virtuelle, mais il n’a pas été initialisé.</br></br>- Le disque de données à l’intérieur de la machine virtuelle ne signale pas correctement la valeur de numéro d’unité logique (LUN) à laquelle le disque a été attaché à la machine virtuelle.| Vérifiez que les disques de données sont initialisés, puis réessayez l’opération.</br></br>Pour Windows : [Attacher et initialiser un nouveau disque](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Pour Linux : [Initialiser un nouveau disque de données sous Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Résoudre le problème
Vérifiez que les disques de données ont été initialisés, puis réessayez l’opération :

- Pour Windows : [Attacher et initialiser un nouveau disque](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Pour Linux : [Ajouter un nouveau disque de données dans Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Si le problème persiste, contactez le support technique.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Un ou plusieurs disques sont disponibles pour protection (code d'erreur 153039)
- **Cause possible** </br>
  - si un ou plusieurs disques ont récemment été ajoutés à la machine après la protection. 
  - si un ou plusieurs disques ont été initialisés plus tard après la protection de la machine virtuelle.

### <a name="fix-the-problem"></a>Résoudre le problème
Vous pouvez choisir de protéger les disques ou d’ignorer l’avertissement pour rendre à la machine virtuelle un statut de réplication sain.</br>
1. Pour protéger le(s) disque(s). Accédez à Éléments répliqués > Machine virtuelle > Disques > cliquez sur le disque non protégé > Activer la réplication.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Pour ignorer l’avertissement. Accédez à Éléments répliqués > Machine virtuelle > Cliquez sur Ignorer l’alerte dans la section Vue d’ensemble.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Supprimer la machine virtuelle du coffre complétée avec des informations (code d’erreur 150225)
Au moment de la protection de la machine virtuelle, Azure Site Recovery crée des liens sur la machine virtuelle source. Lorsque vous supprimez la protection ou désactivez la réplication, Azure Site Recovery supprime ces liens dans le cadre de travail de nettoyage. Dans le cas où la machine virtuelle dispose d’un verrouillage des ressources, le travail est terminé avec les informations. Ces informations indiquent que la machine virtuelle a été supprimée de l’espace de stockage des services de récupération, mais que certains liens obsolètes n’ont pas pu être nettoyés de la machine source.

Vous pouvez ignorer cet avertissement si vous n’envisagez pas de protéger cette machine virtuelle à nouveau dans le futur. Cependant, si vous devez protéger cette machine virtuelle plus tard, vous devez nettoyer les liens comme indiqué dans les étapes ci-dessous. 

**Si vous ne faites pas le nettoyage :**

1.  Pendant la période d’activation de la réplication via le coffre des services de restauration, la machine virtuelle ne sera pas répertoriée. 
2.  Si vous essayez de protéger la machine virtuelle via **Machine virtuelle>Paramètres> Récupération d’urgence**, cela échouera avec le message d’erreur « *La réplication ne peut être activée en raison des liens de ressources obsolètes existants sur la machine virtuelle* ».


### <a name="fix-the-problem"></a>Résoudre le problème

>[!NOTE]
>
>Azure Site Recovery ne supprime pas la machine virtuelle source et n’a aucun impact sur elle lors de l’exécution des étapes suivantes.
>

1. Supprimez le verrou de la machine virtuelle ou du groupe de ressources. Par exemple :  Au-dessous du nom de la machine virtuelle « MoveDemo » se trouve le verrouillage des ressources qui doit être supprimé.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Téléchargez le script [Retirer la configuration de Azure Site Recovery obsolète](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Exécutez le script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Indiquez l’ID d’abonnement, le groupe de ressources de la machine virtuelle et le nom de la machine virtuelle en tant que paramètre.
5. Si on vous demande vos identifiants Azure, veuillez les fournir et vérifier que le script est exécuté sans aucun échec. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>La réplication ne peut pas être activée en raison des liens de ressources obsolètes existants sur la machine virtuelle (code d’erreur 150226)

**Cause : La configuration de la machine virtuelle est obsolète par rapport à la protection précédente de récupération de site**

Une configuration obsolète peut être conservée sur une machine virtuelle Azure dans les cas suivants :

- Vous avez activé la réplication pour la machine virtuelle Azure en utilisant Site Recovery puis désactivé la réplication, mais la **machine virtuelle source avait un verrouillage des ressources**.
- Vous avez activé la réplication pour la machine virtuelle Azure à l’aide de Site Recovery, puis vous avez supprimé le coffre Site Recovery sans désactiver explicitement la réplication sur la machine virtuelle.
- Vous avez activé la réplication pour la machine virtuelle Azure à l’aide de Site Recovery, puis vous avez supprimé le groupe de ressources contenant le coffre Site Recovery sans désactiver explicitement la réplication sur la machine virtuelle.

### <a name="fix-the-problem"></a>Résoudre le problème

>[!NOTE]
>
>Azure Site Recovery ne supprime pas la machine virtuelle source et n’a aucun impact sur elle lors de l’exécution des étapes suivantes.


1. Supprimez le verrou de la machine virtuelle ou du groupe de ressources, s’il y en a un. *Par exemple :* Au-dessous du nom de la machine virtuelle « MoveDemo » se trouve le verrouillage des ressources qui doit être supprimé.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Téléchargez le script [Retirer la configuration de Azure Site Recovery obsolète](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Exécutez le script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Indiquez l’ID d’abonnement, le groupe de ressources de la machine virtuelle et le nom de la machine virtuelle en tant que paramètre.
5. Si on vous demande vos identifiants Azure, veuillez les fournir et vérifier que le script est exécuté sans aucun échec.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Impossible de sélectionner la machine virtuelle ou le groupe de ressources Azure dans « Activer la réplication »

 **Cause 1 :  le groupe de ressources et la machine virtuelle source se trouvent à un emplacement différent**
 
Azure Site Recovery exige actuellement que le groupe de ressources et les machines virtuelles de la région source se trouvent dans le même emplacement. Si tel n’est pas le cas, vous ne pouvez pas trouver la machine virtuelle ou le groupe de ressources pendant la durée de la protection. 

**Comme solution de contournement**, vous pouvez Activer la réplication depuis la machine virtuelle à la place du coffre Recovery Services. Accédez à Machine virtuelle source > Propriétés > Récupération d’urgence et Activer la réplication.

**Cause 2 : le groupe de ressources ne fait pas partie de l’abonnement sélectionné**

Il se peut que vous ne puissiez pas trouver le groupe de ressources au moment de la protection s’il ne fait pas partie de l’abonnement donné. Assurez-vous que le groupe de ressources appartient à l’abonnement en cours d’utilisation.

 **Cause 3 : configuration obsolète**
 
Si vous ne voyez pas la machine virtuelle que vous souhaitez activer pour la réplication, cela peut-être dû à une configuration de récupération de site obsolète conservée sur la machine virtuelle Azure. Une configuration obsolète peut être conservée sur une machine virtuelle Azure dans les cas suivants :

- Vous avez activé la réplication pour la machine virtuelle Azure à l’aide de Site Recovery, puis vous avez supprimé le coffre Site Recovery sans désactiver explicitement la réplication sur la machine virtuelle.
- Vous avez activé la réplication pour la machine virtuelle Azure à l’aide de Site Recovery, puis vous avez supprimé le groupe de ressources contenant le coffre Site Recovery sans désactiver explicitement la réplication sur la machine virtuelle.

- Vous avez activé la réplication pour la machine virtuelle Azure en utilisant Site Recovery puis désactivé la réplication, mais la machine virtuelle source avait un verrouillage des ressources.

### <a name="fix-the-problem"></a>Résoudre le problème

> [!NOTE]
>
> Veillez à mettre à jour le module AzureRM.Resources avant d’utiliser le script ci-dessous. Azure Site Recovery ne supprime pas la machine virtuelle source et n’a aucun impact sur elle lors de l’exécution des étapes suivantes.
>

1. Supprimez le verrou de la machine virtuelle ou du groupe de ressources, s’il y en a un. *Par exemple :* Au-dessous du nom de la machine virtuelle « MoveDemo » se trouve le verrouillage des ressources qui doit être supprimé.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Télécharger le script [Supprimer la configuration périmée](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Exécutez le script *Cleanup-stale-asr-config-Azure-VM.ps1*.
4. Indiquez l’ID d’abonnement, le groupe de ressources de la machine virtuelle et le nom de la machine virtuelle en tant que paramètre.
5. Si on vous demande vos identifiants Azure, veuillez les fournir et vérifier que le script est exécuté sans aucun échec.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Impossible de sélectionner la machine virtuelle pour la protection
 **Cause 1 :  la machine virtuelle comporte une extension en échec ou qui ne répond pas** <br>
 Accédez à Machines virtuelles > Paramètre > Extensions, et vérifiez s’il existe des extensions en état d’échec. Désinstallez l’extension en état d’échec, puis réessayez de protéger la machine virtuelle.<br>
 **Cause 2 :  [l’état d’approvisionnement de la machine virtuelle n’est pas valide](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>L’état de provisionnement de la machine virtuelle n’est pas valide (code d’erreur 150019)

Pour activer la réplication sur la machine virtuelle, l’état de provisionnement doit être **Réussite**. Vous pouvez vérifier l’état de la machine virtuelle en suivant les étapes ci-dessous.

1.  Sélectionnez **l’Explorateur de ressources** sous **Tous les services** dans le portail Azure.
2.  Développez la liste **Abonnements** et sélectionnez votre abonnement.
3.  Développez la liste **Groupes de ressources** et sélectionnez le groupe de ressources de la machine virtuelle.
4.  Développez la liste **Ressources** et sélectionnez votre machine virtuelle
5.  Vérifiez le champ **provisioningState** dans la vue Instance à droite.

### <a name="fix-the-problem"></a>Résoudre le problème

- Si **provisioningState** a la valeur **Échec**, contactez le support avec les détails pour résoudre les problèmes.
- Si **provisioningState** a la valeur **Mise à jour**, une autre extension est peut-être en cours de déploiement. Vérifiez si des opérations sont en cours sur la machine virtuelle, attendez qu’elles se terminent et réessayez la tâche Site Recovery **Activer la réplication** qui a échoué.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Impossible de sélectionner le réseau virtuel cible - Onglet de sélection de réseau grisé

**Cause 1 : votre machine virtuelle est attachée à un réseau déjà mappé sur un « réseau cible »**
- Si la machine virtuelle source fait partie d’un réseau virtuel et qu’une autre machine virtuelle du même réseau virtuel est déjà mappée sur un réseau dans le groupe de ressources cible, la liste déroulante de sélection de réseau est désactivée par défaut.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Cause 2 : vous avez déjà protégé la machine virtuelle avec Azure Site Recovery et désactivé la réplication**
 - La désactivation de la réplication d’une machine virtuelle ne supprime pas le mappage réseau. Ce dernier doit être supprimé du coffre Recovery Services dans lequel la machine virtuelle a été protégée. </br>
 Accédez à Coffre Recovery Services > Infrastructure Site Recovery > Mappage réseau. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Le réseau cible configuré lors de la configuration de la récupération d’urgence est modifiable après la configuration initiale, une fois que la machine virtuelle est protégée. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Notez que la modification du mappage réseau affecte toutes les machines virtuelles protégées qui utilisent ce mappage réseau spécifique.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>Erreur du service COM+ / Cliché instantané de volume (code d’erreur 151025)

**Code d’erreur** | **Causes possibles** | **Recommandations**
--- | --- | ---
151025<br></br>**Message** : Échec de l’installation de l’extension de récupération de site | - Le service « Application système COM+ » est désactivé.</br></br>- Le service « Cliché instantané de volume » est désactivé.| Définissez les services « Application système COM+ » et « Cliché instantané de volume » en mode de démarrage manuel ou automatique.

### <a name="fix-the-problem"></a>Résoudre le problème

Vous pouvez ouvrir la console « Services » et vérifier que « Application système COM+ » et « Cliché instantané de volume » ne sont pas définis sur « Désactivé » pour le « Type de démarrage ».
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Taille de disque managé non prise en charge (code d’erreur 150172)


**Code d’erreur** | **Causes possibles** | **Recommandations**
--- | --- | ---
150172<br></br>**Message** : Impossible d’activer la protection pour la machine virtuelle, car elle a un disque (nom_disque) de taille (taille_disque) inférieure à la valeur minimale prise en charge de 1024 Mo. | - Le disque a une taille inférieure à la taille prise en charge de 1 024 Mo| Vérifiez que les tailles de disque sont dans la plage des tailles prises en charge et réessayez l’opération.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>L'activation de la protection a échoué car le nom de l'appareil mentionné dans la configuration GRUB n'est pas l'UUID (code d'erreur 151126)

**Cause possible :** </br>
Les fichiers de configuration GRUB (« /boot/grub/menu.lst », « /boot/grub/grub.cfg », « /boot/grub2/grub.cfg » ou « /etc/default/grub ») peuvent contenir la valeur des paramètres **root** et **resume** en tant que noms d'appareils en lieu et place de l'UUID. Site Recovery impose l'approche UUID car le nom de l'appareil peut changer lors du redémarrage de la machine virtuelle. Et si la machine virtuelle n'apparaît pas sous le même nom lors du basculement, des problèmes peuvent survenir. Par exemple : </br>


- La ligne suivante provient du fichier GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- La ligne suivante provient du fichier GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Si vous observez la chaîne en gras ci-dessus, cela signifie que GRUB contient les valeurs des paramètres « root » et « resume » en tant que noms d'appareils en lieu et place de l'UUID.

**Procédure de résolution :**<br>
Le nom de chaque appareil doit être remplacé par l'UUID correspondante.<br>


1. Recherchez l’UUID de l’appareil en exécutant la commande « blkid \<nom_appareil> ». Par exemple :<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. Remplacez maintenant le nom de l’appareil par son UUID dans un format du type « root=UUID=\<UUID> ». Par exemple, si l’on remplace le nom des appareils par l’UUID pour les paramètres root et resume mentionnés plus haut dans les fichiers « /boot/grub2/grub.cfg », « /boot/grub2/grub.cfg » or « /etc/default/grub », les lignes des fichiers se présentent ainsi : <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Redémarrez à nouveau la protection.

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>L'activation de la protection a échoué car le nom de l'appareil mentionné dans la configuration GRUB n’existe pas (code d'erreur 151124).
**Cause possible :** </br>
Les fichiers de configuration GRUB (« /boot/grub/menu.lst », « /boot/grub/grub.cfg », « /boot/grub2/grub.cfg » ou « /etc/default/grub ») peuvent contenir les paramètres « rd.lvm.lv » ou « rd_LVM_LV » pour indiquer l’appareil LVM qui doit être découvert au démarrage. Si ces appareils LVM n’existent pas, le système protégé proprement dit ne démarrera pas et ne sera pas bloqué lors du processus de démarrage. Ceci est également valable pour la machine virtuelle de basculement. Quelques exemples ci-dessous :

Quelques exemples : </br>

1. La ligne suivante provient du fichier GRUB **« /boot/grub2/grub.cfg »** sur RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   La partie en surbrillance montre que GRUB doit détecter deux périphériques LVM appelés **« root »** et **« swap »** du groupe de volumes « rootvg ».
1. La ligne suivante provient du fichier GRUB **« /etc/default/grub »** sur RHEL7. </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   La partie en surbrillance montre que GRUB doit détecter deux périphériques LVM appelés **« root »** et **« swap »** du groupe de volumes « rootvg ».
1. La ligne suivante provient du fichier GRUB **« /boot/grub/menu.lst »** sur RHEL6. </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   La partie en surbrillance montre que GRUB doit détecter deux périphériques LVM appelés **« root »** et **« swap »** du groupe de volumes « rootvg ».<br>

**Procédure de résolution :**<br>

Si l’appareil LVM n’existe pas, corrigez en le créant ou supprimez le paramètre de cet appareil des fichiers de configuration GRUB, puis essayez à nouveau d’activer la protection. </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>La mise à jour du service de mobilité Site Recovery a retourné des avertissements (code d’erreur 151083)
Le service Mobilité Site Recovery a de nombreux composants, et notamment le pilote de filtre. Le pilote de filtre ne se charge dans la mémoire système qu’au moment du redémarrage du système. À chaque fois que des mises à jour du service de mobilité Site Recovery entraînent des modifications du pilote de filtre, nous mettons à jour l’ordinateur tout en vous avertissant que certains correctifs nécessitent un redémarrage. Cela signifie que seul le chargement d’un nouveau pilote de filtre, qui se produit seulement lors du redémarrage système, permet d’apporter des correctifs au pilote de filtre.<br>
**Veuillez noter** qu’il s’agit d’un simple avertissement et que la réplication existante fonctionne toujours après la nouvelle mise à jour de l’agent. Vous pouvez choisir de redémarrer chaque fois que vous souhaitez profiter des avantages du nouveau pilote de filtre ; en l’absence de redémarrage, l’ancien pilote de filtre continue également de fonctionner. Hormis le pilote de filtre, **les autres améliorations et correctifs du service de mobilité prennent effet sans redémarrage lorsque l’agent est mis à jour.** .  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Impossible d’activer la protection, car le disque managé de réplica « diskname-Replica » existe déjà sans les balises attendues dans le groupe de ressources cible (code d’erreur 150161

**Cause** : Cela peut se produire si l’ordinateur virtuel a été protégé précédemment et que, lors de la désactivation de la réplication, le disque de réplica n’a pas été nettoyé pour une raison quelconque.</br>
**Procédure de résolution :** Supprimez le disque de réplica mentionné dans le message d’erreur et redémarrez la tâche de protection qui a échoué.

## <a name="next-steps"></a>Étapes suivantes
[Répliquer des machines virtuelles Azure](site-recovery-replicate-azure-to-azure.md)
