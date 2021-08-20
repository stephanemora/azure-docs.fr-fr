---
title: Reconnaissance vocale ouverte pour le russe
titleSuffix: Azure Open Datasets
description: Découvrez comment utiliser le jeu de données Reconnaissance vocale ouverte pour le russe dans Azure Open Datasets.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4c0ec200fa3506dd0b251903954240eff5132dd4
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982507"
---
# <a name="russian-open-speech-to-text"></a>Reconnaissance vocale ouverte pour le russe

Extraits de discours provenant de diverses sources audio. Le jeu de données contient de brefs clips audio en russe.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Ce jeu de données Reconnaissance vocale pour le russe comprend les éléments suivants :
- Environ 16 millions d’énoncés
- Environ 20 000 heures
- 2,3 To (non compressés au format .wav en int16), 356G en opus
- Tous les fichiers ont été convertis en opus, à l’exception des jeux de données de validation

L’objectif principal du jeu de données est d’entraîner des modèles de reconnaissance vocale.

## <a name="dataset-composition"></a>Composition du jeu de données

La taille du jeu de données est indiquée pour les fichiers .wav.

| JEU DE DONNÉES | ÉNONCÉS | HEURES | Go | SECONDES/CARACTÈRES | COMMENT | ANNOTATION | QUALITÉ/BRUIT |
|-|-|-|-|-|-|-|-|
| radio_v4 (*) | 7 603 192 | 10 430 | 1 195 | 5s/68 | Case d’option | Aligner | 95 % / net |
| public_speech (*) | 1 700 060 | 2 709 | 301 | 6s/79 | Discours public | Aligner | 95 % / net |
| audiobook_2 | 1 149 404 | 1 511 | 162 | 5s/56 | Livres | Aligner | 95 % / net |
| radio_2 | 651 645 | 1 439 | 154 | 8s/110 | Case d’option | Aligner | 95 % / net |
| public_youtube1120 | 1 410 979 | 1 104 | 237 | 3s/34 | YouTube | Sous-titres | 95 % / presque net |
| public_youtube700 | 759 483 | 701 | 75 | 3s/43 | YouTube | Sous-titres | 95 % / presque net |
| tts_russian_addresses | 1 741 838 | 754 | 81 | 2s/20 | Adresses | Synthèse vocale 4 voix | 100 %/net |
| asr_public_phone_calls_2 | 603 797 | 601 | 66 | 4s/37 | Appels téléphoniques | ASR | 70 % / bruyant |
| public_youtube1120_hq | 369 245 | 291 | 31 | 3s/37 | HQ YouTube | Sous-titres | 95 % / presque net |
| asr_public_phone_calls_1 | 233 868 | 211 | 23 | 3s/29 | Appels téléphoniques | ASR | 70 % / bruyant |
| radio_v4_add (*) | 92 679 | 157 | 18 | 6s/80 | Case d’option | Aligner | 95 % / net |
| asr_public_stories_2 | 78 186 | 78 | 9 | 4s/43 | Livres | ASR | 80 % / net |
| asr_public_stories_1 | 46 142 | 38 | 4 | 3s/30 | Livres | ASR | 80 % / net |
| public_series_1 | 20 243 | 17 | 2 | 3s/38 | YouTube | Sous-titres | 95 % / presque net |
| asr_calls_2_val | 12 950 | 7 7 | 2 | 2s/34 | Appels téléphoniques | Annotation manuelle | 99 % / net |
| public_lecture_1 | 6 803 | 6 | 1 | 3s/47 | Conférences | Sous-titres | 95 % / net |
| buriy_audiobooks_2_val | 7 850 | 4,9 | 1 | 2s/31 | Livres | Annotation manuelle | 99 % / net |
| public_youtube700_val | 7 311 | 4,5 | 1 | 2s/35 | YouTube | Annotation manuelle | 99 % / net |

(*) Seul un exemple de données est fourni avec les fichiers .txt.

## <a name="annotation-methodology"></a>Méthodologie d’annotation

