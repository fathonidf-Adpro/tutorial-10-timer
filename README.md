# TUTORIAL 10 ADPRO  (Asynchronous Programming)
#### Daffa Mohamad Fathoni (2206824161)
#### Advanced Programming B / GEN

<hr>

## REFLECTION

> Experiment 1.2: Understanding how it works

![image](https://github.com/fathonidf-Adpro/tutorial-9-grpc-tutorial/assets/105644250/b75d8ecc-a611-4f11-8de1-a9517d0bd30b)

1. **Eksekusi Langsung** main(): Saat fungsi main() mulai dijalankan, kode di dalamnya dieksekusi secara langsung. Artinya, perintah println!("Dafton's Computer: hey hey"); dieksekusi langsung sebelum tugas async apapun dijadwalkan atau dijalankan. Itulah mengapa "hey hey" muncul pertama.

2. **Penjadwalan dan Eksekusi Async Task**:
* Setelah itu, fungsi main() melanjutkan untuk membuat executor dan spawner, dan memanggil spawner.spawn dengan future yang mencetak "howdy!", menunggu selama dua detik, dan kemudian mencetak "done!".
* Task ini kemudian dikirim ke dalam antrian task yang akan dieksekusi oleh executor.
* Executor tidak mulai menjalankan tugas-tugasnya sampai fungsi main() memanggil executor.run(). Pada saat ini, semua tugas dalam antrian mulai dieksekusi.

3. **Async Nature**:
* spawner.spawn secara asynchronous memasukkan tugas ke dalam antrian dan tidak menunggu penyelesaian tugas tersebut sebelum melanjutkan eksekusi di main().
Hal ini berarti, meskipun task untuk mencetak "howdy!" dan "done!" dijadwalkan sebelum perintah println!("Dafton's Computer: hey hey"); di main() selesai, task tersebut hanya dieksekusi ketika executor.run() dipanggil.
