---
title: Illumina Platinum Genomes
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Illumina Platinum Genomes dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0a1b9d5a41a0dcc45bea0456c2c3714b787a5af8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284247"
---
# <a name="illumina-platinum-genomes"></a>Illumina Platinum Genomes

Le séquençage de la totalité du génome permet aux chercheurs du monde entier de caractériser le génome humain de manière plus précise et complète. Cela exige de disposer en guise de référence d’un catalogue pangénomique exhaustif de variants d’un niveau de confiance élevé dans une collection de génomes. Illumina a généré des données complètes sur le séquençage du génome entier de 17 individus dans un segment généalogique de trois générations. Illumina a fait appel à des variants dans chaque génome en utilisant un éventail d’algorithmes actuellement disponibles.

Pour plus d’informations sur les données, consultez le [site Illumina](https://www.illumina.com/platinumgenomes.html) officiel.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>Source de données

Ce jeu de données reflète ftp://ussd-ftp.illumina.com/

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

Ce jeu de données contient approximativement 2 Go de données et est mis à jour quotidiennement.

## <a name="storage-location"></a>Emplacement de stockage

Ce jeu de données est stocké dans les régions Azure USA Ouest 2 et USA Centre-Ouest. Nous recommandons de placer les ressources de calcul dans la région USA Ouest 2 ou USA Centre-Ouest à des fins d’affinité.

## <a name="data-access"></a>Accès aux données

USA Ouest 2 : « https://datasetplatinumgenomes.blob.core.windows.net/dataset  »

USA Centre-Ouest : « https://datasetplatinumgenomes-secondary.blob.core.windows.net/dataset  »

[Jeton SAS](../storage/common/storage-sas-overview.md) : sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D

## <a name="use-terms"></a>Conditions d’utilisation

Les données sont disponibles sans restrictions. Pour plus d’informations et des détails sur les citations, consultez le [site Illumina officiel](https://www.illumina.com/platinumgenomes.html).

## <a name="contact"></a>Contact

Si vous avez des questions ou des commentaires sur le jeu de données, contactez platinumgenomes@illumina.com.

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes -->

> [!TIP]
> **[Téléchargez le jeu de données à la place](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-platinum-genomes)** .

## <a name="getting-the-illumina-platinum-genomes-from-azure-open-datasets-and-doing-initial-analysis"></a>Obtention du jeu de données Illumina Platinum Genomes à partir d’Azure Open Datasets et exécution de l’analyse initiale 

Utilisez des notebooks Jupyter, GATK et Picard pour effectuer les opérations suivantes :

1. Annoter des génotypes à l’aide de VariantFiltration
2. Sélectionner des variants spécifiques
3. Filtrer les variants pertinents (ni appel ni région spécifique)
4. Effectuer une analyse de concordance
5. Convertir les fichiers VCF finaux en table 

**Dépendances :**

Ce notebook nécessite les bibliothèques suivantes :

- Stockage Azure `pip install azure-storage-blob`

- numpy `pip install numpy`

- Genome Analysis Toolkit (GATK) (*les utilisateurs doivent télécharger GATK à partir de la page web de l’institut dans le même environnement de calcul avec ce notebook : https://github.com/broadinstitute/gatk/releases* )

**Informations importantes : ce notebook utilise le noyau python 3.6**

## <a name="getting-the-genomics-data-from-azure-open-datasets"></a>Obtention des données génomiques à partir d’Azure Open Datasets

Diverses données génomiques publiques ont été chargées en tant que jeu de données ouvert Azure [ici](https://azure.microsoft.com/services/open-datasets/catalog/). Nous créons un service blob lié à ce jeu de données ouvert. Vous trouverez ci-dessous des exemples de procédures d’appel de données à partir d’un jeu de données ouvert Azure pour des jeux de données `Illumina Platinum Genomes` :

### <a name="downloading-the-specific-illumina-platinum-genomes"></a>Téléchargement du jeu de données « Illumina Platinum Genomes » spécifique

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetplatinumgenomes', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=FFfZ0QaDcnEPQmWsshtpoYOjbzd4jtwIWeK%2Fc4i9MqM%3D')     
blob_service_client.get_blob_to_path('dataset/2017-1.0/hg38/small_variants/NA12877', 'NA12877.vcf.gz', './NA12877.vcf.gz')
```

### <a name="1-annotate-genotypes-using-variantfiltration"></a>1. Annoter les génotypes à l’aide de VariantFiltration

**Remarque importante : vérifiez que votre GATK est en cours d’exécution sur votre système.**

Si vous souhaitez filtrer des génotypes hétérozygotes, nous utilisons l’option `--genotype-filter-expression isHet == 1` de VariantFiltration. Nous pouvons spécifier la valeur d’annotation dont l’outil se sert pour étiqueter les génotypes hétérozygotes avec l’option `--genotype-filter-name`. Ici, la valeur de ce paramètre est définie sur `isHetFilter` . Dans notre premier exemple, nous avons utilisé la valeur `NA12877.vcf.gz` extraite du jeu de données  Illumina Platinum Genomes, mais les utilisateurs peuvent utiliser n’importe quels fichiers VCF d’autres jeux de données :`Platinum Genomes`

```python
run gatk VariantFiltration -V NA12877.vcf.gz -O outputannot.vcf --genotype-filter-expression "isHet == 1" --genotype-filter-name "isHetFilter"
```

### <a name="2-select-specific-variants"></a>2. Sélectionner des variants spécifiques

Sélectionnez un sous-ensemble de variants dans un fichier VCF. Cet outil permet de sélectionner un sous-ensemble de variants en fonction de différents critères afin de faciliter certaines analyses. Parmi ces analyses, citons la comparaison et la mise en contraste des cas et des contrôles, l’extraction de loci variants ou non variants répondant à certains critères, ou la résolution des problèmes liés à certains résultats inattendus.

Il existe de nombreuses options pour sélectionner des sous-ensembles de variants dans un ensemble d’appels plus volumineux :

Extrayez un ou plusieurs échantillons d’un ensemble d’appels en fonction d’un nom d’échantillon complet ou d’une correspondance à un modèle.
Spécifiez des critères d’inclusion qui fixent des seuils à des valeurs d’annotation, **par exemple « DP > 1000 » (profondeur de couverture supérieure à 1 000 fois), « AF < 0.25 » (sites avec une fréquence d’allèle inférieure à 0,25)** . Ces critères sont écrits sous la forme d’« expressions JEXL ». Celles-ci sont documentées dans l’article décrivant l’utilisation des expressions JEXL.
Fournissez des pistes de concordance ou de discordance afin d’inclure ou d’exclure des variants également présents dans d’autres ensembles d’appels donnés.
Sélectionnez des variants en fonction de critères tels que le type (par exemple, INDELs uniquement), la preuve d’une violation des règles de Mendel, l’état de filtrage, l’allélicité, etc. Il existe également plusieurs options pour enregistrer les valeurs d’origine de certaines annotations, qui sont recalculées en cas de sous-définition du nouvel ensemble d’appels, de suppression d’allèles, etc.

Entrée : ensemble d’appels de variant au format VCF dans lequel un sous-ensemble peut être sélectionné.

Sortie : nouveau fichier VCF contenant le sous-ensemble de variants sélectionné.

```python
run gatk SelectVariants -R Homo_sapiens_assembly38.fasta -V outputannot.vcf --select-type-to-include SNP --select-type-to-include INDEL -O selective.vcf
```

### <a name="3-transform-filtered-genotypes-to-no-call"></a>3. Transformer les génotypes filtrés pour les exclure de l’appel 

L’exécution de SelectVariants avec l’indicateur --set-filtred-gt-to-nocall permet de transformer davantage les génotypes avec indicateur à l’aide d’un appel de génotype null. 

Cette conversion est nécessaire parce que les outils en aval n’analysent pas le champ de filtre au niveau du FORMAT.

Comment filtrer les variantes avec **« No call »**

```python
run gatk SelectVariants -V outputannot.vcf --set-filtered-gt-to-nocall -O outputnocall.vcf
```

### <a name="4-check-the-concordance-of-vcf-file-with-ground-truth"></a>4. Vérifier la concordance du fichier VCF avec la réalité

Évaluez la concordance au niveau du site d’un VCF entré par rapport à un VCF réel.
Cet outil évalue deux ensembles d’appels de variants en les comparant, et produit une table de métriques récapitulatives sur six colonnes. 

**Cette fonction effectue les opérations suivantes :**

1. Stratifie les appels SNP et INDEL
2. Signale les appels vrais positifs, faux positifs et faux négatifs
3. Calcule la sensibilité et la précision

L’outil suppose que tous les enregistrements dans le VCF réel (--truth) des variants réels passant. Pour le VCF évalué (-eval), l’outil utilise uniquement des appels passant non filtrés.

Il est éventuellement possible de paramétrer l’outil pour produire des VCF des enregistrements de variants suivants, annotés avec l’état de concordance de chaque variant :

Vrais positifs et faux négatifs (c’est-à-dire, tous les variants dans le VCF réel) : utiles pour le calcul de la sensibilité

Vrais positifs et faux positifs (c’est-à-dire, tous les variants dans le VCF évalué) : utiles pour l’obtention d’un jeu de données d’apprentissage pour les classifieurs d’artefacts de Machine Learning

**Ces VCF de sortie peuvent être passés à VariantsToTable pour produire un fichier TSV en vue d’une analyse statistique en R ou Python.**

```python
 run gatk Concordance -R Homo_sapiens_assembly38.fasta -eval outputannot.vcf --truth outputnocall.vcf  --summary summary.tsv 
```

### <a name="5-variantstotable"></a>5. VariantsToTable

Extrayez des champs d’un fichier VCF vers une table délimitée par des tabulations. Cet outil extrait les champs spécifiés pour chaque variant dans un fichier VCF vers une table délimitée par des tabulations, qui peut être plus facile à utiliser qu’un fichier VCF. Par défaut, l’outil extrait uniquement les variants passant ou non filtrés dans le fichier VCF. Il est possible d’inclure des variants filtrés dans la sortie en ajoutant l’indicateur --show-filtred. L’outil peut extraire tant les champs INFO (au niveau du site) que les champs FORMAT (au niveau de l’échantillon).

Champs INFO/au niveau du site :

Utilisez l’argument `-F` pour extraire les champs INFO. Chaque champ occupera une seule colonne dans le fichier de sortie. Le champ peut être n’importe quelle colonne VCF standard (par exemple, CHROM, ID, QUAL) ou n’importe quel nom d’annotation dans le champ INFO (par exemple, AC, AF). L’outil prend également en charge les champs suivants :

EVENTLENGTH (longueur de l’événement) TRANSITION (1 pour une transition biallélique (SNP), 0 pour une transversion biallélique (SNP), -1 pour INDELS et multialléliques), HET (nombre de génotypes hétérozygotes), HOM-REF (nombre de génotypes de référence homozygotes), HOM-VAR (nombre de génotypes variants homozygotes), NO-CALL (nombre de génotypes no-call), TYPE (type de variant, NO_VARIATION, SNP, MNP, INDEL, SYMBOLIC), MIXED VAR (nombre de génotypes non-reference), NSAMPLES (nombre d’échantillons), NCALLED (nombre d’échantillons appelés), MULTI-ALLELIC (variant multiallélique ? vrai/faux)

Champs FORMAT/au niveau de l’échantillon :

Utilisez l’argument `-GF` pour extraire les champs FORMAT/au niveau de l’échantillon. L’outil crée une colonne par échantillon, nommée « NOM_ÉCHANTILLON.NOM_CHAMP_FORMAT », par exemple, NA12877.GQ, NA12878.GQ.

Entrée :

Fichier VCF à convertir en table

Sortie :

Fichier délimité par des tabulations contenant les valeurs des champs demandés dans le fichier VCF.

```python
run gatk VariantsToTable -V NA12877.vcf.gz -F CHROM -F POS -F TYPE -F AC -F AD -F AF -GF DP -GF AD -O outputtable.table
```

## <a name="references"></a>Références

1. VariantFiltration : https://gatk.broadinstitute.org/hc/en-us/articles/360036827111-VariantFiltration 
2. Sélectionner des variantes : https://gatk.broadinstitute.org/hc/en-us/articles/360037052272-SelectVariants
3. Concordance : https://gatk.broadinstitute.org/hc/en-us/articles/360041851651-Concordance
4. Variants à la table : https://gatk.broadinstitute.org/hc/en-us/articles/360036882811-VariantsToTable 
5. Illumina Platinum Genomes : https://www.illumina.com/platinumgenomes.html 

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).