Le jeu de données est compilé à l’aide de sources ouvertes. Les longues séquences sont divisées en morceaux audio à l’aide de l’alignement et de la détection d’activité vocale. Certains types d’audio sont annotés automatiquement et vérifiés statistiquement à l’aide d’euristiques.

## <a name="data-volumes-and-update-frequency"></a>Volumes de données et fréquence de mise à jour

La taille totale du jeu de données est de 350 Go. La taille totale du jeu de données avec des étiquettes partagées publiquement est de 130 Go.

Il est peu probable que le jeu de données lui-même soit mis à jour pour assurer une compatibilité descendante. Suivez le référentiel d’origine pour les points de référence et les fichiers exclude.

De nouveaux domaines et de nouvelles langues pourront être ajoutés à l’avenir.

## <a name="audio-normalization"></a>Normalisation audio

Tous les fichiers sont normalisés pour faciliter et accélérer les augmentations du runtime. Le traitement est le suivant :

- Convertis en mono, si nécessaire
- Convertis en fréquence d’échantillonnage 16 kHz, si nécessaire
- Stockés sous forme d’entiers 16 bits
- Convertis en OPUS

## <a name="on-disk-db-methodology"></a>Méthodologie de base de données sur disque

Chaque fichier audio (wav, binaire) est haché. Le code de hachage est utilisé pour créer une arborescence des dossiers pour un fonctionnement fs plus optimal.

```python
target_format = 'wav'
wavb = wav.tobytes()

f_hash = hashlib.sha1(wavb).hexdigest()

store_path = Path(root_folder,
                  f_hash[0],
                  f_hash[1:3],
                  f_hash[3:15] + '.' + target_format)
```

## <a name="downloads"></a>Téléchargements

Ce jeu de données est fourni sous deux formes :

- Des archives disponibles via Stockage Blob Azure et/ou des liens directs
- Les fichiers originaux disponibles via Stockage Blob Azure. Le tout est stocké dans « https://azureopendatastorage.blob.core.windows.net/openstt/  »

Structure des dossiers :

```
└── ru_open_stt_opus                                            <= archived folders
│   │
│   ├── archives
│   │    ├── asr_calls_2_val.tar.gz                             <= tar.gz archives with opus and wav files
│   │    │   ...                                                <= see the below table for enumeration
│   │    └── tts_russian_addresses_rhvoice_4voices.tar.gz
│   │
│   └── manifests
│        ├── asr_calls_2_val.csv                                <= csv files with wav_path, text_path, duration (see notebooks)
│        │   ...
│        └── tts_russian_addresses_rhvoice_4voices.csv
│
└── ru_open_stt_opus_unpacked                                   <= a separate folder for each uploaded domain
    ├── public_youtube1120
    │    ├── 0                                                  <= see "On disk DB methodology" for details
    │    ├── 1
    │    │   ├── 00
    │    │   │  ...
    │    │   └── ff
    │    │        ├── *.opus                                   <= actual files
    │    │        └── *.txt
    │    │   ...
    │    └── f
    │
    ├── public_youtube1120_hq
    ├── public_youtube700_val
    ├── asr_calls_2_val
    ├── radio_2
    ├── private_buriy_audiobooks_2
    ├── asr_public_phone_calls_2
    ├── asr_public_stories_2
    ├── asr_public_stories_1
    ├── public_lecture_1
    ├── asr_public_phone_calls_1
    ├── public_series_1
    └── public_youtube700
```

