---
title: Configurer un labo de piratage éthique sur VirtualBox avec Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un labo à l’aide d’Azure Lab Services pour enseigner le piratage éthique avec VirtualBox.
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: ef9ac58490ea4e917f76d1b0ee4874411dd7b4e2
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176925"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class-with-virtualbox"></a>Configurer un labo pour enseigner le piratage éthique avec VirtualBox

Cet article explique comment organiser un cours autour de l’analyse forensique du piratage éthique. Des tests d’intrusion, une pratique utilisée par la communauté de piratage éthique, sont effectués quand quelqu’un tente d’accéder au système ou au réseau pour détecter les vulnérabilités qu’un attaquant malveillant pourrait exploiter.

Dans une classe sur le piratage éthique, les étudiants apprennent les techniques modernes de défense face aux vulnérabilités. Chaque étudiant a accès à une machine virtuelle hôte qui a trois machines virtuelles imbriquées : deux machines virtuelles avec une image [Seed](https://seedsecuritylabs.org/lab_env.html) et une autre machine avec une image [Kali Linux](https://www.kali.org/). La machine virtuelle Seed est utilisée pour les besoins de fonctionnement, et la machine virtuelle Kali sert à accéder aux outils nécessaires pour exécuter les tâches forensiques.

Cet article comprend deux sections principales. La première section explique comment créer le laboratoire. La deuxième section explique comment créer la machine modèle à l’aide de la virtualisation imbriquée, ainsi que des outils et des images nécessaires. Dans ce cas, il s’agit de deux images Seed et d’une image Linux Kali présentes sur une machine où [VirtualBox](https://www.virtualbox.org/) est activé pour héberger les images.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services ou utiliser un compte existant. Pour créer un compte lab, consultez le tutoriel suivant : [Tutoriel pour configurer un compte lab](tutorial-setup-lab-account.md).

Suivez [ce tutoriel](tutorial-setup-classroom-lab.md) pour créer un labo et appliquer les paramètres suivants :

| Taille de la machine virtuelle | Image |
| -------------------- | ----- |
| Moyenne (virtualisation imbriquée) | Windows Server 2019 Datacenter |
| Moyenne (virtualisation imbriquée) | Windows 10 |

## <a name="template-machine"></a>Machine modèle

Une fois la machine modèle créée, démarrez-la et connectez-vous à celle-ci pour effectuer les trois tâches principales suivantes.

1. Configurez la machine pour qu’elle utilise [VirtualBox](https://www.virtualbox.org/) pour les machines virtuelles imbriquées.
2. Configurez l’image Linux [Kali](https://www.kali.org/). Kali est une distribution Linux qui comprend des outils pour le test d’intrusion et l’audit de sécurité.
3. Configurez l’image Seed. Dans cet exemple, l’image [Seed](https://seedsecuritylabs.org/lab_env.html) est utilisée. Cette image est créée spécifiquement pour la formation de sécurité.

Le reste de cet article aborde les étapes manuelles permettant d’effectuer les tâches ci-dessus.

### <a name="installing-virtualbox"></a>Installation de VirtualBox

1. Téléchargez les [packages de plateforme VirtualBox](https://www.virtualbox.org/wiki/Downloads) en sélectionnant l’option d’hôtes Windows.
2. Exécutez l’exécutable d’installation et utilisez les options par défaut pour terminer l’installation.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurer une machine virtuelle imbriquée avec une image Linux Kali

Kali est une distribution Linux qui comprend des outils pour le test d’intrusion et l’audit de sécurité.

1. Téléchargez l’image ova à partir des [images VirtualBox de la machine virtuelle Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines).  Nous vous recommandons la version 32 bits, car la version 64 bits est chargée avec des erreurs.  Notez le nom d’utilisateur par défaut et le mot de passe indiqués dans la page de téléchargement.
2. Ouvrez VirtualBox Manager et [importez l’image .ova](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html).  Le contrat de licence Kali doit être passé en revue et accepté pour pouvoir continuer.

>[!Note]
>- La RAM par défaut de VirtualBox pour la machine virtuelle Kali est 2 Go (2048). Nous vous recommandons d’augmenter la RAM à au moins 4 Go (4096) ou plus en fonction de vos besoins.  Cela peut être modifié par les étudiants sur leurs machines virtuelles.  La modification de la taille de la RAM dans VirtualBox ne modifie pas la taille de machine virtuelle du labo.
>- Par défaut, le disque dur est défini sur une limite de 80 Go, mais est alloué dynamiquement.  Les machines du service lab sont limitées à 128 Go d’espace disque. Veillez donc à ne pas dépasser cette taille de disque.
>- L’image Kali dispose de la norme USB 2.0 qui requiert le [pack d’extension VirtualBox de machine virtuelle Oracle](https://www.virtualbox.org/wiki/Downloads) ou de définir le contrôleur USB sur 1.0 sous l’onglet USB.

### <a name="setup-seed-lab-images"></a>Configurer des images de labo Seed

1. Téléchargez et extrayez l'[image de machine virtuelle SEED Labs](https://seedsecuritylabs.org/labsetup.html).
2. Suivez les instructions pour [créer une machine virtuelle dans VirtualBox.](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md)
   Si vous avez besoin de plusieurs machines virtuelles SEED, faites des copies du fichier .iso pour chaque machine. L’utilisation du même fichier .iso pour différents machines ne fonctionnera pas correctement.

>[!IMPORTANT] 
>Assurez-vous que toutes les machines virtuelles imbriquées sont hors tension avant de publier le modèle.  Les laisser sous tension provoque des effets secondaires inattendus, notamment un endommagement des machines virtuelles.

## <a name="cost"></a>Coût  

Si vous souhaitez estimer le coût de ce labo, vous pouvez utiliser l’exemple suivant :

Pour une classe de 25 étudiants avec 20 heures de cours planifiées et un quota de 10 heures pour le travail à la maison ou les devoirs, le prix du labo est le suivant :

25 étudiants \* (20 + 10) heures \* 55 unités Lab \* 0,01 USD par heure = 412,50 USD

>[!IMPORTANT]
>L’estimation du coût est fournie à titre d’exemple uniquement. Pour en savoir plus sur les tarifs actuels, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusion

Cet article vous a présenté les étapes de création d’un labo pour un cours sur le piratage éthique. Il comprend les étapes de configuration de la virtualisation imbriquée qui sont nécessaires pour créer deux machines virtuelles dans une machine virtuelle hôte en vue d’exécuter les tests d’intrusion.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)
