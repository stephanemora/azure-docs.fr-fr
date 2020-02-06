---
title: Contrats et avenants Azure EA
description: Cet article explique dans quelle mesure les contrats et avenants Azure EA affectent votre utilisation du portail Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: fe3e63550c0bc37366d8564340032d7708942c2b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018718"
---
# <a name="azure-ea-agreements-and-amendments"></a>Contrats et avenants Azure EA

L’article décrit dans quelle mesure les contrats et avenants Azure EA peuvent affecter votre accès, votre utilisation et vos paiements pour les services Azure.

## <a name="amendment-status"></a>État des avenants

Tout avenant concernant les clients Azure doit passer par l’équipe partenaire ou des comptes Microsoft correspondante et être traité par le biais du centre d’opérations régional. Si vous pensez qu’un avenant n’a pas été traité, contactez votre partenaire, conseiller logiciel ou équipe des comptes Microsoft.

## <a name="enrollment-provisioning-status"></a>État du provisionnement de l’inscription

La date de début d’un nouvel engagement financier est définie par la date à laquelle le centre régional des opérations l’a traité. Étant donné que les commandes d’engagement financier via le portail Azure EA sont traitées dans le fuseau horaire UTC, vous pouvez constater un certain délai si votre bon de commande d’engagement financier a été traité dans une autre région. La date de début de couverture sur le bon de commande sur https://www.explore.ms indique le début de l’engagement financier. La date de début de couverture est le moment où l’engagement financier apparaît dans le portail Azure EA.

## <a name="special-pricing-and-azure-commitment-discount-amendments"></a>Tarification spéciale et modifications des remises d’engagement Azure

Si vous avez besoin d’un tarif spécial et d’avenants ACD (Azure Commitment Discount), vous devez en discuter avec l’équipe en charge de votre compte. L’équipe des opérations Azure peut appliquer la remise ACD une fois qu’un avenant a été traité par le service commercial et demandé à l’équipe des opérations Azure. La remise ACD est appliquée uniquement au mois en cours. Les mois précédents sont compensés par un crédit rétroactif. 

## <a name="support-offer-not-provisioned"></a>Offre de support non provisionnée

Vous pouvez commander un support Standard ou Pro Direct en achetant des références SKU spécifiques au support. La commande est similaire à un bon de commande de référence SKU financier Azure EA. Par exemple, 6QK-00001. Les numéros de référence SKU de support sont W6T-00002 (Pro-Direct) et W6T-00003 (Standard). Consultez les offres de support actuelles avant d’acheter des références SKU de support.