| JEU DE DONNÉES | GO, WAV | GO, ARCHIVE | ARCHIVER | SOURCE | MANIFESTE |
|-|-|-|-|-|-|
| Former |  |  |  |  |  |
| Échantillon radio et discours public | - | 11,4 | opus+txt | - | manifest |
| audiobook_2 | 162 | 25,8 | opus+txt | Internet + alignment | manifest |
| radio_2 | 154 | 24,6 | opus+txt | Case d’option | manifest |
| public_youtube1120 | 237 | 19,0 | opus+txt | Vidéos YouTube | manifest |
| asr_public_phone_calls_2 | 66 | 9,4 | opus+txt | Internet + ASR | manifest |
| public_youtube1120_hq | 31 | 4,9 | opus+txt | Vidéos YouTube | manifest |
| asr_public_stories_2 | 9 | 1.4 | opus+txt | Internet + alignment | manifest |
| tts_russian_addresses_rhvoice_4voices | 80,9 | 12.9 | opus+txt | TTS | manifest |
| public_youtube700 | 75,0 | 12,2 | opus+txt | Vidéos YouTube | manifest |
| asr_public_phone_calls_1 | 22,7 | 3.2 | opus+txt | Internet + ASR | manifest |
| asr_public_stories_1 | 4,1 | 0,7 | opus+txt | Histoires publiques | manifest |
| public_series_1 | 1,9 | 0.3 | opus+txt | Séries publiques | manifest |
| public_lecture_1 | 0,7 | 0,1 | opus+txt | Internet + manuel | manifest |
| Val |  |  |  |  |  |
| asr_calls_2_val | 2 | 0,8 | wav+txt | Internet | manifest |
| buriy_audiobooks_2_val | 1 | 0,5 | wav+txt | Livres + manuel | manifest |
| public_youtube700_val | 2 | 0,13 | wav+txt | Vidéos YouTube + manuel | manifest |

## <a name="download-instructions"></a>Instructions de téléchargement

### <a name="direct-download"></a>Téléchargement direct

