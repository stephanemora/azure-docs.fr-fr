---
title: Description de la sécurité Azure Australie
description: Informations les plus demandées sur les régions d’Australie et la configuration requise pour satisfaire aux exigences spécifiques des réglementations, de la législation et de la politique du secteur public australien.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571109"
---
# <a name="azure-australia-security-explained"></a>Description de la sécurité Azure Australie

Cet article répond à certaines questions et aux principales informations demandées par et pour les agences gouvernementales australiennes concernant l’examen, la conception et le déploiement de Microsoft Azure dans les régions d’Australie.

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>Documents IRAP (Programme des évaluateurs agréés en sécurité de l’information) et CCSL (Liste des services cloud certifiés)

Le ACSC (Centre de cyber-sécurité d’Australie) fournit une lettre de certification, un rapport de certification et un guide du consommateur pour un service lorsqu’il est ajouté à la CCSL.

Microsoft est actuellement répertorié sur la CCSL pour Azure, Office 365 et Dynamics 365 CRM.

Microsoft met ses documents de certification d’audit, d’évaluation et de ACSC à la disposition de ses clients et partenaires sur une page spécifique à l’Australie du [portail Microsoft Service Trust](https://aka.ms/au-irap).

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>Certification DLM (Marqueurs de limitation de diffusion) et niveau PROTECTED (Protégé)

Le processus d’approbation des systèmes, tels que les services cloud, par les organisations gouvernementales, est défini dans l’[Information Security Manual (ISM)](https://acsc.gov.au/infosec/ism/) (Manuel relatif à la sécurisation des informations) produit et publié par l’ACSC. L’ACSC est l’entité de l’ASD responsable de la certification en matière de cyber-sécurité et de services cloud.

Le processus d’approbation passe par deux étapes :

1. L’IRAP (évaluation de la sécurité) : processus dans lequel des professionnels inscrits évaluent les systèmes, services et solutions par rapport aux contrôles techniques dans l’ISM, puis évaluent l’efficacité de leur implémentation. L’évaluation identifie également les risques spécifiques que l’autorité d’approbation doit prendre en compte avant d’émettre une ATO (Approbation de bon fonctionnement).

1. L’ATO : processus dans lequel un directeur senior d’une agence gouvernementale reconnaît formellement et accepte le risque résiduel d’un système par rapport aux informations qu’il traite, stocke et communique.  Ce processus débouche sur le SA (Évaluation de la sécurité).

L’évaluation des services Azure au niveau PROTECTED (Protégé) confirme l’implémentation et le bon fonctionnement des contrôles de sécurité requis pour le stockage et le traitement des données de niveau PROTECTED et des niveaux inférieurs.

## <a name="australian-data-classification-changes"></a>Modifications de classification des données australiennes

Le 1er octobre 2018, l’Attorney General’s Department (Ministère du procureur général) a annoncé des modifications apportées au PSPF (Cadre de la politique de protection en matière de sécurité), notamment l’introduction d’un système [SCI](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx) (Traitement des informations sensibles et classifiées).

![Classifications du PSPF révisées](media/pspf-classifications.png)

Toutes les agences et organisations australiennes opérant sous le PSPF sont affectées par ces modifications. La principale modification impactant nos certifications IRAP/CCSL actuelles concerne le retrait des DLM (Marqueurs de limitation de diffusion). Le marqueur **OFFICIAL: Sensitive** (Officiellement sensible) remplace les différents DLM utilisés pour la protection des informations sensibles. Cette modification a également introduit trois marqueurs de gestion des informations qui peuvent être appliqués à tous les niveaux de sensibilité et de classification de toutes les informations officielles. La classification **PROTECTED** (Protégé) reste inchangée.

Le terme Unclassified (Non classifié) est supprimé du nouveau système et le terme Unofficial (Non officiel) est appliqué aux informations non gouvernementales, bien qu’il ne nécessite pas de marqueur formel.

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>Choix d’une région Azure pour mes charges de travail OFFICIAL: Sensitive et PROTECTED

Les services certifiés **OFFICIAL: Sensitive** et **PROTECTED** Azure sont déployés dans les quatre régions de Data Center australiennes (Australie Est, Australie Sud-Est, Australie Centre et Australie Centre 2). Toutefois, le rapport de certification émis par l’ACSC recommande que les données de niveau **PROTECTED** soient déployées vers les régions Azure Government dans Canberra, si un service y est disponible. Des informations plus détaillées sur les services certifiés **PROTECTED** Azure sont disponibles sur la page [Australie du STP](https://aka.ms/au-irap).

>[!NOTE]
>Microsoft recommande que les données gouvernementales de toutes les sensibilités et classifications soient déployées dans les régions de Australie Centre et Australie Centre 2, car elles sont conçues et exploitées spécifiquement pour les besoins du gouvernement.

Vous trouverez plus d’informations sur la nature spécifique des régions australiennes d’Azure sur la page [Régions centrales d’Australie Azure](https://azure.microsoft.com/global-infrastructure/australia/).

## <a name="how-microsoft-separates-classified-and-official-data"></a>Comment Microsoft sépare les données classifiées et officielles

Microsoft utilise Azure et Office 365 en Australie en considérant que toutes les données sont sensibles et/ou classées, et en augmentant ses contrôles de sécurité en conséquence.

L’infrastructure prenant en charge Azure est susceptible de traiter des données de plusieurs classifications.  Mais comme nous partons du principe que les données client sont classifiées, nous avons mis en place les contrôles appropriés. Microsoft a adopté une position de sécurité de base pour ses services conformes aux exigences du PSPF pour stocker et traiter les informations classifiées au niveau **PROTECTED**.

Pour garantir à nos clients qu’un locataire dans Azure n’est pas exposé à d’autres locataires, Microsoft implémente des contrôles complets de défense en profondeur.

Au-delà des fonctionnalités implémentées au sein de la plate-forme Microsoft Azure, des contrôles configurables supplémentaires tels que le chiffrement avec des clés gérées par le client, la virtualisation imbriquée et l’accès administratif juste-à-temps peuvent réduire le risque. Au sein des régions australiennes d’Azure Government dans Canberra, nous avons implémenté un processus d’inscription sur liste verte officielle des seules organisations gouvernementales australiennes et néo-zélandaises et des organisations nationales d’infrastructures essentielles. Ce cloud de la communauté du secteur public offre une garantie supplémentaire aux organisations qui sont sensibles aux risques de colocataires.

Le rapport de certification **PROTECTED** de Microsoft Azure confirme que ces contrôles sont efficaces pour le stockage, le traitement et l’isolation des données classifiées au niveau **PROTECTED**.

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>Pertinence de l’IRAP/CCSL pour les fournisseurs d’infrastructures critiques et les administrations publiques

De nombreux fournisseurs d’infrastructures critiques et d’administrations publiques intègrent les exigences du gouvernement fédéral dans leur politique de sécurité et leur infrastructure d’assurance. Ces organisations gèrent également des données de niveau **OFFICIAL**, **OFFICIAL: Sensitive** et **PROTECTED**, soit à partir de leur interaction avec le gouvernement fédéral, soit à part entière.

Le gouvernement australien se focalise de plus en plus sur la politique et la législation sur la protection des données non gouvernementales qui ont un impact majeur sur la sécurité et la prospérité économique de l’Australie. C’est pourquoi les régions d’Australie Azure et la certification CCSL sont adaptées à tous ces secteurs.

![Secteurs d’infrastructure critiques](media/nci-sectors.png)

Les certifications Microsoft montrent que les services Azure ont été soumis à une évaluation rigoureuse et formelle des protections de sécurité en place et qu’ils ont été approuvés pour la gestion de ces données hautement sensibles.

## <a name="location-and-control-of-microsoft-data-centres"></a>Emplacement et contrôle des centres de données Microsoft

Il s’agit d’une exigence obligatoire de l’infrastructure gouvernementale et du gouvernement pour connaître explicitement l’emplacement du centre de données et la propriété des services cloud qui traitent leurs données.  Microsoft, en tant que fournisseur de cloud hyperscale, est en position unique pour fournir des informations complètes sur ces emplacements et propriétés.

Les régions d’Australie de Microsoft Azure (Australie Centre et Australie Centre 2) sont exploitées dans les sites de CDC.  La propriété de CDC est contrôlée à 48 % par le secteur public australien, sous le compte de la Commonwealth Superannuation Corporation, à 48 % par Infratil (un fonds d’actifs d’infrastructure à long terme coté en Nouvelle-Zélande, basé en Nouvelle-Zélande et coté aux bourses australienne et néo-zélandaise) et à 4 % par la direction australienne.  

La direction de CDC a mis en place des assurances contractuelles avec le gouvernement australien, afin de limiter les futurs transferts de propriété et de contrôle. Cette transparence de la chaîne logistique et de la propriété via le partenariat de Microsoft avec CDC, est conforme aux principes de la [Stratégie globale d’hébergement du gouvernement](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) ainsi qu’à la définition d’un **Certified Sovereign Datacentre** (Centre de données souverain certifié).

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>Services Azure inclus dans la certification CCSL actuelle

En juin 2017, l’ACSC a certifié 41 services Azure pour le stockage et le traitement des données au niveau **Unclassified: DLM**. En avril 2018, 24 de ces services étaient certifiés les données classifiées au niveau **PROTECTED**.

La disponibilité des services Azure certifiés par l’ACSC dans l’ensemble des régions Azure Australie est la suivante (ceux en gras sont certifiés au niveau **PROTECTED**) :

|Régions du Centre de l’Australie Azure|Services non régionaux et autres régions|
|---|---|
|Gestion des API, Application Gateway, Services d’application, **Automation,Portail Microsoft Azure, Sauvegarde, Azure Batch, Azure Cloud Services**, Azure Cosmos DB, Azure Event Hubs, **Microsoft Azure ExpressRoute**, Azure HDInsight, **Azure Key Vault**, Azure Load Balancer, analytique des journaux d’activité, **MFA**, Cache Redis Azure, **Azure Resource Manager, Microsoft Azure Service Bus, Microsoft Azure Service Fabric, Azure Site Recovery, Azure SQL Database, Stockage Azure**, Microsoft Azure Traffic Manager, **Machines virtuelles Microsoft Azure, Réseau virtuel Microsoft Azure, passerelle VPN**|**Azure Active Directory**, CDN, Azure Data Catalog, **Import Export, Azure Information Protection, IOT Hub**, Azure Machine Learning, Azure Media Services, **Microsoft Azure Notification Hubs**, Power BI, **Centre de sécurité, Microsoft Azure Scheduler**, Recherche Azure, Azure Stream Analytics|
|

Microsoft publie une [Présentation de la conformité Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf) qui répertorie tous les services dans le cadre de tous les processus de conformité et d’évaluation globaux, gouvernementaux, sectoriels et régionaux, y compris l’IRAP/CCSL.

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Service Azure non répertorié ou évalué à un niveau inférieur à celui dont j’ai besoin

Les services qui ne sont pas certifiés ou qui ont été certifiés au niveau **OFFICIAL: Sensitive**, mais pas **PROTECTED **, peuvent être utilisés avec une solution d’hébergement de données de niveau** PROTECTED**, à condition que ces services :
1. ne stockent ni ne traitent de données de niveau **PROTECTED**, ou
1. que vous ayez effectué une évaluation des risques et approuvé l’utilisation du service pour le stockage des données de niveau **PROTECTED** vous-même.

Si vous souhaitez utiliser un service qui n’est pas inclus dans la certification de la CCSL pour stocker et traiter les données de niveau **OFFICIAL**, vous le pouvez. Cependant, selon l’ISM, vous devez informer l’ACSC par écrit de vos activités, avant d’établir ou de renouveler un contrat avec un fournisseur de services cloud.

La sécurité de tout service utilisé par une agence pour les charges de travail de niveau **PROTECTED** doit toujours être évaluée et approuvée conformément aux processus décrits dans l’ISM et le processus d’évaluation de l’IRAP géré par l’organisme, dans la [DTA Secure Cloud Strategy](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf) (Stratégie de cloud sécurisé DTA).

![Processus de certification de la stratégie de cloud sécurisé DTA](media/certification.png)

Microsoft évalue continuellement ses services pour s’assurer que sa plateforme est sécurisée et adaptée à l’usage des administrations australiennes. Si vous avez besoin d’aide au sujet d’un service qui n’est pas actuellement au niveau **PROTECTED** de la CCSL, veuillez contacter Microsoft.

Étant donné que Microsoft propose une série de services certifiés sur la CCSL pour les classifications **Unclassified DLM**et**PROTECTED**, l’ISM exige que nous effectuions une évaluation IRAP de nos services au moins tous les deux ans. Microsoft effectue une évaluation annuelle. C’est également l’occasion d’inclure des services supplémentaires à prendre en compte.

## <a name="certified-protected-gateway-in-azure"></a>Passerelle certifiée PROTECTED dans Azure

Microsoft ne fait pas appel à une passerelle Internet sécurisée (SIG) certifiée par le gouvernement en raison de restrictions sur le nombre de SIG autorisées dans le cadre du programme de consolidation de passerelle.  Toutefois, les fonctionnalités attendues et nécessaires d’une SIG peuvent être configurées dans Microsoft Azure.

Via la certification **PROTECTED** des services Azure, l’ACSC établit des recommandations spécifiques pour les agences en ce qui concerne la connexion à Azure et l’implémentation de la segmentation du réseau entre les domaines de sécurité, notamment entre le niveau **PROTECTED** et Internet. Ces recommandations incluent l’utilisation de groupes de sécurité réseau (NSG), de pare-feux et de réseaux privés virtuels (VPN).  L’ACSC recommande l’utilisation d’une appliance de passerelle virtuelle. Plusieurs appliances virtuelles disponibles dans Azure disposent d’un équivalent physique dans la liste des produits évalués par l’ASD ou ont été évaluées par rapport aux profils de protection des critères communs et sont répertoriées dans le portail Common Criteria. Ces produits sont mutuellement reconnus par l’ASD dans le cadre de l’accord de reconnaissance des critères communs (CCRA).

Microsoft a publié des recommandations sur l’implémentation des fonctionnalités Azure qui fournissent les fonctions de sécurité requises pour protéger la limite entre les différents domaines de sécurité, qui, lorsqu’ils sont combinés, constituent l’équivalent d’une SIG certifiée. Plusieurs partenaires peuvent vous aider à concevoir et à implémenter ces fonctionnalités. Vous pouvez également utiliser un certain nombre de leurs solutions.

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Habilitations de sécurité et citoyenneté du personnel du service de support Microsoft

Microsoft assure le fonctionnement de ses services grâce à un personnel de sécurité filtré et formé.  Les membres du personnel ayant un accès physique sans escorte aux installations de Sydney et de Melbourne disposent d’une habilitation de sécurité de base du gouvernement australien. Le personnel des régions Australie Centre et Australie Centre 2 a une habilitation minimale de Negative Vetting 1 (NV1) (ceci est requis pour les données de niveau **SECRET**). Ceci fournit aux clients l’assurance supplémentaire que le personnel des centres de données d’Azure est digne de confiance.

Microsoft a une politique d’accès permanent zéro avec un accès accordé par le biais d’un système de juste-à-temps et d’administration « juste assez » basé sur des contrôles d’accès en fonction du rôle. Dans la grande majorité des cas, nos administrateurs n’ont pas besoin d’accès ou de privilèges aux données des clients pour dépanner et maintenir le service.  Les niveaux élevés d’automatisation et de script des tâches pour l’exécution à distance éliminent le besoin d’un accès direct aux données des clients.

L’AGD (Ministère du Procureur général) a confirmé que les politiques et procédures de Microsoft en matière de sécurité du personnel au sein d’Azure sont conformes à l’intention des dispositions sur l’accès à l’information de la PSPF dans INFOSEC-9.

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>Stockage des données de l’ITAR (International Traffic of Arms Regulations) ou de l’EAR (Export Administration Regulations)

Les contrôles techniques d’Azure qui aident les clients à respecter leurs obligations en matière de données contrôlées à l’exportation sont globalement les mêmes pour l’ensemble des régions Azure. Il est important de noter qu’il n’y a pas de critère spécifique pour l’ITAR/EAR, car il n’existe pas de cadre formel d’évaluation et de certification pour les données contrôlées à l’exportation.

Pour Azure Government et Office 365 US Government for Defense, nous avons mis en place des mesures contractuelles et des processus supplémentaires pour assister les clients soumis à des contrôles à l’exportation. Ces clauses contractuelles supplémentaires, la garantie du support et l’administration des régions d’Azure par les États-Unis ne sont implémentées pour l’Australie.

Cela ne signifie pas qu’Azure en Australie ne peut pas être utilisé pour ITAR/EAR, mais vous devez comprendre clairement les restrictions qui vous sont imposées par votre licence d’exportation et vous devez mettre en place des protections supplémentaires pour respecter ces obligations avant d’utiliser Azure pour stocker ces données. Par exemple, vous pourriez avoir besoin de construire la nationalité comme attribut dans Azure Active Directory, d’utiliser Azure Information Protection pour appliquer les règles de cryptage sur les données et les limiter aux seuls États-Unis et à toutes les autres nationalités incluses dans la licence d’exportation, de crypter toutes les données sur place avant de les stocker dans Azure, d’utiliser votre propre clé client ou de conserver votre clé pour les données ITAR, etc.

Comme l’ITAR n’est pas une certification formelle, vous devez comprendre quelles sont les restrictions et les limitations associées à la licence d’exportation et ensuite déterminer s’il y a suffisamment de contrôles dans Azure pour répondre à ces exigences. Dans ce cas, l’une des questions à examiner attentivement concerne l’accès de nos ingénieurs, dont la nationalité ne sera peut être pas approuvée par la licence d’exportation.

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article sur la [passerelle VPN Azure](vpn-gateway.md) pour la configuration et l’implémentation d’une connectivité VPN conforme à l’ISM avec Azure Australie.
