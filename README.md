# trwiki-67 dataset

<a href="https://doi.org/10.5281/zenodo.5205683"><img src="https://zenodo.org/badge/DOI/10.5281/zenodo.5205683.svg" alt="DOI"></a>

__trwiki-67__ is a language modeling dataset that contain 67 million words of raw wikipedia articles. It can be utilized as a benchmark for different language modeling tasks on character, subword, or word level.

This dataset was extracted from a Turkish wikipedia [dump](https://dumps.wikimedia.org/trwiki/) on 20 July 2021.

## Download

The dataset is hosted on [Zenodo](https://zenodo.org/), it can be downloaded using the following:

```bash
wget -O trwiki-67.tar.bz2 https://zenodo.org/record/5205683/files/trwiki-67.tar.bz2?download=1
tar -xf trwiki-67.tar.bz2
rm trwiki-67.tar.bz2
```

## Preprocessing

All lists and tables were removed from the articles, and the initial extraction from .xml dump was done using [wikiextractor](https://github.com/attardi/wikiextractor/):

```shell
pip install wikiextractor
python -m wikiextractor.WikiExtractor trwiki-20210720-pages-articles-multistream.xml.bz2 \
       --json \
       --processes 16 \
       --no-templates \
       --output trwiki \
       --bytes 100M \
       --compress
```

Additionally, further preprocessing was applied to get rid of redundant text. Only raw text with the titles of the articles were kept in their cased format (with no upper/lower case transformations). 

Article titles were included in text using this format `== TITLE ==`. And articles are seperated by empty lines.

Raw example:

```
== NGC 1710 == 

NGC 1710, Yeni Genel Katalog'da yer alan bir galaksidir. Gökyüzünde Aslan takımyıldızı yönünde bulunur. E-S0 tipi bir merceksi, eliptik galaksidir. Amerikan astronom Francis Leavenworth tarafından 1885 yılında 66,04 cm (26 inç) çaplı mercekli tip bir teleskopla keşfedilmiştir.

== Şenol Gürşan == 

Şenol Gürşan, (d. 17 Ekim 1964, Pınarhisar, Kırklareli) Türk avukat ve siyasetçi.
İstanbul Üniversitesi Hukuk Fakültesi'ni bitirmiş ve serbest avukat olarak çalışmıştır. Kırklareli İlim Yayma Cemiyeti Kuruculuğu ve Başkanlığı görevlerinde bulunmuştur.
2009 yılında Adalet ve Kalkınma Partisi Kırklareli il yönetim kurulu üyesi olmuş, TBMM 24. dönem AK Parti Kırklareli milletvekili, Türkiye-Polonya Dostluk Grubu Başkanı ve TBMM KİT Komisyonu Sözcüsü olmuştur. Gelecek Partisi Kurucular Kurulu üyesi olup aynı zamanda partinin genel sekreteridir.
İyi düzeyde Almanca bilen Gürşan, evli ve 2 çocuk babasıdır.

== Bovenau == 

Bovenau Almanya'nın kuzeyinde Schleswig-Holstein eyaletinde, Rendsburg-Eckernförde iline bağlı bölge. Rendsburg'un 10 km. doğusunda yer alır. 26,2 km² yüzölçümüne sahiptir. Nüfusu, 17 Temmuz 2006 itibarıyla yaklaşık 1098 olarak tespit edilmiştir. Belediye Başkanlığı Jürgen Liebsch tarafından yürütülmektedir.
```

## Tokenization

We train a sentencepiece tokenizer using [tokenizers](https://huggingface.co/docs/tokenizers/python/latest/index.html) with 32K vocabulary size.

Tokenized example:

```
▁alper ▁narman <EOS>
▁alper ▁narman ▁ , ▁türk ▁şarkı ▁yazarı ▁ . ▁narman ▁ , ▁bugün e ▁kadar ▁hande ▁yener ▁ , ▁simge ▁sağ ın ▁ , ▁ayşe ▁hatun ▁önal ▁ , ▁ e dis ▁ , ▁ emel ▁ müftüoğlu ▁ , ▁hadise ▁ , ▁ i zel ▁ve ▁ışın ▁karaca ▁gibi ▁pek ▁çok ▁türk ▁pop ▁müziği ▁şarkıcısı yla ▁çalıştı ▁ . ▁ 2 ▁ 0 ▁ 0 ▁ 0 ▁ ' ▁ lerde ▁birçok ▁eserini ▁ fettah ▁can ▁ile ▁birlikte ▁yazı p ▁besteler ken ▁ 2 ▁ 0 ▁ 1 ▁ 0 ▁ ' ▁ larda ▁onur r ▁ile ▁birlikte ▁ortak ▁ işlere ▁imza ▁attı <EOS>
<EOS>
▁namı k ▁kemal ▁ , ▁ nizi p <EOS>
▁namı k ▁kemal ▁ , ▁gaziantep ▁ilinin ▁ nizi p ▁ilçesine ▁bağlı ▁bir ▁mahalle dir <EOS>
<EOS>
```

## Details

This dataset is split based on the number of articles for each split. In the table below you can find some statistics:

|                                 | train  | val   | test  | total  |
|---------------------------------|--------|-------|-------|--------|
| # of Articles                   | 374123 | 10000 | 10000 | 394123 |
| Raw text size                   | 508MB  | 15MB  | 14MB  | 536MB  |
| Total # of words                | 63.5M  | 1.7M  | 1.7M  | 67M    |
| Avg. # of words per article     | 198    | 210   | 200   | 198    |
| Avg. # of sentences per article | 12.8   | 13.3  | 12.9  | 12.8   |

After setting min frequency to 3, number of unique words is ~644K

## Splitting articles

To seperate articles with their titles you can use this snippet: 

```python
import re

with open("trwiki-67.test.txt") as fi:
    content = fi.read()
    articles = [ { "title": ti.strip("==").strip(), "text": tx.strip()} for ti, tx in zip(re.findall("== .* ==", content), re.split("== .* == \n\n", content)[1:]) ]
```

## Citation and Contact

```
@dataset{ali_safaya_2021_5205683,
  author       = {Ali Safaya},
  title        = {trwiki-67},
  month        = aug,
  year         = 2021,
  publisher    = {Zenodo},
  version      = {1.1},
  doi          = {10.5281/zenodo.5205683},
  url          = {https://doi.org/10.5281/zenodo.5205683}
}
```

Ali Safaya (alisafaya at gmail dot com).
