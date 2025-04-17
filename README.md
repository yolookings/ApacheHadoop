# `Tugas BigData - Apache Hadoop`

| Nama Lengkap         | NRP        |
| -------------------- | ---------- |
| Maulana Ahmad Zahiri | 5027231010 |

# 1. Apache Hadoop (HDFS)

## Tugas:

- Buat direktori movielens di HDFS.
- Upload file u.data ke direktori tersebut.
- Tampilkan 10 baris pertama dari file.
- Hitung ukuran file di HDFS.

# 2. Apache Pig

## Tugas:

- Load file u.data ke Pig.
- Hitung rata-rata rating per item_id (film).
- Ambil hanya film yang memiliki rating rata-rata ≥ 4.0.
- Simpan hasil akhir ke output/film_favorit.

# 3. Apache Hive

## Tugas:

- Buat database movielens.
- Buat tabel ratings (user_id INT, item_id INT, rating INT, timestamp BIGINT).
- Load data dari file u.data.
- Hitung rata-rata rating setiap film.
- Ambil 10 film dengan rata-rata rating tertinggi.

# Penyelesaian :

# Setting

```bash
docker run -it -p 9870:9870 -p 8088:8088 -p 9864:9864 --name mwlanaz silicoflare/hadoop:arm
```

![setting apache hadoop](/img/run.png)

```bash
init
source ~/.bashrc
```

![setting apache hadoop](/img/init.png)

# Testing Pig and Hive

![setting apache hadoop](/img/testing.png)

# 1. Hadoop

1. Install Unzip

```bash
apt install unzip
```

2. Dapatkan Direktori Movielens

```bash
wget https://files.grouplens.org/datasets/movielens/ml-100k.zip
```

![setting apache hadoop](/img/unduh.png)

3. Unzip File yang masi berupa zip

```bash
unzip ml-100k.zip
```

![setting apache hadoop](/img/unzip.png)

4. Buat Direktori movielens di HDFS

```bash
hdfs dfs -mkdir /movielens
```

```bash
hdfs dfs -put /ml-100k/u.data /movielens/
```

![setting apache hadoop](/img/direktori-movielens.png)
![setting apache hadoop](/img/pindah.png)

5. Menampilkan 10 baris pertama dari file.

```bash
hdfs dfs -cat /movielens/u.data | head -n 10
```

![setting apache hadoop](/img/baris.png)

6. Menampilkan ukuran file di HDFS.

```bash
hdfs dfs -du -h /movielens/u.data
```

![setting apache hadoop](/img/size.png)

# 2. Pig

```bash
# load data
raw_data = LOAD '/movielens/u.data' USING PigStorage('\t')
           AS (user_id:int, item_id:int, rating:int, timestamp:long);

grouped = GROUP raw_data BY item_id;
# hitung rating
avg_rating = FOREACH grouped GENERATE group AS item_id,
             AVG(raw_data.rating) AS avg_rating;
# filter hanya rating >= 4.0
fav_movies = FILTER avg_rating BY avg_rating >= 4.0;

# store ke hdfs
STORE fav_movies INTO '/output/film_favorit' USING PigStorage('\t');

```

1. Menampilkan /output/film_favorit :

```bash
hdfs dfs -ls /output/film_favorit
```

![setting apache hadoop](/img/lihat-film-favorit.png)

2. Menampilkan hanya film yang memilikki rating >= 4.0

```
hdfs dfs -cat /output/film_favorit/part-r-00000
```

![setting apache hadoop](/img/bintang-5.png)

# Hive

```bash
CREATE DATABASE IF NOT EXISTS movielens;
USE movielens;

CREATE TABLE IF NOT EXISTS ratings (
  user_id INT,
  item_id INT,
  rating INT,
  'timestamp' BIGINT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;

LOAD DATA INPATH '/movielens/u.data' INTO TABLE ratings;

SELECT item_id, AVG(rating) AS avg_rating
FROM ratings
GROUP BY item_id
ORDER BY avg_rating DESC
LIMIT 10;
```

1. Buat database movielens.

![setting apache hadoop](/img/create-database.png)

2. Buat tabel ratings (user_id INT, item_id INT, rating INT, timestamp BIGINT), dan Load data dari file u.data, serta menghitung rata2 rating setiap film

![setting apache hadoop](/img/load-ambil.png)

3. Ambil 10 film dengan rata-rata rating tertinggi.

![setting apache hadoop](/img/nilai-tinggi.png)

sekian terimakasih.