Pour savoir comment télécharger directement le jeu de données, consultez la [page d’instructions de téléchargement de GitHub](https://github.com/snakers4/open_stt#download-instructions).

## <a name="additional-information"></a>Informations supplémentaires

Pour obtenir de l’aide ou poser des questions sur les données, contactez les auteurs des données à l’adresse aveysov@gmail.com.

Cette licence permet aux utilisateurs de distribuer, de remixer, d’adapter et d’exploiter le matériel sur n’importe quel support ou dans n’importe quel format, à des fins non commerciales uniquement, et à condition que l’auteur soit mentionné. Elle inclut les éléments suivants :
* BY : Le créateur doit être cité
* NC : Seules les utilisations non commerciales de l’œuvre sont autorisées

CC-BY-NC - Utilisation commerciale disponible après accord avec les auteurs du jeu de données.

## <a name="data-access"></a>Accès aux données

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text -->

> [!TIP]
> **[Téléchargez plutôt le notebook](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text)** .

## <a name="helper-functions--dependencies"></a>Fonctions/dépendances de l’assistance

### <a name="building-libsndfile"></a>Génération de libsndfile

Un moyen efficace de lire les fichiers opus en Python qui n’entraîne pas de surcharge importante consiste à utiliser pysoundfile (un wrapper CFFI Python autour de libsoundfile).

La prise en charge d’opus a été implémenté en amont, mais elle n’a pas été correctement publiée. C’est pourquoi nous avons opté pour une build personnalisée et des MonkeyPatch.

En règle générale, vous devez l’exécuter dans votre interpréteur de commandes avec un accès sudo :

```bash
apt-get update
apt-get install cmake autoconf autogen automake build-essential libasound2-dev \
libflac-dev libogg-dev libtool libvorbis-dev libopus-dev pkg-config -y

cd /usr/local/lib
git clone https://github.com/erikd/libsndfile.git
cd libsndfile
git reset --hard 49b7d61
mkdir -p build && cd build

cmake .. -DBUILD_SHARED_LIBS=ON
make && make install
cmake --build .
```
### <a name="helper-functions--dependencies"></a>Fonctions/dépendances de l’assistance

Installez les bibliothèques suivantes :

```
pandas
numpy
scipy
tqdm
soundfile
librosa
```

Les manifestes sont des fichiers CSV avec les colonnes suivantes :

- Chemin d’accès au fichier audio
- Chemin d’accès au fichier texte
- Duration

Ils se sont avérés être le format le plus simple pour accéder aux données.

Pour faciliter l’utilisation, tous les manifestes sont déjà réenracinés. Tous les chemins d’accès étant relatifs, vous devez fournir un dossier racine.

```python
# manifest utils
import os
import numpy as np
import pandas as pd
from tqdm import tqdm
from urllib.request import urlopen


def reroot_manifest(manifest_df,
                    source_path,
                    target_path):
    if source_path != '':
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: x.replace(source_path,
                                                                              target_path))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: x.replace(source_path,
                                                                                target_path))
    else:
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: os.path.join(target_path, x))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: os.path.join(target_path, x))    
    return manifest_df


def save_manifest(manifest_df,
                  path,
                  domain=False):
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']

    manifest_df.reset_index(drop=True).sort_values(by='duration',
                                                   ascending=True).to_csv(path,
                                                                          sep=',',
                                                                          header=False,
                                                                          index=False)
    return True


def read_manifest(manifest_path,
                  domain=False):
    if domain:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration',
                               'domain'])
    else:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration'])


def check_files(manifest_df,
                domain=False):
    orig_len = len(manifest_df)
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    wav_paths = list(manifest_df.wav_path.values)
    text_path = list(manifest_df.text_path.values)

    omitted_wavs = []
    omitted_txts = []

    for wav_path, text_path in zip(wav_paths, text_path):
        if not os.path.exists(wav_path):
            print('Dropping {}'.format(wav_path))
            omitted_wavs.append(wav_path)
        if not os.path.exists(text_path):
            print('Dropping {}'.format(text_path))
            omitted_txts.append(text_path)

    manifest_df = manifest_df[~manifest_df.wav_path.isin(omitted_wavs)]
    manifest_df = manifest_df[~manifest_df.text_path.isin(omitted_txts)]
    final_len = len(manifest_df)

    if final_len != orig_len:
        print('Removed {} lines'.format(orig_len-final_len))
    return manifest_df


def plain_merge_manifests(manifest_paths,
                          MIN_DURATION=0.1,
                          MAX_DURATION=100):

    manifest_df = pd.concat([read_manifest(_)
                             for _ in manifest_paths])
    manifest_df = check_files(manifest_df)

    manifest_df_fit = manifest_df[(manifest_df.duration>=MIN_DURATION) &
                                  (manifest_df.duration<=MAX_DURATION)]

    manifest_df_non_fit = manifest_df[(manifest_df.duration<MIN_DURATION) |
                                      (manifest_df.duration>MAX_DURATION)]

    print(f'Good hours: {manifest_df_fit.duration.sum() / 3600:.2f}')
    print(f'Bad hours: {manifest_df_non_fit.duration.sum() / 3600:.2f}')

    return manifest_df_fit


def save_txt_file(wav_path, text):
    txt_path = wav_path.replace('.wav','.txt')
    with open(txt_path, "w") as text_file:
        print(text, file=text_file)
    return txt_path


def read_txt_file(text_path):
    #with open(text_path, 'r') as file:
    response = urlopen(text_path)
    file = response.readlines()
    for i in range(len(file)):
        file[i] = file[i].decode('utf8')
    return file 

def create_manifest_from_df(df, domain=False):
    if domain:
        columns = ['wav_path', 'text_path', 'duration', 'domain']
    else:
        columns = ['wav_path', 'text_path', 'duration']
    manifest = df[columns]
    return manifest


def create_txt_files(manifest_df):
    assert 'text' in manifest_df.columns
    assert 'wav_path' in manifest_df.columns
    wav_paths, texts = list(manifest_df['wav_path'].values), list(manifest_df['text'].values)
    # not using multiprocessing for simplicity
    txt_paths = [save_txt_file(*_) for _ in tqdm(zip(wav_paths, texts), total=len(wav_paths))]
    manifest_df['text_path'] = txt_paths
    return manifest_df


def replace_encoded(text):
    text = text.lower()
    if '2' in text:
        text = list(text)
        _text = []
        for i,char in enumerate(text):
            if char=='2':
                try:
                    _text.extend([_text[-1]])
                except:
                    print(''.join(text))
            else:
                _text.extend([char])
        text = ''.join(_text)
    return text
```

```python
# reading opus files
import os
import soundfile as sf



# Fx for soundfile read/write functions
def fx_seek(self, frames, whence=os.SEEK_SET):
    self._check_if_closed()
    position = sf._snd.sf_seek(self._file, frames, whence)
    return position


def fx_get_format_from_filename(file, mode):
    format = ''
    file = getattr(file, 'name', file)
    try:
        format = os.path.splitext(file)[-1][1:]
        format = format.decode('utf-8', 'replace')
    except Exception:
        pass
    if format == 'opus':
        return 'OGG'
    if format.upper() not in sf._formats and 'r' not in mode:
        raise TypeError("No format specified and unable to get format from "
                        "file extension: {0!r}".format(file))
    return format


#sf._snd = sf._ffi.dlopen('/usr/local/lib/libsndfile/build/libsndfile.so.1.0.29')
sf._subtypes['OPUS'] = 0x0064
sf.SoundFile.seek = fx_seek
sf._get_format_from_filename = fx_get_format_from_filename


def read(file, **kwargs):
    return sf.read(file, **kwargs)


def write(file, data, samplerate, **kwargs):
    return sf.write(file, data, samplerate, **kwargs)
```

```python
# display utils
import gc
from IPython.display import HTML, Audio, display_html
pd.set_option('display.max_colwidth', 3000)
#Prepend_path is set to read directly from Azure. To read from local replace below string with path to the downloaded dataset files
prepend_path = 'https://azureopendatastorage.blob.core.windows.net/openstt/ru_open_stt_opus_unpacked/'


def audio_player(audio_path):
    return '<audio preload="none" controls="controls"><source src="{}" type="audio/wav"></audio>'.format(audio_path)

def display_manifest(manifest_df):
    display_df = manifest_df
    display_df['wav'] = [audio_player(prepend_path+path) for path in display_df.wav_path]
    display_df['txt'] = [read_txt_file(prepend_path+path) for path in tqdm(display_df.text_path)]
    audio_style = '<style>audio {height:44px;border:0;padding:0 20px 0px;margin:-10px -20px -20px;}</style>'
    display_df = display_df[['wav','txt', 'duration']]
    display(HTML(audio_style + display_df.to_html(escape=False)))
    del display_df
    gc.collect()
```

## <a name="play-with-a-dataset"></a>Jouer avec un jeu de données

### <a name="play-a-sample-of-files"></a>Lire un échantillon de fichiers

La plupart des navigateurs de plateformes prennent en charge la lecture audio native. Nous pouvons donc utiliser des lecteurs audio HTML5 pour consulter nos données.

```python
manifest_df = read_manifest(prepend_path +'/manifests/public_series_1.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')

```

```python
sample = manifest_df.sample(n=20)
display_manifest(sample)
```

### <a name="read-a-file"></a>Lire un fichier

```python
!ls ru_open_stt_opus/manifests/*.csv
```

Quelques exemples montrant comment lire au mieux les fichiers WAV et opus.

Scipy est le plus rapide pour le format WAV. Pysoundfile est dans l’ensemble le meilleur pour opus.

```python
%matplotlib inline

import librosa
from scipy.io import wavfile
from librosa import display as ldisplay
from matplotlib import pyplot as plt
```

#### <a name="read-a-wav"></a>Lire un WAV

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_calls_2_val.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
from io import BytesIO

wav_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+wav_path)
data = response.read()
sr, wav = wavfile.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

#### <a name="read-opus"></a>Lire opus

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_public_phone_calls_2.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
opus_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+opus_path)
data = response.read()
wav, sr = sf.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

<!-- nbend -->

---

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres jeux de données du [catalogue Open Datasets](dataset-catalog.md).
