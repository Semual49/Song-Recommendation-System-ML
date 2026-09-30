[README.md](https://github.com/user-attachments/files/32849266/README.md)
# Song Recommendation System

Sistem rekomendasi lagu berbasis konten memakai TF-IDF dan cosine similarity. Dikerjakan oleh Samuel Christopher.

## Isi Repositori

```
.
├── README.md
├── requirements.txt
├── Song_Recommendation.ipynb
└── song_recomendation_B.csv
```

File CSV harus berada di folder yang sama dengan notebook karena dibaca dengan path relatif. Nama file memakai ejaan `recomendation` dan harus dibiarkan apa adanya.

## Dataset

`song_recomendation_B.csv`: 4.999 baris, 19 kolom. Berisi metadata lagu (judul, artis, album, tanggal rilis, genre, popularitas) dan fitur audio (`Danceability`, `Energy`, `Valence`, `Tempo`, `Loudness`, dan lain-lain).

## Alur Notebook

1. **Cleaning**: konversi `Album Release Date` ke datetime, isi nilai kosong (median untuk numerik, modus untuk kategorikal), hapus duplikat berdasarkan `Track Name` dan `Artist Name(s)` dengan menyimpan versi paling populer.
2. **EDA**: histogram popularitas, scatter `Danceability` vs `Energy`, boxplot `Loudness`.
3. **Modeling**: kolom teks gabungan dari judul, artis, dan genre diubah dengan `TfidfVectorizer`. Query pengguna diubah ke vektor yang sama, lalu dibandingkan dengan cosine similarity.
4. **Contoh pemakaian**:
   ```python
   recommend_songs("classic rock Fleetwood Mac")
   recommend_songs("electropop Halsey", top_n=10)
   ```
   Output berupa `Track Name`, `Artist Name(s)`, `Album Name`, dan `Popularity`.

## Catatan Versi Pandas

`requirements.txt` membatasi pandas di bawah versi 3.0. Notebook memakai pola `df[col].fillna(..., inplace=True)` dan `select_dtypes(include='object')`. Di pandas 3.0, pola pertama tidak lagi mengubah DataFrame asli dan pola kedua tidak lagi menangkap kolom string, sehingga nilai kosong tidak terisi dan kolom kategorikal tidak terproses. Jangan naikkan versi pandas kecuali kode diperbarui.

## Keterbatasan

- Rekomendasi hanya memakai kemiripan teks. Fitur audio (`Danceability`, `Energy`, `Valence`, `Tempo`) sudah di-scale tetapi belum dipakai dalam skor.
- Query yang katanya tidak muncul di judul, artis, atau genre (misalnya "chill" atau "movie soundtrack") akan menghasilkan hasil yang kurang relevan.
- Tidak ada metrik evaluasi kuantitatif. Kualitas hasil dinilai manual dari 10 contoh query di notebook.