L’inscription doit avoir au moins un compte actif pour que l’offre de support soit provisionnée.

 L’[Offre de support Contrat Entreprise](https://azure.microsoft.com/offers/enterprise-agreement-support/) d’Azure est disponible pour certains clients.

## <a name="enrollment-status"></a>État de l’inscription

Une inscription a l’une des valeurs d’état suivantes. Chaque valeur détermine la façon dont vous pouvez utiliser une inscription et y accéder. L’état de l’inscription détermine à quelle phase se trouve votre inscription. Il vous indique si l’inscription doit être activée avant de pouvoir être utilisée. Ou, si la période initiale a expiré et que vous êtes facturé pour le dépassement de l’utilisation.

**En attente** : l’administrateur de l’inscription doit se connecter au portail Azure EA. Une fois ce dernier connecté, l’inscription passe à l’état **Actif**.

**Actif** : l’inscription est accessible et utilisable. Vous pouvez créer des comptes et des abonnements dans le portail Azure EA. L’inscription reste active jusqu’à la date de fin du contrat Entreprise.

**Durée prolongée indéterminée** Cet état est activé quand la date de fin du contrat Entreprise est atteinte. Avant que l’inscription EA n’atteigne la date de fin du contrat Entreprise, l’administrateur de l’inscription doit prendre l’une des décisions suivantes :

- Renouveler l’inscription en ajoutant un engagement financier
- Transférer l’inscription existante vers une nouvelle inscription
- Migrer vers le Programme d’abonnement en ligne Microsoft (MOSP)
- Confirmer la désactivation de tous les services associés à l’inscription

**Expiré** : l’inscription EA expire lorsqu’elle atteint la date de fin du contrat entreprise. Le client EA est exclu de la durée prolongée et tous ses services sont désactivés.

À partir du 1er août 2019, les nouveaux formulaires d’annulation ne sont pas acceptés pour les clients commerciaux Azure. Au lieu de cela, toutes les inscriptions rentrent dans l’état de durée prolongée indéterminée. Si vous souhaitez cesser d’utiliser des services Azure, fermez votre abonnement dans le [portail Azure EA](https://portal.azure.com). Ou bien, votre partenaire peut envoyer une demande de cessation sur https://www.explore.ms. Rien ne change pour les clients ayant des types de contrat public.

**Transféré** : l’état transféré est appliqué aux inscriptions dont les comptes et services associés sont transférés vers une nouvelle inscription. Les inscriptions ne sont pas automatiquement transférées si un nouveau numéro d’inscription est généré lors du renouvellement. Le numéro d’inscription précédent doit être inclus dans la demande de renouvellement du client pour qu’un transfert automatique puisse avoir lieu.

## <a name="partner-markup"></a>Majoration des partenaires

Dans le portail Azure EA, la majoration des prix des partenaires aide à améliorer les rapports de coût pour les clients. Le portail Azure EA montre l’utilisation et les prix configurés par les partenaires pour leurs clients.

La majoration permet aux administrateurs partenaires d’ajouter un pourcentage de majoration à leurs contrats entreprise indirects. Le pourcentage de majoration s’applique à toutes les informations de service interne Microsoft dans le portail Azure EA telles que : tarifs de compteur, engagements financiers et commandes. Une fois la majoration publiée par le partenaire, le client voit les coûts Azure dans le portail Azure EA. Par exemple, le résumé d’utilisation, les listes de prix et les rapports d’utilisation téléchargés.

À compter de septembre 2019, les partenaires peuvent appliquer une majoration à tout moment. Ils n’ont pas besoin d’attendre la prochaine date anniversaire pour appliquer la majoration.

Microsoft n’a pas accès et n’utilise pas la majoration fournie et aux prix associés à quelque fin que ce soit, sauf autorisation explicite du partenaire de canal.

### <a name="how-the-calculation-works"></a>Fonctionnement du calcul

Le LSP fournit un seul pourcentage dans le portail EA.  Toutes les informations commerciales sur le portail seront réparties en fonction du pourcentage fourni par le LSP. Exemple :

- Le client signe un Contrat Entreprise avec un engagement financier de 100 000 USD.
- Le tarif de compteur pour le service A est de 10 USD/heure.
- Le LSP définit un pourcentage de majoration de 10 % sur le portail EA.
- L’exemple ci-dessous montre comment le client verra les informations commerciales :
    - Solde financier : 110 000 USD.
    - Tarif de compteur pour le service A : 11 USD/heure.
    - Informations d’utilisation/d’hébergement pour le service A lorsqu’il est utilisé pendant 100 heures : 1 100 USD.
    - Solde financier disponible pour la post-déduction client de consommation du service A : 108 900 USD.

### <a name="when-to-use-a-markup"></a>Quand utiliser une majoration

Utilisez la fonctionnalité si vous définissez le même pourcentage de majoration sur TOUTES les transactions commerciales dans l’EA. À savoir, si vous majorez les informations d’engagement financier, les tarifs de compteur, les informations de commande, etc.

N’utilisez pas la fonctionnalité de majoration si :
- Vous utilisez des tarifs différents entre l’engagement financier et le tarifs de compteur.
- Vous utilisez des tarifs différents pour différents compteurs.

Si vous utilisez des tarifs différents pour différents compteurs, nous vous recommandons de développer une solution personnalisée basée sur la clé d’API, qui peut être fournie par le client, pour extraire les données de consommation et fournir des rapports.

### <a name="other-important-information"></a>Autres informations importantes

Cette fonctionnalité est conçue pour fournir une estimation des coûts Azure au client final. Le LSP est responsable de toutes les transactions financières avec le client dans le cadre du Contrat Entreprise.

Veillez à consulter les informations commerciales, informations sur le solde financier, liste des prix, etc., avant de présenter les prix majorés au client final.

### <a name="how-to-add-a-price-markup"></a>Comment ajouter une majoration de prix

**Étape 1 : Ajouter une majoration de prix**

1. Dans Enterprise Portal, cliquez sur **Rapports** dans le volet de navigation de gauche.
1. Sous _Résumé de l’utilisation_, cliquez sur le libellé bleu **Majoration**.
1. Entrez le pourcentage de majoration (entre -100 et 100), puis cliquez sur **Aperçu**.


**Étape 2 : Réviser et valider**

Passez en revue le prix de la majoration dans le _Résumé de l’utilisation_ pour la période d’engagement dans la vue client. Le tarif Microsoft sera toujours disponible dans la vue partenaire. Les vues peuvent être changées à l’aide du bouton « personnes » de la majoration partenaire en haut à droite.

1. Consultez les prix dans la grille tarifaire.
1. Des modifications peuvent être apportées avant la publication en sélectionnant **Modifier** dans l’onglet _Afficher le résumé d’utilisation > Vue client_.  
  Les prix des services et les soldes d’engagement sont majorés selon les mêmes pourcentages. Si vous avez des pourcentages différents pour le solde financier et les tarifs de compteur, ou des pourcentages différents pour différents services, n’utilisez pas cette fonctionnalité.

**Étape 3 : Publier**

Une fois la tarification vérifiée et validée, cliquez sur **Publier**.
  
La tarification avec majoration sera accessible aux administrateurs d’entreprise immédiatement après la sélection de Publier. Des modifications ne peuvent pas être apportées à la majoration. Vous devez désactiver la majoration et recommencer à partir de l’Étape 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Quelles sont les inscriptions pour lesquelles une majoration est activée ?

Pour vérifier si une inscription comprend une majoration publiée, cliquez sur **Gérer les** dans le volet de navigation de gauche, puis cliquez sur l’onglet **Inscription**. Sélectionnez la zone de l’inscription à vérifier et afficher l’état de majoration sous _Détails de l’inscription_. L’état actuel de la fonctionnalité de majoration pour ce Contrat Entreprise indique Désactivé, Aperçu ou Publié.

### <a name="how-can-the-customer-download-usage-estimates"></a>Comment le client peut-il télécharger des estimations d’utilisation ?

Une fois que la majoration partenaire est publiée, le client indirect a accès aux fichiers .csv mensuels de solde et de frais et aux fichiers . csv de détail de l’utilisation. Les fichiers de détails de l’utilisation incluent le taux de la ressource et le coût total.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Comment, en tant que partenaire, appliquer une majoration à des clients EA existants qui étaient antérieurs avec un autre partenaire ?
Les partenaires peuvent utiliser la fonctionnalité de majoration (sur Azure EA) après le traitement d’un changement de partenaire de canal. Vous n’avez pas besoin d’attendre le prochain terme anniversaire.


## <a name="resource-commitment-and-requesting-quota-increases"></a>Augmentations d’engagement sur les ressources et de demande de quota

**Le système applique les quotas par défaut suivants par abonnement :**

| **Ressource** | **Quota par défaut** | **Commentaires** |
| --- | --- | --- |
| Instances de calcul Microsoft Azure | 20 petites instances de calcul simultanées ou leurs équivalents des autres tailles d’instance de calcul. | Le tableau suivant indique comment calculer le nombre équivalent de petites instances :<ul><li> Très petite - 1 petite instance équivalente </li><li> Petite - 1 petite instance équivalente </li><li> Moyenne - 2 petites instances équivalentes </li><li> Grande - 4 petites instances équivalentes </li><li> Très grande - 8 petites instances équivalentes </li> </ul>|
| Machines virtuelles d’instances de calcul Microsoft Azure v2 | EA : 350 cœurs | Machines virtuelles Azure IaaS v2 :<ul><li> Famille A0\_A7 - 350 cœurs </li><li> Famille B\_A0\_A4 - 350 cœurs </li><li> Famille A8\_A9 - 350 cœurs </li><li> Famille DF - 350 cœurs</li><li> GF - 350 cœurs </li></ul>|
| Services hébergés Microsoft Azure | 6 services hébergés | Cette limite de services hébergés ne peut pas être augmentée au-delà de six pour un abonnement individuel. Si vous avez besoin de services hébergés supplémentaires, ajoutez des abonnements supplémentaires. |
| Stockage Microsoft Azure | 5 comptes de stockage, chacun d’une taille maximale de 100 To. | Vous pouvez augmenter le nombre de comptes de stockage jusqu’à 20 par abonnement. Si vous avez besoin de comptes de stockage supplémentaires, ajoutez des abonnements supplémentaires. |
| SQL Azure | 149 bases de données de l’un ou l’autre type (par exemple, Web Edition ou Business Edition). |   |
| Contrôle d’accès | 50 espaces de noms par compte. 100 millions de transactions de contrôle d’accès par mois |   |
| Service Bus | 50 espaces de noms par compte. 40 connexions Service Bus | Les clients qui achètent des connexions Service Bus via des packs de connexion disposent de quotas égaux au point médian entre le pack de connexion qu’ils ont acheté et le montant de pack de connexion suivant le plus élevé. Les clients qui choisissent un pack de 500 auront un quota de 750. |

## <a name="resource-commitment"></a>Engagement sur les ressources

Microsoft s’engage à vous fournir des services à hauteur (au minimum) du niveau d’utilisation associé inclus dans l’engagement mensuel que vous avez acheté (engagement de service), mais toutes les autres augmentations des niveaux d’utilisation des ressources du service (par exemple, l’ajout du nombre d’instances de calcul exécutées ou l’augmentation du volume de stockage utilisé) sont sujettes à la disponibilité de ces ressources de service.

Tout quota décrit ci-dessus n’est pas un engagement de service. Afin de déterminer le nombre de petites instances de calcul simultanées (ou leur équivalent) fourni dans le cadre de l’engagement de service, Microsoft divise le nombre d’heures de petites instances de calcul validées et achetées pour un mois par le nombre d’heures du mois le plus court de l’année (soit février, 672 heures).

## <a name="requesting-a-quota-increase"></a>Demande d’augmentation de quota

Vous pouvez demander une augmentation de quota à tout moment en soumettant un [requête en ligne](https://g.microsoftonline.com/0WAEP00en/6). Pour traiter votre requête, fournissez les informations suivantes :

- Le compte Microsoft ou le compte professionnel ou scolaire associé au propriétaire du compte de votre abonnement. Il s’agit de l’adresse e-mail utilisée pour se connecter au portail Microsoft Azure pour gérer vos abonnements. Veuillez également préciser si ce compte est associé à une inscription EA.
- Ressources et quantité pour lesquelles vous souhaitez augmenter le quota.
- ID d’abonnement du portail des développeurs Azure associé à votre service.
  - Pour plus d’informations sur l’obtention de votre ID d’abonnement, veuillez [contacter le support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="azure-compute-pre-purchase-plan-cpp"></a>Offre de préachat (CPP) Azure Compute

L’offre préachat Azure Compute (CPP, précédemment connu sous le nom de promotion Azure Compute) est une offre qui propose aux clients un état stable, des charges de travail prévisibles, l’opportunité d’obtenir un calcul à une remise significative en préachetant Azure Compute pour une période de 12 mois. Elle est disponible pour EA et visible dans le portail EA.

Si vous avez commandé une référence SKU CPP, elle s’applique à un type d’ordinateur spécifique dans un centre de données spécifique. Comme nous l’avons vu aujourd’hui, nous ne fournissons pas de liste de tous les achats CPP effectués par un client, ce qui signifie qu’il n’y a aucune liste de crédits en attente. Toutefois, lorsque vous configurez les types d’ordinateurs achetés dans le centre de données dans lequel ils ont été achetés, vous verrez l’allocation mensuelle dans le rapport Résumé de l’utilisation sur le portail EA.

Le montant préalablement acheté s’affiche comme un crédit dans la colonne « Unités incluses » et l’utilisation sur ce montant n’entraîne aucun frais d’utilisation tant que les « Unités incluses » ne sont pas consommées. Une fois qu’elles sont consommées, l’utilisation supplémentaire sera facturée au tarif négocié dans la grille tarifaire normale du client EA.

La façon dont les crédits sont achetés est basée sur le nombre d’ordinateurs d’une classe par mois. Par exemple : si vous avez acheté 70 machines virtuelles A2 par an dans la région USA Est 2, vous ne le verrez pas vraiment dans le portail EA comment vous l’avez acheté.

Les machines mensuelles sont calculées comme s’exécutant 744 heures par mois. La conversion en heures serait de 70 fois 744 ou 52 080 heures achetées de machines virtuelles A2 par mois. Dans le résumé de l’utilisation du portail EA, les machines virtuelles A2 sont rapportées par incréments de 100 heures. Vous pouvez donc voir un crédit de 520,8 (52 080/100) « Unités incluses » par mois et voir les unités consommées jusqu’à présent au cours du mois. Les frais d’utilisation doivent être de zéro jusqu’à ce que les unités consommées mensuellement dépassent les unités incluses mensuellement.

Différents types d’ordinateurs utilisent des unités de mesure différentes. Ainsi, par exemple, les machines virtuelles D3 et D4 utilisent une unité de mesure de 10 heures dans notre rapport de résumé de l’utilisation. Ainsi, un achat de 70 de celles-ci peut afficher jusqu’à 5 208 unités incluses par mois (52 080/10).

En général, la formule ci-dessous vous fournira le nombre de machines virtuelles achetées en fonction de leurs unités de mesure :

<center><b> (« Quantité incluse » * « Unités de mesure ») / 744 </b></center>

## <a name="plan-skus"></a>Références SKU de plan

Les références SKU de plan vous permettent d’acheter une suite de services intégrés à tarif réduit. Les références SKU de plan sont conçues pour se compléter via d’autres offres intégrées et suites pour réaliser des économies supplémentaires.

L’abonnement Operations Management Suite (OMS) en est un exemple. OMS offre un moyen simple d’accéder à un ensemble complet de fonctionnalités de gestion basées sur le cloud, notamment l’analyse, la configuration, l’automatisation, la sécurité, la sauvegarde et la récupération d’urgence. Les abonnements OMS incluent des droits sur les composants de System Center pour fournir une solution complète pour les environnements cloud hybrides.

Les administrateurs d’entreprise peuvent attribuer des propriétaires de comptes afin d’approvisionner des références SKU de plan précédemment achetées dans Enterprise Portal en procédant comme suit :

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Afficher la grille tarifaire pour vérifier la quantité incluse

1. Connectez-vous en tant qu’administrateur d’entreprise.
1. Cliquez sur **Rapports** dans la barre de navigation à gauche.
1. Cliquez sur l’onglet **Grille tarifaire**.
1. Cliquez sur l’icône « Télécharger » dans le coin supérieur droit.
1. Recherchez les numéros de références SKU de plan correspondants avec un filtre sur la colonne « Quantité incluse », puis sélectionnez des valeurs supérieures à « 0 ».

### <a name="provision-the-plan-skus"></a>Approvisionner les références SKU de plan

Les administrateurs d’entreprise peuvent ajouter de nouveaux propriétaires de comptes, associer un propriétaire de compte existant ou demander aux propriétaires de comptes existants de se connecter au portail Azure EA afin d’approvisionner des références SKU de plan précédemment achetées dans le portail Azure EA en effectuant les étapes ci-dessous.  

**Ajout d’un nouveau propriétaire de compte (effectué par l’administrateur EA) :**

1. Dans le portail Azure EA, sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Compte**.
1. Dans la page du compte, cliquez sur **+ Ajouter un compte**.
1. Sélectionnez un service ou laissez comme non affecté.
1. Sélectionnez le type d’authentification voulu.
1. Spécifiez un nom convivial à utiliser pour identifier ce compte dans les rapports.
1. Entrez l’adresse e-mail du propriétaire du compte que vous souhaitez associer au nouveau compte.
1. Confirmez l’adresse e-mail que vous souhaitez associer au nouveau compte.
1. Cliquez sur **Add**.
1. Vous pouvez ajouter un autre compte en cliquant sur **Add Another Account** (Ajouter un autre compte), ou en cliquant sur le bouton **Ajouter** en bas à droite dans la barre d’outils de gauche.
1. Le propriétaire du compte peut maintenant se connecter pour ajouter les abonnements appropriés afin d’approvisionner les références SKU de plan

**Associer un propriétaire de compte existant :**

1. Dans le Enterprise Portal, cliquez sur **Gérer**.
1. Cliquez sur l’onglet **Compte**.
1. Cliquez sur **+ Ajouter un compte**. Entrez le compte Microsoft ou un compte professionnel ou scolaire associé au compte existant.
1. Confirmez le compte Microsoft ou un compte professionnel ou scolaire associé au compte existant.
1. Spécifiez le nom à utiliser pour identifier ce compte dans les rapports.
1. Cliquez sur **Add**.
1. Vous pouvez ajouter un compte supplémentaire en sélectionnant à nouveau l’option **+ Ajouter un compte**, ou bien revenir à la page d’accueil en sélectionnant le bouton **Admin**.  
1. Si vous cliquez pour afficher la page Compte, le compte que vous venez d’ajouter s’affiche avec l’état « En attente ». Il passe à l’état « Actif » lorsque le propriétaire du compte se connecte pour la première fois au portail EA.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Propriétaires de comptes existants/nouveaux pour créer de nouveaux abonnements

**Étape 1 : Se connecter au compte**
1. Dans le portail Azure EA, sélectionnez l’onglet **Gérer** et accédez à **Abonnement** dans le menu supérieur.
1. Vérifiez que vous êtes connecté en tant que propriétaire de ce compte.
1. Cliquez sur **+ Ajouter un abonnement**.
1. Cliquez sur **Achat**.

La première fois que vous ajoutez un abonnement à un compte, vous devez fournir vos informations de contact. Lorsque vous ajoutez des abonnements ultérieurement, vos informations de contact sont remplies automatiquement pour vous.

La première fois que vous ajoutez un abonnement à votre compte, vous êtes invité à accepter le contrat MOSA et un plan tarifaire. Ces sections ne s’appliquent PAS aux clients Contrat Entreprise, mais sont actuellement nécessaires pour approvisionner votre abonnement. L’avenant à votre inscription Contrat Entreprise Microsoft Azure se substitue aux éléments cités ci-dessus, et votre lien contractuel ne changera pas. Cochez la case indiquant que vous acceptez les conditions.

**Étape 2 : Mettre à jour le nom de l’abonnement**

Tous les nouveaux abonnements sont ajoutés avec le nom d’abonnement « Microsoft Azure Enterprise » par défaut. Il est important de mettre à jour le nom de l’abonnement pour le différencier des autres abonnements au sein de votre inscription Enterprise et vous assurer qu’il est reconnaissable sur les rapports au niveau de l’entreprise.

Cliquez sur **Abonnements**, cliquez sur l’abonnement que vous avez créé, puis cliquez sur **Edit Subscription Details** (Modifier les détails de l’abonnement).

Mettez à jour le nom de l’abonnement et l’administrateur de service, puis cliquez sur la case. Le nom de l’abonnement est indiqué dans les rapports. Il correspond également au nom du projet qui est associé à l’abonnement dans le portail de développement.
Cela peut prendre jusqu’à 24 heures pour que les nouveaux abonnements apparaissent dans la liste des abonnements.

Seuls les propriétaires de comptes peuvent consulter et gérer les abonnements.

### <a name="troubleshooting"></a>Dépannage

**Propriétaire du compte affiché à l’état En attente**

Lorsque de nouveaux propriétaires de compte sont ajoutés pour la première fois à l’inscription, « En attente » apparaît toujours sous l’état. Lors de la réception de l’e-mail de bienvenue et d’activation, l’AO peut se connecter pour activer son compte. Cette activation met à jour l’état du compte de « en attente » à « actif ».

**Utilisations facturées après l’achat de références SKU de plan**

Ce scénario se produit lorsque le client a déployé des services sous un numéro d’inscription incorrect ou a sélectionné des services incorrects.

Pour valider si vous effectuez un déploiement dans le cadre de l’inscription appropriée, vous pouvez vérifier les informations sur les unités incluses via la grille tarifaire. Connectez-vous en tant qu’administrateur d’entreprise, cliquez sur **Rapports** dans le volet de navigation gauche, puis sélectionnez l’onglet **Grille tarifaire**. Cliquez sur l’icône Télécharger dans le coin supérieur droit et recherchez les numéros de références SKU de plan correspondants avec un filtre sur la colonne « Quantité incluse », puis sélectionnez des valeurs supérieures à « 0 ».

Assurez-vous que votre plan OMS est affiché sur la grille tarifaire, sous les unités incluses. S’il n’existe aucune unité incluse pour le plan OMS sur votre inscription, votre plan OMS se trouve peut-être sous une autre inscription. Veuillez contacter le support Azure Enterprise Portal à l’adresse [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Si les unités incluses pour les services de la grille tarifaire ne correspondent pas à celles que vous avez déployées, par exemple Operational Insights Premium Data Analyzed et Operational Insights Standard Data Analyzed, cela signifie que vous avez peut-être déployé des services qui ne sont pas couverts par le plan. Veuillez contacter le support Azure Enterprise Portal à l’adresse [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) afin que nous puissions vous aider davantage.

**Services de références SKU de plan approvisionnés sur une inscription incorrecte**

Si vous avez plusieurs inscriptions et que vous avez déployé des services sous un numéro d’inscription incorrect, qui ne dispose pas d’un plan OMS, contactez le support Azure Enterprise Portal à l’adresse [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à utiliser le portail Azure EA, consultez [Bien démarrer avec le portail Azure EA](ea-portal-get-started.md).
- Les administrateurs du portail Azure EA doivent lire la [documentation consacrée à l’administration du portail Azure EA](ea-portal-administration.md) pour découvrir les tâches d’administration courantes.
