---
title: Résoudre les problèmes de récupération d’urgence de connectivité pour Azure avec Azure Site Recovery
description: Résoudre les problèmes de connectivité dans le cadre de la reprise d’activité d’une machine virtuelle Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292023"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Résoudre les problèmes de connectivité du réseau de machines virtuelles Azure vers Azure

Cet article décrit les problèmes courants liés à la connectivité réseau lorsque vous répliquez et récupérez des machines virtuelles Azure d’une région à une autre. Pour plus d’informations sur la configuration requise pour la mise en réseau, consultez la page [Configuration de connectivité pour la réplication des machines virtuelles Azure](azure-to-azure-about-networking.md).

Pour que la réplication Site Recovery fonctionne, une connectivité sortante vers des URL ou des plages d’adresses IP spécifiques est nécessaire à partir de la machine virtuelle. Si votre machine virtuelle se trouve derrière un pare-feu ou utilise des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, vous pouvez rencontrer l’un des problèmes ci-après.

**URL** | **Détails**  
--- | ---
*.blob.core.windows.net | Nécessaire pour que les données puissent être écrites dans le compte de stockage de cache dans la région source à partir de la machine virtuelle. Si vous connaissez tous les comptes de stockage de cache pour vos machines virtuelles, vous pouvez autoriser-répertorier les URL de compte de stockage spécifiques (par exemple, cache1.blob.core.windows.net et cache2.blob.core.windows.net) au lieu de *.blob.core.windows.net
login.microsoftonline.com | Nécessaire pour l’autorisation et l’authentification aux URL du service Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Nécessaire pour que la communication du service Site Recovery puisse avoir lieu à partir de la machine virtuelle. Vous pouvez utiliser l’« Adresse IP de Site Recovery » correspondante si votre proxy de pare-feu prend en charge les adresses IP.
*.servicebus.windows.net | Nécessaire pour que les données de surveillance et de diagnostic Site Recovery puissent être écrites à partir de la machine virtuelle. Vous pouvez utiliser l’« Adresse IP de supervision de Site Recovery » correspondante si votre proxy de pare-feu prend en charge les adresses IP.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connectivité sortante pour les plages d’adresses IP ou les URL Site Recovery (code d’erreur 151037 ou 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problème 1 : Échec de l’inscription de la machine virtuelle Azure auprès de Site Recovery (151195) </br>
- **Cause possible** </br>
  - Il est impossible d’établir la connexion aux points de terminaison de Site Recovery en raison de l’échec de la résolution DNS.
  - Cela est plus fréquent pendant la reprotection lorsque vous avez procédé au basculement de la machine virtuelle, mais que le serveur DNS n’est pas accessible à partir de la région de récupération d’urgence.

- **Résolution :**
   - Si vous utilisez DNS personnalisé, assurez-vous que le serveur DNS est accessible à partir de la région de récupération d’urgence. Pour vérifier si vous avez un système DNS personnalisé accédez à Machine virtuelle > Réseau de récupération d’urgence > Serveurs DNS. Essayez d’accéder au serveur DNS à partir de la machine virtuelle. S’il n’est pas accessible, rendez-le accessible en faisant basculer le serveur DNS ou en créant la ligne de site entre le réseau de récupération d’urgence et le DNS.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problème2 : Échec de la configuration de Site Recovery (151196)

> [!NOTE]
> Si les machines virtuelles se trouvent derrière un équilibreur de charge interne **standard**, elles n’ont pas accès aux adresses IP O365 (autrement dit, login.microsoftonline.com) par défaut. Changez-le en un type d’équilibreur de charge **De base** ou créez un accès hors limite comme mentionné dans cet [article](https://aka.ms/lboutboundrulescli).

- **Cause possible** </br>
  - Impossible d’établir la connexion aux points de terminaison IP4 d’identité et d’authentification Office 365.

- **Résolution :**
  - Azure Site Recovery exigeait l’accès aux plages d’adresses IP d’Office 365 pour l’authentification.
    Si vous utilisez un proxy de règles/pare-feu de groupe de sécurité réseau Azure pour contrôler la connectivité réseau sortante sur la machine virtuelle, assurez-vous d’autoriser la communication avec les plages IP Office 365. Créer une règle de groupe de sécurité réseau [basée sur une balise de service Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags) pour autoriser l’accès à toutes les adresses IP correspondant à Azure AD
      - Si de nouvelles adresses sont ajoutées à Azure AD à l’avenir, vous devez créer des règles de groupe de sécurité réseau.

### <a name="example-nsg-configuration"></a>Exemple de configuration de groupe de sécurité réseau

Cet exemple montre comment configurer des règles de groupes de sécurité réseau pour une machine virtuelle à répliquer.

- Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, utilisez des règles « Autoriser le trafic HTTPS sortant » sur port:443 pour toutes les plages d’adresses IP requises.
- L’exemple part du principe que « USA Est » est l’emplacement source de la machine virtuelle, et que « USA Centre » en est l’emplacement cible.

### <a name="nsg-rules---east-us"></a>Règles de groupe de sécurité réseau - USA Est

1. Créez une règle de sécurité HTTPS sortante (443) pour « Storage.EastUS » sur le groupe de sécurité réseau comme indiqué dans la capture d’écran ci-dessous.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Créez une règle de sécurité HTTPS sortante (443) pour « AzureActiveDirectory » sur le groupe de sécurité réseau comme indiqué dans la capture d’écran ci-dessous.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Créez des règles HTTPS sortantes (443) pour les adresses IP Site Recovery qui correspondent à l’emplacement cible :

   **Lieu** | **Adresse IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   USA Centre | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Règles de groupe de sécurité réseau - USA Centre

Ces règles sont nécessaires pour que la réplication puisse être activée de la région cible vers la région source après le basculement :

1. Créez une règle de sécurité HTTPS sortante (443) pour « Storage.CentralUS » sur le groupe de sécurité réseau.

2. Créez une règle de sécurité HTTPS sortante (443) pour « AzureActiveDirectory » sur le groupe de sécurité réseau.

3. Créez des règles HTTPS sortantes (443) pour les adresses IP Site Recovery qui correspondent à l’emplacement source :

   **Lieu** | **Adresse IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   USA Centre | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problème 3 : Échec de la configuration de Site Recovery (151197)
- **Cause possible** </br>
  - Il est impossible d’établir la connexion aux points de terminaison de service Azure Site Recovery.

- **Résolution :**
  - Azure Site Recovery exigeait l’accès aux [plages d’adresses IP Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags) selon la région. Assurez-vous que les plages d’adresses IP requises sont accessibles à partir de la machine virtuelle.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problème 4 : Échec de la réplication interapplication lorsque le trafic réseau transite par un serveur proxy local (151072)
- **Cause possible** </br>
  - Les paramètres de proxy personnalisés sont incorrects, et l’agent du Mobility Service Azure Site Recovery n’a pas détecté automatiquement les paramètres de proxy à partir d’Internet Explorer


- **Résolution :**
  1. L’agent du service Mobilité détecte les paramètres de proxy à partir d’Internet Explorer sur Windows et à l’emplacement /etc/environment sur Linux.
  2. Si vous préférez définir le proxy uniquement pour Azure Site Recovery Mobility Service, vous pouvez fournir les détails du proxy dans ProxyInfo.conf à l’emplacement suivant :</br>
     - ``/usr/local/InMage/config/`` sur ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` sur ***Windows***
  3. Le fichier ProxyInfo.conf doit inclure les paramètres de proxy au format INI suivant.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. L’agent du Mobility Service Azure Site Recovery prend uniquement en charge les ***proxies non authentifiés***.

### <a name="fix-the-problem"></a>Résoudre le problème
Pour autoriser les [URL requises](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) ou les [plages d’adresses IP requises](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), suivez les étapes fournies dans ce [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Étapes suivantes
[Répliquer des machines virtuelles Azure](site-recovery-replicate-azure-to-azure.md)
