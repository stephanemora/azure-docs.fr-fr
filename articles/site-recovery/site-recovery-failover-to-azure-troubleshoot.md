---
title: Résoudre les problèmes d’échec de basculement vers Azure | Microsoft Docs
description: Cet article explique comment résoudre les erreurs courantes qui surviennent lors d’un basculement vers Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: b7b5dcd88b6e4e09dd9beb21e83ef405df148115
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443382"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Résoudre les erreurs se produisant lors du basculement d’une machine virtuelle vers Azure

Vous pouvez recevoir les erreurs suivantes lorsque vous procédez au basculement d’une machine virtuelle vers Azure. Pour résoudre les problèmes, servez-vous des étapes décrites pour chaque condition d’erreur.

## <a name="failover-failed-with-error-id-28031"></a>Le basculement a échoué avec l’ID d’erreur 28031

Site Recovery n’a pas pu créer de machine virtuelle basculée dans Azure. L’une des raisons suivantes peut en être la cause :

* Le quota disponible n’est pas suffisant pour créer la machine virtuelle : vous pouvez vérifier le quota disponible en accédant à Abonnement -> Utilisation + quotas. Vous pouvez ouvrir une [nouvelle demande de support](http://aka.ms/getazuresupport) pour augmenter le quota.

* Vous tentez de basculer des machines virtuelles de familles de taille différentes dans le même groupe à haute disponibilité. Veillez à choisir le même ordre de grandeur de taille pour toutes les machines virtuelles d’un même groupe à haute disponibilité. Changez la taille en accédant aux paramètres Calcul et réseau de la machine virtuelle, puis réessayez le basculement.

* Il existe une stratégie sur l’abonnement qui empêche la création d’une machine virtuelle. Modifiez la stratégie pour permettre la création d’une machine virtuelle, puis réessayez le basculement.

## <a name="failover-failed-with-error-id-28092"></a>Le basculement a échoué avec l’ID d’erreur 28092

Site Recovery n’a pas pu créer d’interface réseau pour la machine virtuelle basculée. Assurez-vous que vous avez un quota suffisant pour créer des interfaces réseau dans l’abonnement. Vous pouvez vérifier le quota disponible en accédant à Abonnement -> Utilisation + quotas. Vous pouvez ouvrir une [nouvelle demande de support](http://aka.ms/getazuresupport) pour augmenter le quota. Si vous disposez d’un quota suffisant, le problème peut être occasionnel, recommencez l’opération. Si le problème persiste même après plusieurs tentatives, laissez un commentaire à la fin de ce document.  

## <a name="failover-failed-with-error-id-70038"></a>Le basculement a échoué avec l’ID d’erreur 70038

Site Recovery n’a pas pu créer de machine virtuelle classique basculée dans Azure. Les raisons suivantes peuvent en être la cause :

* Une des ressources, comme un réseau virtuel, nécessaire à la création de la machine virtuelle n’existe pas. Créez le réseau virtuel, tel que proposé sous les paramètres Calcul et réseau de la machine virtuelle, ou modifiez le paramètre sur un réseau virtuel qui existe déjà et réessayez le basculement.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Impossible de se connecter ou d’établir une liaison RDP ou SSH à la machine virtuelle après son basculement, car le bouton Se connecter est grisé

Si le bouton Se connecter est grisé et que vous n’avez pas établi de connexion ExpressRoute ou réseau privé virtuel de site à site à Azure :

1. Accédez à **Machine virtuelle** > **Réseaux**, cliquez sur le nom de l’interface réseau concernée.  ![interface-réseau](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
1. Accédez à **Configurations d’adresses IP**, puis cliquez sur le champ Nom de la configuration d’adresse IP souhaitée. ![Configurations d’adresses IP](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
1. Pour activer l’adresse IP publique, cliquez sur **Activer**. ![Activer l’adresse IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
1. Cliquez sur **Configurer les paramètres requis** > **Créer**. ![Créer](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
1. Entrez le nom de l’adresse publique, choisissez les options par défaut pour **SKU** et **Affectation**, puis cliquez sur **OK**.
1. Pour enregistrer les modifications, cliquez sur **Enregistrer**.
1. Fermez les panneaux et accédez à la section **Vue d’ensemble** de la machine virtuelle pour vous connecter/établir une liaison RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Impossible de se connecter ou d’établir une liaison RDP ou SSH à la machine virtuelle après son basculement alors que le bouton Se connecter est disponible (non grisé)

Ouvrez **Diagnostics de démarrage** sur votre machine virtuelle pour voir s’il s’est produit certaines des erreurs listées dans cet article.

1. Si la machine virtuelle n’a pas démarré, tentez de basculer vers un point de récupération plus ancien.
1. Si l’application de la machine virtuelle n’est pas disponible, tentez de basculer vers un point de récupération cohérent au niveau application.
1. Si la machine virtuelle est jointe au domaine, vérifiez que le contrôleur de domaine fonctionne bien. Pour cela, vous pouvez suivre les étapes ci-dessous.
    a. Créez une machine virtuelle dans le même réseau.

    b.  Veillez à ce qu’elle puisse être jointe au domaine sur lequel la machine virtuelle devrait apparaître après son basculement.

    c. Si le contrôleur de domaine ne fonctionne **pas** bien, essayez de vous connecter à la machine virtuelle basculée à l’aide d’un compte Administrateur local.
1. Si vous utilisez un serveur DNS personnalisé, vérifiez qu’il est accessible. Pour cela, vous pouvez suivre les étapes ci-dessous.
    a. Créez une machine virtuelle dans le même réseau. b. Vérifiez que la machine virtuelle parvient à effectuer la résolution de noms à l’aide du serveur DNS personnalisé.

>[!Note]
>Tout paramètre autre que les diagnostics de démarrage nécessite l’installation de l’agent de machine virtuelle Azure sur la machine virtuelle avant le basculement.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide supplémentaire, posez votre question sur le [Forum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou laissez un commentaire à la fin de ce document. Notre communauté est active et doit être en mesure de vous aider.
