---
title: Configurer un labo axé sur le deep learning avec Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un labo pour enseigner la création de scripts shell sur Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 889d0d1e98f5c9947588011774d02e54f05edca1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257757"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Configurer un labo axé sur le deep learning dans le cadre du traitement en langage naturel avec Azure Lab Services
Cet article vous montre comment configurer un labo axé sur le deep learning dans le cadre du traitement en langage naturel (NLP) avec Azure Lab Services. Le traitement en langage naturel (NLP) est une forme d’intelligence artificielle (IA) qui fournit aux ordinateurs des fonctionnalités de traduction, de reconnaissance vocale et d’autres fonctionnalités de compréhension de la langue.  

Les étudiants qui suivent un cours de traitement en langage naturel disposent d’une machine virtuelle Linux pour apprendre à appliquer des algorithmes de réseau neuronal afin de développer des modèles de deep learning utilisés pour l’analyse de la langue humaine écrite. 

## <a name="lab-configuration"></a>Configuration du laboratoire
Pour configurer ce labo, vous avez besoin d’un abonnement Azure pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services ou utiliser un compte lab existant. Pour créer un compte lab, consultez le tutoriel suivant : [Tutoriel pour configurer un compte lab](tutorial-setup-lab-account.md).
 
Après avoir créé le compte lab, activez les paramètres suivants dans celui-ci : 

| Paramètres du compte lab | Instructions |
| ----------- | ------------ |  
| Images de la Place de marché | Activez l’image Data Science Virtual Machine pour Linux (Ubuntu) à utiliser au sein de votre compte lab.  Pour obtenir des instructions, consultez l’article suivant : [Spécifier les images de la Place de marché accessibles aux créateurs de labo](specify-marketplace-images.md). | 

Suivez [ce tutoriel](tutorial-setup-classroom-lab.md) pour créer un labo et appliquer les paramètres suivants :

| Paramètres du labo | Valeur/instructions | 
| ------------ | ------------------ |
| Taille de machine virtuelle | GPU de petite taille (calcul). Cette taille est optimisée pour les applications nécessitant beaucoup de ressources système et de ressources réseau, notamment l’intelligence artificielle et le deep learning. |
| Image de machine virtuelle | [Data Science Virtual Machine pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Cette image fournit des outils et des frameworks de deep learning pour le machine learning et la science des données. Pour voir la liste complète des outils installés sur cette image, consultez l’article suivant : [Ce qui est inclut dans la machine DSVM](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Activer la connexion Bureau à distance | Activer. <p>L’activation de ce paramètre permet aux enseignants et aux étudiants de se connecter à leurs machines virtuelles à l’aide du Bureau à distance (RDP).</p><p>**Important !** Le protocole RDP est déjà installé et configuré sur l’image Data Science Virtual Machine pour Linux. Par conséquent, les enseignants/étudiants peuvent se connecter aux machines virtuelles via RDP sans aucune étape supplémentaire. De plus, si vous devez vous connecter au bureau graphique, le [serveur X2Go](https://wiki.x2go.org/doku.php/doc:newtox2go) est déjà installé pour cette image sur la machine virtuelle. Les étudiants doivent installer le client X2Go sur leurs ordinateurs locaux et l’utiliser pour se connecter. Pour plus d’informations, consultez les guides suivants : <ul><li>[Comment accéder à Data Science Virtual Machine pour Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Se connecter à la machine virtuelle modèle pour installer des packages GUI et RDP](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

L’image Data Science Virtual Machine pour Linux fournit les outils et infrastructures de deep learning nécessaires pour ce type de cours. Par conséquent, une fois la machine modèle créée, vous n’avez pas besoin de la personnaliser davantage. Elle peut être publiée afin d’être utilisée par les étudiants. Sélectionnez le bouton **Publier** dans la page du modèle pour publier le modèle dans le labo.  

## <a name="cost"></a>Coût
Si vous souhaitez estimer le coût de ce labo, vous pouvez utiliser l’exemple suivant : 

Pour une classe de 25 étudiants avec 20 heures de cours planifiées et un quota de 10 heures pour le travail à la maison ou les devoirs, le prix du labo est le suivant : 25 étudiants * (20 + 10) heures * 139 unités Lab * 0,01 USD par heure = 1 042,5 USD

Pour plus d’informations sur les tarifs, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusion
Cet article vous a présenté les étapes de création d’un labo pour le cours sur le traitement en langage naturel. Vous pouvez utiliser une configuration similaire pour d’autres cours de deep learning.

## <a name="next-steps"></a>Étapes suivantes
Les étapes suivantes sont communes à la configuration de n’importe quel labo :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users) 

