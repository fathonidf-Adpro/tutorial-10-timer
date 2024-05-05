# TUTORIAL 10 ADPRO  (Asynchronous Programming)
#### Daffa Mohamad Fathoni (2206824161)
#### Advanced Programming B / GEN

<hr>

## REFLECTION

> Experiment 1.2: Understanding how it works

![image](https://github.com/fathonidf-Adpro/tutorial-9-grpc-tutorial/assets/105644250/b75d8ecc-a611-4f11-8de1-a9517d0bd30b)

1. **Eksekusi Langsung** `main()`: Saat fungsi `main()` mulai dijalankan, kode di dalamnya dieksekusi secara langsung. Artinya, perintah `println!("Dafton's Computer: hey hey");` dieksekusi langsung sebelum tugas async apapun dijadwalkan atau dijalankan. Itulah mengapa "hey hey" muncul pertama.

2. **Penjadwalan dan Eksekusi Async Task**:
* Setelah itu, fungsi `main()` melanjutkan untuk membuat executor dan spawner, dan memanggil `spawner.spawn` dengan future yang mencetak "howdy!", menunggu selama dua detik, dan kemudian mencetak "done!".
* Task ini kemudian dikirim ke dalam antrian task yang akan dieksekusi oleh executor.
* Executor tidak mulai menjalankan tugas-tugasnya sampai fungsi `main()` memanggil `executor.run()`. Pada saat ini, semua tugas dalam antrian mulai dieksekusi.

3. **Async Nature**:
* spawner.spawn secara asynchronous memasukkan tugas ke dalam antrian dan tidak menunggu penyelesaian tugas tersebut sebelum melanjutkan eksekusi di `main()`.
Hal ini berarti, meskipun task untuk mencetak "howdy!" dan "done!" dijadwalkan sebelum perintah `println!("Dafton's Computer: hey hey");` di `main()` selesai, task tersebut hanya dieksekusi ketika `executor.run()` dipanggil.

>Experiment 1.3: Multiple Spawn and removing drop

Tanpa `drop(spawner);`
![image](https://github.com/fathonidf-Adpro/tutorial-9-grpc-tutorial/assets/105644250/fdbfa1e1-ad14-4b64-b97f-093b36c15b52)

Dengan `drop(spawner);`
![image](https://github.com/fathonidf-Adpro/tutorial-9-grpc-tutorial/assets/105644250/86f26775-c912-4804-8ebe-034fe3d382ac)

Terlihat bahwa program tidak selesai ketika dihapuskan kode `drop(spawner);`. Hal ini karena:
1. **Loop Executor Berkesinambungan**: Executor terus menjalankan loop `while let Ok(task) = self.ready_queue.recv() {...}`, menunggu task baru atau task yang existing yang belum selesai. Jika `ready_queue` tidak pernah mengalami keadaan "kosong" atau `drop(spawner)` tidak dipanggil, executor akan terus menunggu task baru potensial, membuat program tetap berjalan tanpa keluar.

2. **Spawner Tidak Dihapuskan**: Berarti spawner tidak di-drop atau dihancurkan. Karena spawner masih ada, executor mungkin menganggap masih ada kemungkinan menerima task baru, jadi ia tidak keluar dari loop penerimaan task.

Selain itu, pada output "done!" tidak diprint secara berurutan juga. Hal ini dikarenakan:
1. **Concurrency dan Timing Non-deterministik**:
Semua task memulai timer dua detik mereka hampir bersamaan, tetapi ada variasi kecil dalam waktu mulai dan selesai karena overhead sistem.

2. **Pengaruh Executor dan Sistem Operasi**:
Overhead dari locking dan unlocking menggunakan `Mutex` dapat mempengaruhi kapan `future` siap untuk dipoll lagi.
Fungsi `wake` yang dipanggil untuk mengirim task kembali ke antrian mungkin tidak selalu mengirimkannya kembali dalam urutan yang sama.

3. **Non-deterministik dari sync_channel**:
Channel yang digunakan untuk komunikasi antara `spawner` dan `executor` dapat menyebabkan variasi dalam urutan di mana task dijalankan kembali setelah menjadi pending.